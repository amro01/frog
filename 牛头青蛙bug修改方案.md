### 核心问题分析：
1.  **死机/崩溃原因（致命Bug）**：原程序是基于**整数网格**设计的。当你设置移动速度为 `0.5` 时，`player.gridY` 变成了 `8.5`。在 `update` 函数中，代码执行 `mapLayout[player.gridY]`，由于数组索引不能是小数，导致获取到 `undefined`，随后读取 `undefined.type` 导致程序崩溃。
2.  **牛角位置**：坐标系偏移计算未同步 `heightOffset`（方块的高度位移）。
3.  **受身逻辑**：目前的逻辑是基于冷却时间（Cooldown），而不是次数限制。

### 设计优化建议：
为了突出“速度慢”又不破坏网格对齐逻辑，我将**速度慢**改为**移动冷却时间**。即：牛头青蛙每次跳跃后，必须等待一小段时间（如 400ms）才能进行下一次跳跃。这样既维持了网格逻辑，又让玩家感到角色“沉重”。

### 修复后的代码片段（请替换对应的部分）：

#### 1. 修改角色数据定义 (CHARACTERS)
增加了 `moveDelay` (移动冷却) 和 `dodgeCharges` (受身次数)。

```javascript
const CHARACTERS = {
    NORMAL: {
        name: '普通青蛙',
        color: '#00ff00',
        moveSpeed: { horizontal: 1, vertical: 1 }, // 必须为整数
        moveDelay: 100, // 移动后的硬直时间(ms)
        abilities: [],
        description: '标准的青蛙，平衡的移动速度'
    },
    BULL_FROG: {
        name: '牛头青蛙',
        color: '#00ff00',
        moveSpeed: { horizontal: 1, vertical: 1 }, // 维持整数移动
        moveDelay: 450, // 增加硬直，表现“速度慢”
        abilities: ['dodge', 'extended_unstable'],
        description: '移动缓慢, 拥有1次受身机会',
        dodgeCharges: 1, // 只有1次机会
        unstableTimeMultiplier: 2.0
    }
};

// 记录当前角色的动态状态
let charState = {
    lastMoveTime: 0,
    dodgeUsed: false
};
```

#### 2. 修复牛角位置 (drawCharacter)
调整了 Y 轴坐标计算，使其锁定在方块顶部。

```javascript
function drawCharacter(x, y, w, h, depth, color, eyeStyle = 'none', characterType = 'NORMAL') {
    // 1. 绘制基础青蛙身体
    drawCube(x, y, w, h, depth, color, eyeStyle);
    
    // 2. 如果是牛头青蛙，绘制头顶牛角
    if (characterType === 'BULL_FROG') {
        ctx.save();
        ctx.translate(VIEW_OFFSET_X, VIEW_OFFSET_Y);
        
        // y 是方块底部的坐标，y - 10(heightOffset) 是方块顶部的坐标
        const topY = y - 10; 
        const hornColor = '#654321';
        
        // 左角
        ctx.fillStyle = hornColor;
        ctx.beginPath();
        ctx.moveTo(x + 5, topY);
        ctx.lineTo(x - 2, topY - 12); // 向左上方延伸
        ctx.lineTo(x + 10, topY);
        ctx.fill();

        // 右角
        ctx.beginPath();
        ctx.moveTo(x + w - 5, topY);
        ctx.lineTo(x + w + 2, topY - 12); // 向右上方延伸
        ctx.lineTo(x + w - 10, topY);
        ctx.fill();
        
        ctx.restore();
    }
}
```

#### 3. 修复移动崩溃逻辑 (handleInput)
加入时间戳检查，防止非法索引产生。

```javascript
function handleInput(key) {
    if (gameState !== 'PLAYING' || isLevelTransitioning) return;
    
    const now = Date.now();
    const charBase = CHARACTERS[currentCharacter];

    // 检查移动冷却（表现速度慢）
    if (now - charState.lastMoveTime < charBase.moveDelay) return;

    charState.lastMoveTime = now;
    player.isSleeping = false;
    
    // 必须确保移动是整数
    switch(key) {
        case 'ArrowUp':
            if (player.gridY > 0) {
                player.gridY -= 1;
                player.lastMoveType = 'vertical';
            }
            break;
        case 'ArrowDown':
            if (player.gridY < ROWS - 1) {
                player.gridY += 1;
                player.lastMoveType = 'vertical';
            }
            break;
        case 'ArrowLeft':
            if (player.gridX > 0) {
                player.gridX -= 1;
                player.lastMoveType = 'horizontal';
            }
            break;
        case 'ArrowRight':
            if (player.gridX < COLS - 1) {
                player.gridX += 1;
                player.lastMoveType = 'horizontal';
            }
            break;
    }
}
```

#### 4. 修复只能受身一次的逻辑 (checkDodgeAbility)

```javascript
function checkDodgeAbility(obs) {
    if (currentCharacter !== 'BULL_FROG') return false;
    // 检查是否已经用过了受身
    if (charState.dodgeUsed) return false;
    
    const pPxX = player.gridX * TILE_SIZE;
    const pHitBox = { x: pPxX + 12, w: TILE_SIZE - 24 };
    const cHitBox = { x: obs.x + 4, w: obs.width - 8 };
    
    // 检测碰撞
    if (isColliding(pHitBox.x, cHitBox.x, pHitBox.w, cHitBox.w)) {
        // 触发受身：将玩家弹开到安全的一格，或者原地闪烁
        charState.dodgeUsed = true; // 标记为已使用
        
        // 视觉效果
        let px = player.gridX * TILE_SIZE + TILE_SIZE/2;
        let py = player.gridY * TILE_SIZE + TILE_SIZE/2;
        spawnParticles(px, py, '#FFD700', 20, 'shatter'); // 金色碎片效果
        
        // 受身操作：跳回上一行或者原地无敌一闪
        player.gridY = Math.min(ROWS - 1, player.gridY + 1); // 紧急后退一行
        
        showNotification("受身成功！(机会已用完)", "info");
        return true; // 抵消死亡
    }
    
    return false;
}
```

#### 5. 初始化的修改
确保每次重新开始游戏或切换关卡时，受身次数重置。

```javascript
function initLevel(resetPlayer = true) {
    // ... 原有逻辑
    if (resetPlayer) {
        charState.dodgeUsed = false; // 重置受身次数
        charState.lastMoveTime = 0;
        // ...
    }
    // ...
}
```

### 总结修复内容：
1.  **牛角位置**：现在通过 `topY = y - 10` 准确锁定了方块顶部，并改成了尖角形状，视觉效果更硬朗。
2.  **崩溃修复**：将移动逻辑改回整数（`1` 格），通过 `moveDelay` (450ms) 实现牛头青蛙的“笨重感”。这彻底解决了因 `gridY` 出现小数导致的死机问题。
3.  **受身限制**：引入了 `charState.dodgeUsed` 变量，确保整局游戏（或每个关卡，视你需求而定）只能触发一次受身。

你可以直接将这些函数替换到你的原代码中，即可正常运行。