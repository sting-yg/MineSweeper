<template>
    <div>
        <svg :width="width" :height="height">
            <g v-for="(row, rowIndex) in grid" :key="rowIndex">
                <g v-for="(cell, colIndex) in row" :key="colIndex">
                    <rect
                        :x="rowIndex * cellSize"
                        :y="colIndex * cellSize"
                        :width="cellSize"
                        :height="cellSize"
                        stroke="#000"
                        stroke-width="1"   
                        :fill="getColor(cell)"                   
                    />
                    <text
                        v-if="cell.isRevealed"
                        :x="colIndex * cellSize + cellSize / 2"
                        :y="rowIndex * cellSize + cellSize / 2"
                        text-anchor="middle"
                        dominant-baseline="middle"
                        fill="black"
                    >
                        {{ getCellValue(cell) }}
                    </text>
                </g>
            </g>
        </svg>     
    </div>
</template>
<script>
export default{
    data(){
        return {
            grid: [],
            width: 500,
            height: 500,
            cols: 5,
            rows: 5,
            cellSize: 50,
           
        }
       
    },
    created(){
        this.initializeSvg();
    },

    methods:{
        initializeSvg(){
            for(let i = 0; i <this.rows; i++){
                let row = [];
                for(let j = 0; j <this.cols; j++){
                    row.push({isMine: false, isClicked: false, isNeiborMineCount: 0});
                }
                this.grid.push(row);
            }
            
            for(let i = 0; i <5; i++){
                let randomRow , randomCol
                do{
                    randomRow = Math.floor(Math.random() * this.rows)
                    randomCol = Math.floor(Math.random() * this.cols)
                }while(this.grid[randomRow][randomCol].isMine)
                this.grid[randomRow][randomCol].isMine = true
            }

            for (let i = 0; i < this.grid.length; i++) {
                for (let j = 0; j < this.grid[i].length; j++) {
                    let cell = this.grid[i][j];
                    if (!cell.isMine) {
                        cell.isNeiborMineCount = this.getMineCount(i, j);
                    }
                }
            }   
        },
        getColor(cell){
            if(cell.isClicked){
                cell.isMine? 'red' : 'white'
            }else{
                'gray'
            }
        },
        getValue(cell){
            return cell.isClicked? (cell.isMine? "O" : cell.isNeiborMineCount) : '';
        },

        getMineCount(row,col){
            let count = 0
            
            for(let i = row-1; i < row + 1; i++){
                for(let j = col-1; j < col + 1; j++){
                    if(i>=0 && i<=this.rows && j>=0 && j<this.cols && this.grid[i][j].isMine){
                        count++
                    }
                }
            }
            return count;
        },
    }
}

</script>