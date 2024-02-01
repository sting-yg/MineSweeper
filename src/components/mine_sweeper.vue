<template>
    <div class="mine-sweeper">
        <svg :width="width" :height="height" xmlns="http://www.w3.org/2000/svg">
            <g v-for="(row, rowIndex) in grid" :key="rowIndex">
                <g v-for="(cell, colIndex) in row" :key="colIndex">
                    <rect 
                        :x="colIndex * cellSize"
                        :y="rowIndex * cellSize"
                        :width="cellSize"
                        :height="cellSize"
                        :fill="getCellColor(cell)"
                        stroke="#000"
                        stroke-width="1"
                        @click="handleCellClick(rowIndex, colIndex)"
                    />
                    <text
                        v-if="cell.isRevealed"
                        :x="colIndex * cellSize+ cellSize/2"
                        :y="rowIndex * cellSize+ cellSize/2"
                        text-anchor="middle"
                        dominant-baseline="middle"
                        fill="black"
                    >
                    </text>
                </g>
            </g>
        </svg>        
    </div>
</template>

<script>
import { reactive } from 'vue';

    export default {
    datat() {
        return {
            grid: [],
            rows: 5,
            cols: 5,
            mines: 5,
            cellSize: 50,
            width: 500,
            height: 500,
        };
    },
    created() {
        this.initializeGrid();
    },
    methods: {
        initializeGrid() {
            this.grid = Array.from({length: this.rows}, ()=>{
                return Array.from({length: this.cols}, ()=>{
                    return({isMine: false, isRevealed: false, adjacentMines: 0})
                });
            });

            for(let i=0; i<this.mines; i++){
                let randomRow, randomCol;
                do{
                    randomRow = Math.floor(Math.random * rows);
                    randomCol = Math.floor(Math.random * cols);
                }while(this.grid[randomRow],[randomCol].isMine)
                this.grid[randomRow],[randomCol].isMine = true;
            }

            this.grid.forEach((row, rowIndex) => {
                row.forEach((cell, colIndex) => {
                    if(!cell.isMine) {
                        cell.adjacentMines = this.countNeibor(rowIndex, colIndex);
                    }
                });
            });
        },
        countNeibor(row, col) {
            let count = 0;
            for(let i= row-1; i<row+1; i++){
                for(let j= col-1; j<col+1; j++){
                    if(i>=0 && i<this.row && j>=0 && j<this.cols && this.grid[i][j].isMine){
                        count++;
                    }
                }
            }
            return count;
        },
        getCellColor(cell) {
            if(cell.isRevealed){
                return cell.isMine? 'red' : 'white';
            }else{
                return 'gray';
            }
        },
        getCellValue(cell) {
            return cell.isRevealed?(cell.isMine?'💣' : cell.adjacentMines) : '';
        },
        handleCellClick(row, col) {
            const cell = this.grid[row][col];
            if(!cell.isRevealed){
                cell.isRevealed = true;
                if(cell.isMine){
                    alert('end')
                }else if(cell.isRevealed ===0){
                    this.revealEmptyCells(row, col);
                }

                this.checkForWin();
            }
        },
        revealEmptyCells(row, col) {
            for(let i=row-1; i<=row+1; i++){
                for(let j=col-1; j<=col+1; j++){
                    if(i >=0 && i < this.rows && j >= 0 && j < this.cols){
                        const neighbor = this.grid[i][j];
                        if(!neighbor.isRevealed && !neighbor.isMine){
                            neighbor.isRevealed = true;
                            if(neighbor.adjacentMines ===0){
                                this.revealEmptyCells(i, j);
                            }
                        }

                    }
                }
            }

        },
        checkForWin() {
            let unrevealedSafeCells = 0;
            this.grid.forEach((row) =>{
                row.forEach((cell)=> {
                    if(!cell.isRevealed && !cell.isMine){
                        unrevealedSafeCells++;
                    }
                });
            });

            if(unrevealedSafeCells === 0){ 
                alert("win");
            }
        },
    },
    components: { reactive }
}
</script>

<style>
.mine-sweeper{
    display: inline-block;
}
text{
    font-size: 20px;
}
</style>