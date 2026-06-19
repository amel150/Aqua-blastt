<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌊 Aqua Blast</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
        }

        body {
            margin: 0;
            min-height: 100vh;
            background: linear-gradient(145deg, #0a2f44 0%, #1a6a8a 50%, #2d9bb9 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Arial', sans-serif;
            overflow: hidden;
            position: relative;
        }

        /* Bubbles background */
        .bubble {
            position: absolute;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.08);
            box-shadow: inset 0 0 20px rgba(255, 255, 255, 0.2);
            animation: float linear infinite;
            pointer-events: none;
        }

        @keyframes float {
            0% {
                transform: translateY(100vh) scale(0.5);
                opacity: 0.3;
            }
            100% {
                transform: translateY(-10vh) scale(1.2);
                opacity: 0;
            }
        }

        #game-container {
            background: rgba(255, 255, 255, 0.08);
            backdrop-filter: blur(6px);
            border-radius: 40px;
            padding: 30px 35px 35px 35px;
            box-shadow: 0 20px 60px rgba(0, 20, 30, 0.7), inset 0 1px 0 rgba(255, 255, 255, 0.15);
            border: 1px solid rgba(255, 255, 255, 0.1);
            position: relative;
            z-index: 10;
            max-width: 520px;
            width: 100%;
            transition: all 0.2s;
        }

        #header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 18px;
            padding: 0 8px;
        }

        #header h1 {
            margin: 0;
            font-size: 28px;
            font-weight: 700;
            color: #e6f7ff;
            text-shadow: 0 2px 12px rgba(0, 40, 60, 0.6), 0 0 30px rgba(0, 180, 255, 0.2);
            letter-spacing: 1px;
        }

        #header h1 span {
            display: inline-block;
            animation: wave 2s infinite ease-in-out;
        }

        @keyframes wave {
            0%,
            100% {
                transform: rotate(0deg);
            }
            50% {
                transform: rotate(12deg);
            }
        }

        #score-box {
            background: rgba(0, 30, 50, 0.5);
            padding: 8px 20px;
            border-radius: 40px;
            border: 1px solid rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(4px);
        }

        #score-box label {
            color: #b0dff0;
            font-size: 14px;
            font-weight: 500;
            letter-spacing: 0.5px;
        }

        #score-value {
            color: #ffffff;
            font-size: 26px;
            font-weight: 700;
            margin-left: 8px;
            text-shadow: 0 0 20px rgba(0, 200, 255, 0.4);
        }

        #gridCanvas {
            display: block;
            margin: 0 auto;
            width: 100%;
            height: auto;
            aspect-ratio: 1/1;
            background: rgba(0, 30, 50, 0.4);
            border-radius: 24px;
            box-shadow: inset 0 0 40px rgba(0, 0, 0, 0.3), 0 8px 32px rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.08);
            cursor: pointer;
            touch-action: none;
        }

        #blocks-panel {
            display: flex;
            justify-content: space-around;
            gap: 12px;
            margin-top: 20px;
            margin-bottom: 16px;
            padding: 8px 4px;
        }

        .block-slot {
            flex: 1;
            aspect-ratio: 1/1;
            max-width: 120px;
            background: rgba(0, 30, 50, 0.35);
            border-radius: 20px;
            border: 2px solid rgba(255, 255, 255, 0.06);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
            cursor: pointer;
            box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
            position: relative;
        }

        .block-slot:hover {
            background: rgba(0, 60, 90, 0.5);
            transform: scale(1.02);
            border-color: rgba(255, 255, 255, 0.15);
        }

        .block-slot.active {
            border-color: #66ddff;
            box-shadow: 0 0 25px rgba(0, 200, 255, 0.5), inset 0 0 25px rgba(0, 200, 255, 0.1);
            background: rgba(0, 80, 120, 0.5);
            transform: scale(1.04);
        }

        .block-slot canvas {
            width: 100%;
            height: 100%;
            display: block;
            border-radius: 16px;
        }

        .block-slot .empty-label {
            color: rgba(255, 255, 255, 0.2);
            font-size: 12px;
            font-weight: 300;
            letter-spacing: 1px;
            position: absolute;
            pointer-events: none;
        }

        #bottom-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 6px;
            padding: 0 6px;
        }

        #restartBtn {
            background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.12);
            color: #cceeff;
            padding: 10px 28px;
            border-radius: 40px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.25s;
            backdrop-filter: blur(4px);
            letter-spacing: 0.5px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        #restartBtn:hover {
            background: rgba(255, 255, 255, 0.12);
            border-color: rgba(255, 255, 255, 0.25);
            transform: scale(1.03);
            box-shadow: 0 0 30px rgba(0, 200, 255, 0.15);
        }

        #restartBtn:active {
            transform: scale(0.96);
        }

        #game-over-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 15, 30, 0.75);
            backdrop-filter: blur(6px);
            border-radius: 40px;
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 20;
            color: white;
            padding: 30px;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        #game-over-overlay.show {
            display: flex;
        }

        #game-over-overlay h2 {
            font-size: 38px;
            margin: 0 0 8px 0;
            color: #8ae2ff;
            text-shadow: 0 0 40px rgba(0, 200, 255, 0.4);
            font-weight: 700;
        }

        #game-over-overlay p {
            font-size: 18px;
            color: #b0dff0;
            margin: 4px 0;
        }

        #game-over-overlay .final-score {
            font-size: 52px;
            font-weight: 800;
            color: #ffffff;
            margin: 10px 0 20px;
            text-shadow: 0 0 40px rgba(0, 200, 255, 0.3);
        }

        #game-over-overlay button {
            background: rgba(255, 255, 255, 0.12);
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: #e6f7ff;
            padding: 14px 44px;
            border-radius: 60px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.25s;
            margin-top: 10px;
        }

        #game-over-overlay button:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05);
        }

        @media (max-width: 480px) {
            #game-container {
                padding: 16px 16px 20px 16px;
                border-radius: 28px;
                margin: 10px;
            }
            #header h1 {
                font-size: 20px;
            }
            #score-box label {
                font-size: 12px;
            }
            #score-value {
                font-size: 20px;
            }
            #blocks-panel {
                gap: 8px;
                margin-top: 14px;
            }
            .block-slot {
                max-width: 90px;
            }
            #restartBtn {
                padding: 8px 18px;
                font-size: 13px;
            }
            #game-over-overlay h2 {
                font-size: 28px;
            }
            #game-over-overlay .final-score {
                font-size: 38px;
            }
        }
    </style>
