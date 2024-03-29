<template>
    <div class="sudoku">
    <div
        class="header"
        v-show='showDetails'
        >
        <timer
        :startMin='minutes'
        :startSec='seconds'
        ref="timer"
        />
        <div>{{mistakes}} Mistakes</div>
        </div>
        <div class="grid">
        <div
            class="row"
            v-for='(row, rowIndex) in sudoku'
            :key='rowIndex'
            >
        <div
            class="cell"
            v-for='(cell, colIndex) in row'
            :key='colIndex'
            :class='getClass(cell, rowIndex, colIndex)'
            @mouseup='handleSelect(rowIndex, colIndex, $event)'
            @keydown.prevent='typeDigit'
            tabindex="0"
            >
            {{cell}}
        </div>
    </div>
    </div>
    <div
        class="digits"
        v-show='showDetails'
        >
        <button
        v-for='n in 9'
        :key='n'
        @click="insertDigit(n)"
        >
        {{n}}
        </button>
    </div>
</div>
</template>

<script>
import Timer from "./Timer";

let newSudoku = [[], [], [], [], [], [], [], [], []];
const digits = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const cornerSquares = [
    {
        rowStart: 0,
        colStart: 0,
    },
    {
        rowStart: 0,
        colStart: 6,
    },
    {
        rowStart: 6,
        colStart: 6,
    },
    {
        rowStart: 6,
        colStart: 0,
    },
];
const innerSquares = [
    {
        rowStart: 0,
        colStart: 3,
    },
    {
        rowStart: 3,
        colStart: 0,
    },
    {
        rowStart: 6,
        colStart: 3,
    },
    {
        rowStart: 3,
        colStart: 6,
    },
    {
        rowStart: 3,
        colStart: 3,
    },
];
export default {
    name: "sudoku",
    data() {
        return {
            sudoku: [],
            solution: [],
            rowSelected: null,
            colSelected: null,
            seconds: 0,
            minutes: 0,
            mistakes: 0,
        };
    },
    components: {
        Timer,
    },
    props: {
        diff: Number,
        showDetails: Boolean,
        currentGame: Boolean,
    },
    methods: {
        handleSelect(rowIndex, colIndex) {
            this.rowSelected = rowIndex;
            this.colSelected = colIndex;
        },
        insertDigit(digit) {
            if (
                digit !== this.solution[this.rowSelected][this.colSelected] &&
                digit !== ""
            ) {
                this.mistakes++;
            }
            this.sudoku[this.rowSelected].splice(this.colSelected, 1, digit);
        },
        typeDigit(event) {
            const key = event.key,
                digit = parseInt(key);
            if (!isNaN(digit) && digit !== 0) {
                this.insertDigit(digit);
            }
            if (key === "Delete" || key === "Backspace") {
                this.insertDigit("");
            }
        },
        getClass(cell, rowIndex, colIndex) {
            if (this.showDetails === false) {
                return "";
            }
            if (cell !== this.solution[rowIndex][colIndex] && cell !== "") {
                return "invalid";
            }
            const row = this.sudoku[rowIndex],
                col = this.getCol(colIndex, this.sudoku),
                square = this.getSquare(rowIndex, colIndex, this.sudoku),
                rowFilled = this.checkFilled(row),
                colFilled = this.checkFilled(col),
                squareFilled = this.checkFilled(square);
            if (rowFilled || colFilled || squareFilled) {
                if (this.rowSelected === rowIndex || this.colSelected === colIndex) {
                    return "completeHighlight";
                }
                return "complete";
            }
            if (this.rowSelected === rowIndex || this.colSelected === colIndex) {
                if (this.rowSelected === rowIndex && this.colSelected === colIndex) {
                    return "";
                }
                return "highlight";
            }
        },
        checkFilled(array) {
            const filtered = array.filter((cell) => cell !== "");
            return new Set(filtered).size === 9;
        },
        randomArrayVal(array) {
            const randomIndex = Math.floor(Math.random() * array.length);
            return array[randomIndex];
        },
        getCol(col, sudoku) {
            return sudoku.map((row) => row[col]);
        },
        selectDigit(row, col, squareArray) {
            const rowArray = newSudoku[row],
                colArray = this.getCol(col, newSudoku),
                // squareArray = this.getSquare(row, col),
                arrayMatrix = rowArray.concat(colArray).concat(squareArray),
                potentialDigits = digits.filter(
                    (digit) => !arrayMatrix.includes(digit)
                );
            return this.randomArrayVal(potentialDigits);
        },
        getSquare(row, col, sudoku) {
            const rowStart = Math.floor(row / 3) * 3,
                rowEnd = rowStart + 3,
                colStart = Math.floor(col / 3) * 3,
                colEnd = colStart + 3,
                squareRows = sudoku.slice(rowStart, rowEnd),
                squareCols = squareRows.map((sR) => {
                    return sR.slice(colStart, colEnd);
                }),
                squareArray = squareCols.flat();
            return squareArray;
        },
        resetCell() {
            return "-";
        },
        fillSquare({ rowStart, colStart }, callback) {
            let squareArray = [];
            for (let row = rowStart; row < rowStart + 3; row++) {
                for (let col = colStart; col < colStart + 3; col++) {
                    const digit = callback(row, col, squareArray);
                    if (digit === undefined) {
                        return true;
                    }
                    squareArray.push(digit);
                    newSudoku[row][col] = digit;
                }
            }
        },
        fillSection(section) {
            section.forEach((square) => this.fillSquare(square, this.resetCell));
            for (const square of section) {
                const breakCheck = this.fillSquare(square, this.selectDigit);
                if (breakCheck === true) {
                    this.fillSection(section);
                    return;
                }
            }
        },
        makeSudoku() {
            this.fillSection(cornerSquares);
            this.fillSection(innerSquares);
            this.sudoku = newSudoku.map((row) => {
                return row.map((col) => {
                    if (Math.round(Math.random() * 10) <= this.diff) {
                        col = "";
                    }
                    return col;
                });
            });
            this.solution = newSudoku;
        },
        saveGame() {
            const { sudoku, solution, mistakes, diff } = this;
            const { minutes, seconds } = this.$refs.timer;
            const currentGame = {
                sudoku,
                solution,
                seconds,
                minutes,
                mistakes,
                diff,
            };
            localStorage.currentGame = JSON.stringify(currentGame);
        },
        loadGame() {
            const game = JSON.parse(localStorage.currentGame);
            Object.keys(game).forEach((key) => (this[key] = game[key]));
        },
    },
    beforeDestroy() {
        this.saveGame();
    },
    mounted() {
        if (this.currentGame === true) {
            this.loadGame();
        } else {
            this.makeSudoku();
        }
        setInterval(this.tick, 1000);
        window.addEventListener("unload", this.saveGame);
    },
};
</script>

<style>
.sudoku {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    cursor: pointer;
}
.grid {
    border-top: 2px solid black;
    border-left: 2px solid black;
}
.header {
    display: flex;
    justify-content: center;
    width: 100%;
    margin-bottom: 10px;
}
.row, .digits {
    display: flex;
}
.digits button {
    font-size: 15px;
    margin: 10px;
}
.row {
    border-bottom: 1px solid black;
}
.row:nth-child(3n) {
    border-bottom: 2px solid black;
}
.cell {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 3em;
    height: 3em;
    border-right: 1px solid black;
    color: #1d2124;
    background: #f8f9fa;
}
.cell.complete, .cell.completeHighlight {
    color: black;
    background: #c4f8ff;
}
.cell.invalid {
    color: white;
    background: rgb(230, 0, 120);
}
.cell.highlight {
    background: #ececee;
}
.cell:nth-child(3n) {
    border-right: 2px solid black;
}
</style>