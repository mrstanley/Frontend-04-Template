# 学习笔记

## 三子棋

--------------------------------

### 一、 棋盘的表示

1. 二位数组或一维数组表示棋盘。

    ```js
    // 二维数组
    const pattern = [
        [0,0,0],
        [0,0,0],
        [0,0,0]
    ];

    // 一维数组
    const pattern = [
        0, 0, 0,
        0, 0, 0,
        0, 0, 0
    ]
    ```

2. 生成棋盘 循环创建棋盘方格

    ```css
    /*解决棋盘格间隙问题*/
    .cell {
        display: inline-block;
        vertical-aline: middle;  /*默认：baseline*/
    }
    ```

    ```js
    function show() {
        const board = document.getElementById('board');
        board.innerHTML = '';
        for (let i = 0; i < pattern.length; i++) {
            for (let j = 0; j < pattern[i].length; j++) {
                const cell = document.createElement('div');
                cell.classList.add('cell')
                const value = pattern[i][j];
                cell.innerText =
                    value === 2 ? '✔' :
                    value === 1 ? '✘' : '';
                cell.addEventListener('click', () => move(i, j));
                board.appendChild(cell);
            }
            board.appendChild(document.createElement('br'));
        }
    }
    ```

### 二、落子判断胜负

1. 棋子的表示：交替落子 通过满足交换律的 3-1 = 2 和 3-2=1 来表示落子双方，监听棋盘格点击事件落子。

    ```js
    // 当前落子方 1表示 √ 2表示 ×
    let type = 1;
    // 下一次落子的一方
    type = 3 - type
    ```

2. 监听点击事件落子

    ```js
    function move(y, x) {
        // 如果已落子
        if (pattern[y][x]) return;
        pattern[y][x] = type;
        if (checkWin(pattern, type)) {
            (function (type) {
                setTimeout(() => {
                    alert(`${type === 2 ? '✔' : '✘'} 赢了`);
                }, 100);
            })(type)
        }
        type = 3 - type;
        show();
        if (willWin(pattern, type)) {
            console.log(`${type === 2 ? '✔' : '✘'} 要赢了`)
        }
    }
    ```

3. 判断胜负（三横行、三数列、交叉）是否有跟当前落子不一样，如果没有则表示赢

    ```js
    function checkWin(pattern, type) {
        for (let i = 0; i < pattern.length; i++) {
            let win = true;
            for (let j = 0; j < pattern[i].length; j++) {
                if (pattern[i][j] !== type) {
                    win = false;
                }
            }
            if (win) {
                return true;
            }
        }
        for (let i = 0; i < pattern[0].length; i++) {
            let win = true;
            for (let j = 0; j < pattern.length; j++) {
                if (pattern[j][i] !== type) {
                    win = false;
                }
            }
            if (win) {
                return true;
            }
        }
        // 花括号局部变量 用于 反复声明和赋值 技巧 tips
        // 斜线的表示 x = y 相等 或者 x, 2-x
        {
            let win = true;
            for (let i = 0; i < pattern.length; i++) {
                if (pattern[i][i] !== type) {
                    win = false;
                }
            }
            if (win) {
                return true;
            }
        }
        {
            let win = true;
            for (let i = 0; i < pattern.length; i++) {
                if (pattern[i][2 - i] !== type) {
                    win = false;
                }
            }
            if (win) {
                return true;
            }
        }
        return false;
    }
    ```

### 三、AI能力

1. 初步AI能力，克隆当前棋盘， 检查下一步是是否可能赢，循环下一步的所有可能性，

    ```js
    // 首先克隆当前棋盘
    function deepClone(parttern) {
        return JSON.parse(JSON.stringify(parttern));
    }
    // 检查下一步是是否可能赢 循环下一步的所有可能性
    function willWin(pattern, type) {
        for (let i = 0; i < pattern.length; i++) {
            for (let j = 0; j < pattern[i].length; j++) {
                if (pattern[i][j])
                    continue;
                const current = deepClone(pattern);
                current[i][j] = type;
                if (checkWin(current, type))
                    return true;
            }
        }
        return false;
    }
    ```

2. 完美AI能力