</head>
<body>

    <!-- Bubbles -->
    <div id="bubbles-container"></div>

    <div id="game-container">
        <!-- Game Over Overlay -->
        <div id="game-over-overlay">
            <h2>🌊 Game Over</h2>
            <p>Skor Akhir</p>
            <div class="final-score" id="final-score">0</div>
            <button id="restartOverlayBtn">🔄 Main Lagi</button>
        </div>

        <!-- Header -->
        <div id="header">
            <h1><span>🌊</span> Aqua Blast</h1>
            <div id="score-box">
                <label>🏆</label>
                <span id="score-value">0</span>
            </div>
        </div>

        <!-- Grid -->
        <canvas id="gridCanvas" width="400" height="400"></canvas>

        <!-- Blocks -->
        <div id="blocks-panel">
            <div class="block-slot" data-index="0">
                <canvas width="120" height="120"></canvas>
                <span class="empty-label">—</span>
            </div>
            <div class="block-slot" data-index="1">
                <canvas width="120" height="120"></canvas>
                <span class="empty-label">—</span>
            </div>
            <div class="block-slot" data-index="2">
                <canvas width="120" height="120"></canvas>
                <span class="empty-label">—</span>
            </div>
        </div>

        <!-- Bottom -->
        <div id="bottom-bar">
            <div style="color:rgba(255,255,255,0.2); font-size:13px;">⋮⋮⋮</div>
            <button id="restartBtn">🔄 Restart</button>
        </div>
    </div>

    <script>
        // ─── CONFIG ───────────────────────────────────────────────
        const GRID_SIZE = 8;
        const CELL_SIZE = 45;
        const PADDING = 15;
        const CANVAS_SIZE = 400;

        const COLORS = [
            '#3aa8d6', '#4db8e8', '#66ccff', '#7ad0f0',
            '#2d9bb9', '#5ab8d9', '#85d4f0', '#a8e0f5'
        ];

        // ─── SHAPES ──────────────────────────────────────────────
        const SHAPES_LIST = [
            { name: 'I4', matrix: [
                    [1, 1, 1, 1]
                ] },
            { name: 'O2', matrix: [
                    [1, 1],
                    [1, 1]
                ] },
            { name: 'T', matrix: [
                    [0, 1, 0],
                    [1, 1, 1]
                ] },
            { name: 'L', matrix: [
                    [1, 0],
                    [1, 0],
                    [1, 1]
                ] },
            { name: 'J', matrix: [
                    [0, 1],
                    [0, 1],
                    [1, 1]
                ] },
            { name: 'S', matrix: [
                    [0, 1, 1],
                    [1, 1, 0]
                ] },
            { name: 'Z', matrix: [
                    [1, 1, 0],
                    [0, 1, 1]
                ] },
            { name: 'I3', matrix: [
                    [1, 1, 1]
                ] },
            { name: 'I2', matrix: [
                    [1, 1]
                ] },
            { name: 'DOT', matrix: [
                    [1]
                ] },
            { name: 'L2', matrix: [
                    [1, 1],
                    [1, 0]
                ] },
            { name: 'L3', matrix: [
                    [1, 1],
                    [0, 1]
                ] },
            { name: 'I3v', matrix: [
                    [1],
                    [1],
                    [1]
                ] },
            { name: 'I2v', matrix: [
                    [1],
                    [1]
                ] },
        ];

        // ─── STATE ───────────────────────────────────────────────
        let grid = [];
        let currentBlocks = []; // array of { matrix, color }
        let selectedIndex = -1;
        let score = 0;
        let gameOver = false;
        let hoverPos = null; // { row, col } untuk preview

        // DOM references
        const gridCanvas = document.getElementById('gridCanvas');
        const ctx = gridCanvas.getContext('2d');
        const scoreSpan = document.getElementById('score-value');
        const finalScoreSpan = document.getElementById('final-score');
        const gameOverOverlay = document.getElementById('game-over-overlay');
        const slots = document.querySelectorAll('.block-slot');
        const slotCanvases = [];
        const slotCtxs = [];
        const emptyLabels = document.querySelectorAll('.empty-label');

        slots.forEach((slot, i) => {
            const canvas = slot.querySelector('canvas');
            slotCanvases.push(canvas);
            slotCtxs.push(canvas.getContext('2d'));
        });

        // ─── HELPERS ─────────────────────────────────────────────
        function randomInt(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function shuffleArray(arr) {
            for (let i = arr.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [arr[i], arr[j]] = [arr[j], arr[i]];
            }
            return arr;
        }

        function cloneMatrix(m) {
            return m.map(row => [...row]);
        }

        function getColor(index) {
            return COLORS[index % COLORS.length];
        }

        // ─── GRID FUNCTIONS ──────────────────────────────────────
        function initGrid() {
            grid = [];
            for (let r = 0; r < GRID_SIZE; r++) {
                grid.push(new Array(GRID_SIZE).fill(null));
            }
        }

        function isCellEmpty(row, col) {
            if (row < 0 || row >= GRID_SIZE || col < 0 || col >= GRID_SIZE) return false;
            return grid[row][col] === null;
        }

        function canPlace(matrix, row, col) {
            for (let r = 0; r < matrix.length; r++) {
                for (let c = 0; c < matrix[0].length; c++) {
                    if (matrix[r][c] === 1) {
                        const gr = row + r;
                        const gc = col + c;
                        if (gr >= GRID_SIZE || gc >= GRID_SIZE) return false;
                        if (!isCellEmpty(gr, gc)) return false;
                    }
                }
            }
            return true;
        }

        function placeMatrix(matrix, row, col, color) {
            for (let r = 0; r < matrix.length; r++) {
                for (let c = 0; c < matrix[0].length; c++) {
                    if (matrix[r][c] === 1) {
                        grid[row + r][col + c] = color;
                    }
                }
            }
        }

        function getEmptyCells() {
            const cells = [];
            for (let r = 0; r < GRID_SIZE; r++) {
                for (let c = 0; c < GRID_SIZE; c++) {
                    if (grid[r][c] === null) cells.push({ row: r, col: c });
                }
            }
            return cells;
        }

        // ─── CHECK LINES ─────────────────────────────────────────
        function checkAndClearLines() {
            let cleared = 0;
            const rowsToClear = [];
            const colsToClear = [];

            // Check rows
            for (let r = 0; r < GRID_SIZE; r++) {
                let full = true;
                for (let c = 0; c < GRID_SIZE; c++) {
                    if (grid[r][c] === null) { full = false; break; }
                }
                if (full) rowsToClear.push(r);
            }

            // Check cols
            for (let c = 0; c < GRID_SIZE; c++) {
                let full = true;
                for (let r = 0; r < GRID_SIZE; r++) {
                    if (grid[r][c] === null) { full = false; break; }
                }
                if (full) colsToClear.push(c);
            }

            // Clear rows
            for (const r of rowsToClear) {
                for (let c = 0; c < GRID_SIZE; c++) {
                    grid[r][c] = null;
                }
                cleared++;
            }

            // Clear cols
            for (const c of colsToClear) {
                for (let r = 0; r < GRID_SIZE; r++) {
                    grid[r][c] = null;
                }
                cleared++;
            }

            // Bonus jika keduanya
            const bonus = (rowsToClear.length > 0 && colsToClear.length > 0) ? 20 : 0;
            const total = cleared * 10 + bonus;
            if (total > 0) {
                score += total;
                updateScore();
                // efek visual sederhana: getar grid? skip
            }
            return total;
        }

        // ─── BLOCKS GENERATION ──────────────────────────────────
        function generateRandomBlock() {
            const idx = randomInt(0, SHAPES_LIST.length - 1);
            const shape = SHAPES_LIST[idx];
            const matrix = cloneMatrix(shape.matrix);
            const color = getColor(randomInt(0, COLORS.length - 1));
            return { matrix, color };
        }

        function generateBlocks() {
            const blocks = [];
            for (let i = 0; i < 3; i++) {
                blocks.push(generateRandomBlock());
            }
            return blocks;
        }

        // ─── GAME OVER CHECK ────────────────────────────────────
        function checkGameOver() {
            // Cek apakah ada blok yang bisa ditempatkan
            for (let bi = 0; bi < currentBlocks.length; bi++) {
                const block = currentBlocks[bi];
                if (!block) continue;
                const matrix = block.matrix;
                for (let r = 0; r <= GRID_SIZE - matrix.length; r++) {
                    for (let c = 0; c <= GRID_SIZE - matrix[0].length; c++) {
                        if (canPlace(matrix, r, c)) {
                            return false; // masih bisa
                        }
                    }
                }
            }
            return true; // game over
        }

        // ─── RENDER ──────────────────────────────────────────────
        function renderGrid() {
            ctx.clearRect(0, 0, CANVAS_SIZE, CANVAS_SIZE);

            // Background grid
            ctx.fillStyle = 'rgba(0,20,40,0.3)';
            ctx.fillRect(0, 0, CANVAS_SIZE, CANVAS_SIZE);

            // Draw cells
            for (let r = 0; r < GRID_SIZE; r++) {
                for (let c = 0; c < GRID_SIZE; c++) {
                    const x = PADDING + c * CELL_SIZE;
                    const y = PADDING + r * CELL_SIZE;
                    const color = grid[r][c];
                    if (color) {
                        // Block
                        const grad = ctx.createRadialGradient(
                            x + 6, y + 6, 4,
                            x + CELL_SIZE / 2, y + CELL_SIZE / 2, CELL_SIZE / 1.6
                        );
                        grad.addColorStop(0, lightenColor(color, 50));
                        grad.addColorStop(1, color);
                        ctx.fillStyle = grad;
                        ctx.shadowColor = 'rgba(0,180,255,0.3)';
                        ctx.shadowBlur = 12;
                        roundRect(ctx, x + 2, y + 2, CELL_SIZE - 4, CELL_SIZE - 4, 6);
                        ctx.fill();
                        ctx.shadowBlur 
