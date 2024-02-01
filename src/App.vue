<template>
    <div>
        <svg :width="width" :height="height">
            <g v-for="(row, rowIndex) in grid" :key="rowIndex">
                <g v-for="(cell, colIndex) in row" :key="colIndex">
                    <rect
                        :x="colIndex * cellSize"
                        :y="rowIndex * cellSize"
                        :width="cellSize"
                        :height="cellSize"
                        :fill="getColor(cell)"
                        stroke="#000"
                        stroke-width="1"
                        @click.left="getLeftClicked(rowIndex, colIndex)"
                        @click.right="getRightClicked(rowIndex, colIndex)"
                    />
                    <text
                        v-if="cell.isClicked"
                        :x="colIndex * cellSize + cellSize / 2"
                        :y="rowIndex * cellSize + cellSize / 2"
                        text-anchor="middle"
                        dominant-baseline="middle"
                        fill="black"
                    >
                        {{ getValue(cell) }}
                    </text>
                    <text
                        v-if="cell.isFlag"
                        :x="colIndex * cellSize + cellSize / 2"
                        :y="rowIndex * cellSize + cellSize / 2"
                        text-anchor="middle"
                        dominant-baseline="middle"
                        fill="black"
                    >
                        {{ getFlag(cell) }}
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
            width: 2000,
            height: 720,
            cols: 30,
            rows: 18,
            cellSize: 40,    
            mines: 50,    
            
        }
    },
    created(){
        this.initializeSvg();
    },

    methods:{
        initializeSvg(){
           
            for(let i = 0; i < this.rows; i++){
                let row = [];
                for(let j = 0; j <this.cols; j++){
                    row.push({isMine: false, isClicked: false,isFlag: false, isNeiborMineCount: 0});    
                }
                this.grid.push(row);
            }
            
            for(let i = 0; i <this.mines; i++){
                let randomRow , randomCol
                do{
                    randomRow = Math.floor(Math.random() * this.rows)
                    randomCol = Math.floor(Math.random() * this.cols)
                }while(this.grid[randomRow][randomCol].isMine)
                this.grid[randomRow][randomCol].isMine = true
            }

            for (let i = 0; i < this.grid.length; i++) {
                for (let j = 0; j < this.grid[i].length; j++) {
                    if (!this.grid[i][j].isMine) {
                        this.grid[i][j].isNeiborMineCount = this.getNeiborMineCount(i, j);
                    }
                }
            }   

        },
        getColor(cell){
            if(cell.isClicked){
                return cell.isMine? 'white' : 'green'
            }else{
                return 'gray'
            }

        },
        getValue(cell){
            if(cell.isClicked){
                return cell.isMine? '💥' : cell.isNeiborMineCount;
            }else{
                return '';
            }
        },
        getFlag(cell){       
            return cell.isFlag? '🚩' : ''           
        },
        getNeiborMineCount(row,col){
            let count = 0
          
            for(let i = row-1; i <= row + 1; i++){
                for(let j = col-1; j <= col + 1; j++){
                    if(i>=0 && i<this.rows && j>=0 && j<this.cols && this.grid[i][j].isMine){
                        count++                      
                        console.log(count);
                    }
                }
            }
            return count;
        },     
        getLeftClicked(row, col){           
            if(!this.grid[row][col].isClicked){
                this.grid[row][col].isClicked = true
                if(this.grid[row][col].isMine == true){
                    alert("you choos the boom game is over!")
                    location.reload();
                }
                else if(this.grid[row][col].isNeiborMineCount == 0){
                    this.getOpenZeroNeibor(row, col);
                }
            }
        },
        getRightClicked(row, col){ 
            document.oncontextmenu = (e) =>{
                e.preventDefault();
            }
            if(!this.grid[row][col].isClicked){
                if(this.grid[row][col].isFlag == false){
                    this.grid[row][col].isFlag = true;
                }else if(this.grid[row][col].isFlag == true){
                    this.grid[row][col].isFlag = false
                }
            }
        },
        getOpenZeroNeibor(row ,col){
            for(let i = row-1; i<= row + 1 ; i++){
                for(let j = col-1; j<= col + 1; j++){
                    if(i>=0 && i < this.rows && j>=0 && j < this.cols){
                        if(!this.grid[i][j].isClicked&&!this.grid[i][j].isMine){
                            this.grid[i][j].isClicked = true
                            this.grid[i][j].isFlag = false
                            if(this.grid[i][j].isNeiborMineCount === 0){
                                this.getOpenZeroNeibor(i,j)
                            }
                        }
                    }   
                }
            }
        },  
    }
}

</script>

<style>
text {
  font-size: 20px;
}
</style>