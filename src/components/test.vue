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
export default {
  data() {
    return {
      grid: [],
      rows: 5,
      cols: 5,
      mines: 5,
      cellSize: 40,
      width: 200,
      height: 200,
    };
  },
  created() {
    this.initializeGrid();
  },
  methods: {
    initializeGrid() {
      // 비어있는 블록 마들기 
      this.grid = Array.from({ length: this.rows }, () => {
        return Array.from({ length: this.cols }, () => {
          // mine 여부 클릭여부 주변 mine 초기 설정 
          return { isMine: false, isRevealed: false, adjacentMines: 0 };
        });
      });

      // 랜덤좌표 행 열 로 mine 생성 최대 5개 
      for (let i = 0; i < this.mines; i++) {
        let randomRow, randomCol;

        do {
          randomRow = Math.floor(Math.random() * this.rows);
          randomCol = Math.floor(Math.random() * this.cols);
        } while (this.grid[randomRow][randomCol].isMine);
        //
        this.grid[randomRow][randomCol].isMine = true;
      }

      // mine 아닌 주변 카운트 
      this.grid.forEach((row, rowIndex) => {
        row.forEach((cell, colIndex) => {
          if (!cell.isMine) {
            cell.adjacentMines = this.calculateAdjacentMines(rowIndex, colIndex);
          }
        });
      });
    },
    calculateAdjacentMines(row, col) {
      let count = 0;
      //mine 아닌 경우 상중하행 좌중우 열 mine 여부 확인 및 수자 카운트 
      for (let i = row - 1; i <= row + 1; i++) {

        for (let j = col - 1; j <= col + 1; j++) {
          if (i >= 0 && i < this.rows && j >= 0 && j < this.cols && this.grid[i][j].isMine) {
            count++;
          }
        }
      }

      return count;
    },

    // mine : red 아니면 white 클릭전 gray 
    getCellColor(cell) {
      if (cell.isRevealed) {
        return cell.isMine ? 'red' : 'white';
      } else {
        return 'gray';
      }
    },
    getCellValue(cell) {
      return cell.isRevealed ? (cell.isMine ? '💣' : cell.adjacentMines) : '';
    },
    handleCellClick(row, col) {
      const cell = this.grid[row][col];

      // 클랙된후 상태 변경
      if (!cell.isRevealed) {
        cell.isRevealed = true;
        // mine 클릭이면 게임끝
        if (cell.isMine) {
          // Handle mine click
          alert('Game Over! You clicked on a mine.');
        } else if (cell.adjacentMines === 0) {
      

          //클릭된 주변에 mine 없으면 주변을 click 상태로 변경 
          this.revealEmptyCells(row, col);
        }

        // Check for win condition
        this.checkForWin();
      }
    },
    revealEmptyCells(row, col) {
      for (let i = row - 1; i <= row + 1; i++) {
        for (let j = col - 1; j <= col + 1; j++) {
          if (i >= 0 && i < this.rows && j >= 0 && j < this.cols) {
            const neighbor = this.grid[i][j];
            if (!neighbor.isRevealed && !neighbor.isMine) {
              neighbor.isRevealed = true;
              // 클릭된 주변에 0 이 나올경우 그주변도 클릭상태로 변경 그좌표 기준 으로 함수 재호출 
              if (neighbor.adjacentMines === 0) {
                this.revealEmptyCells(i, j);
              }
            }
          }
        }
      }
    },
    checkForWin() {
      let unrevealedSafeCells = 0;

      this.grid.forEach((row) => {
        row.forEach((cell) => {
          if (!cell.isRevealed && !cell.isMine) {
            unrevealedSafeCells++;
          }
        });
      });

      if (unrevealedSafeCells === 0) {
        alert('Congratulations! You won!');
      }
    },
  },
};
</script>

<style scoped>
.mine-sweeper {
  display: inline-block;
}

text {
  font-size: 20px;
}
</style>
