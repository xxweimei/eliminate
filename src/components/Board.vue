<template xmlns:v-bind="http://www.w3.org/1999/xhtml">
  <div>
    <div class="board">
      <div v-for="(row,index) in showMaps" class="row"
           v-bind:class="{row_left:index==0,row_right:index==showMaps.length-1}">
        <div v-for="cell in row" class="cell" @click="mouseClick(cell)" v-bind:class="{
                             cell_r:cell.color=='R',
                             cell_b:cell.color=='B',
                             cell_g:cell.color=='G',
                             cell_y:cell.color=='Y',
                             cell_click:cell.click,
                             moveUp:isMoveUp(cell),
                             moveDown:isMoveDown(cell),
                             moveLeft:isMoveLeft(cell),
                             moveRight:isMoveRight(cell)
                           }">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        maps: [],
        showMaps: [],
        colors: [],
        xSize: 8,
        ySize: 8,
        removeList: [],
        sourceCell: null,
        targetCell: null,
        removingFlag: false
      }
    },
    mounted() {
      this.initMaps();
      while (this.isDieMap()) {
        this.initMaps();
      }
      this.refreshMaps();
    },
    computed: {},
    methods: {
      //初始化地图
      initMaps() {
        for (let i = 0; i < this.ySize; i++) {
          this.maps[i] = [];
        }
        this.initColors();
        for (let i = 0; i < this.ySize; i++) {
          for (let j = 0; j < this.xSize; j++) {
            if (this.colors.length > 0) {
              let cell = {};
              let index = parseInt(Math.random() * (this.colors.length), 10);
              cell.color = this.colors[index];
              cell.x = i;
              cell.y = j;
              this.maps[i][j] = cell;
              if (this.isLine(i, j)) {
                j = j - 1;
                this.colors.splice(index, 1);
              } else {
                this.initColors();
              }
            } else {
              this.initMaps();
              return;
            }
          }
        }
      },
      //初始化颜色
      initColors() {
        this.colors = ['R', 'B', 'G', 'Y'];
      },
      //判断当前坐标元素是否可消除
      isLine(x, y) {
        let lx1 = x - 1 > -1;
        let lx2 = x - 2 > -1;
        let bx1 = x + 1 < this.xSize;
        let bx2 = x + 2 < this.xSize;
        let ly1 = y - 1 > -1;
        let ly2 = y - 2 > -1;
        let by1 = y + 1 < this.ySize;
        let by2 = y + 2 < this.ySize;
        if (ly1 && by1 && this.isCellColorEqual(this.maps[x][y - 1], this.maps[x][y], this.maps[x][y + 1])) {
          return true;
        }
        if (lx1 && bx1 && this.isCellColorEqual(this.maps[x - 1][y], this.maps[x][y], this.maps[x + 1][y])) {
          return true;
        }
        if (ly2 && this.isCellColorEqual(this.maps[x][y], this.maps[x][y - 1], this.maps[x][y - 2])) {
          return true;
        }
        if (by2 && this.isCellColorEqual(this.maps[x][y], this.maps[x][y + 1], this.maps[x][y + 2])) {
          return true;
        }
        if (lx2 && this.isCellColorEqual(this.maps[x][y], this.maps[x - 1][y], this.maps[x - 2][y])) {
          return true;
        }
        return bx2 && this.isCellColorEqual(this.maps[x][y], this.maps[x + 1][y], this.maps[x + 2][y]);
      },
      //判断3个元素颜色是否一致
      isCellColorEqual(c1, c2, c3) {
        if (c1 && c2 && c3) {
          if (c1.color && c2.color && c3.color) {
            return (c1.color == c2.color && c1.color == c3.color);
          }
        }
        return false;
      },
      //判断是否为死图
      isDieMap() {
        for (let i = 0; i < this.xSize; i++) {
          for (let j = 0; j < this.ySize; j++) {
            this.maps[i][j].x = i;
            this.maps[i][j].y = j;
            if (this.isDie(i, j) == false) {
              return false;
            }
          }
        }
        return true;
      },
      isDie(x, y) {
        let lx1 = x - 1 > -1;
        let lx2 = x - 2 > -1;
        let lx3 = x - 3 > -1;
        let bx1 = x + 1 < this.xSize;
        let bx2 = x + 2 < this.xSize;
        let bx3 = x + 3 < this.xSize;
        let ly1 = y - 1 > -1;
        let ly2 = y - 2 > -1;
        let ly3 = y - 3 > -1;
        let by1 = y + 1 < this.ySize;
        let by2 = y + 2 < this.ySize;
        let by3 = y + 3 < this.ySize;
        let color = this.maps[x][y].color;
        if (bx1) {
          if (this.maps[x + 1][y].color == color) {
            if (bx3) {
              if (this.maps[x + 3][y].color == color) {
                return false;
              }
            }
            if (bx2 && by1) {
              if (this.maps[x + 2][y + 1].color == color) {
                return false;
              }
            }
            if (bx2 && ly1) {
              if (this.maps[x + 2][y - 1].color == color) {
                return false;
              }
            }
            if (lx2) {
              if (this.maps[x - 2][y].color == color) {
                return false;
              }
            }
            if (lx1 && ly1) {
              if (this.maps[x - 1][y - 1].color == color) {
                return false;
              }
            }
            if (lx1 && by1) {
              if (this.maps[x - 1][y + 1].color == color) {
                return false;
              }
            }
          }
          if (ly1 && by1) {
            if (this.maps[x + 1][y - 1].color == color && this.maps[x + 1][y + 1].color == color) {
              return false;
            }
          }
        }
        if (lx1) {
          if (this.maps[x - 1][y].color == color) {
            if (lx3) {
              if (this.maps[x - 3][y].color == color) {
                return false;
              }
            }
            if (lx2 && by1) {
              if (this.maps[x - 2][y + 1].color == color) {
                return false;
              }
            }
            if (lx2 && ly1) {
              if (this.maps[x - 2][y - 1].color == color) {
                return false;
              }
            }
            if (bx2) {
              if (this.maps[x + 2][y].color == color) {
                return false;
              }
            }
            if (bx1 && ly1) {
              if (this.maps[x + 1][y - 1].color == color) {
                return false;
              }
            }
            if (bx1 && by1) {
              if (this.maps[x + 1][y + 1].color == color) {
                return false;
              }
            }
          }
          if (ly1 && by1) {
            if (this.maps[x - 1][y - 1].color == color && this.maps[x - 1][y + 1].color == color) {
              return false;
            }
          }
        }
        if (by1) {
          if (this.maps[x][y + 1].color == color) {
            if (by3) {
              if (this.maps[x][y + 3].color == color) {
                return false;
              }
            }
            if (lx1 && by2) {
              if (this.maps[x - 1][y + 2].color == color) {
                return false;
              }
            }
            if (bx1 && by2) {
              if (this.maps[x + 1][y + 2].color == color) {
                return false;
              }
            }
            if (ly2) {
              if (this.maps[x][y - 2].color == color) {
                return false;
              }
            }
            if (bx1 && ly1) {
              if (this.maps[x + 1][y - 1].color == color) {
                return false;
              }
            }
            if (lx1 && ly1) {
              if (this.maps[x - 1][y - 1].color == color) {
                return false;
              }
            }
          }
          if (lx1 && bx1) {
            if (this.maps[x - 1][y + 1].color == color && this.maps[x + 1][y + 1].color == color) {
              return false;
            }
          }
        }
        if (ly1) {
          if (this.maps[x][y - 1].color == color) {
            if (ly3) {
              if (this.maps[x][y - 3].color == color) {
                return false;
              }
            }
            if (lx1 && ly2) {
              if (this.maps[x - 1][y - 2].color == color) {
                return false;
              }
            }
            if (bx1 && ly2) {
              if (this.maps[x + 1][y - 2].color == color) {
                return false;
              }
            }
            if (by2) {
              if (this.maps[x][y + 2].color == color) {
                return false;
              }
            }
            if (bx1 && by1) {
              if (this.maps[x + 1][y + 1].color == color) {
                return false;
              }
            }
            if (lx1 && by1) {
              if (this.maps[x - 1][y + 1].color == color) {
                return false;
              }
            }
          }
          if (lx1 && bx1) {
            if (this.maps[x - 1][y - 1].color == color && this.maps[x + 1][y - 1].color == color) {
              return false;
            }
          }
        }
        return true;
      },
      move(source, target) {
        let sourceColor = this.maps[source.x][source.y].color;
        let targetColor = this.maps[target.x][target.y].color;
        this.maps[target.x][target.y].color = sourceColor;
        this.maps[source.x][source.y].color = targetColor;
        if (!this.isLine(source.x, source.y) && !this.isLine(target.x, target.y)) {
          this.maps[source.x][source.y].color = sourceColor;
          this.maps[target.x][target.y].color = targetColor;
          this.sourceCell = null;
          this.removingFlag = false;
          console.log('no eliminate cell');
          return;
        }
        //消除
        this.fadeCircle();
      },
      near(source, target) {
        let nearCell = (source.x == target.x && (source.y == target.y + 1 || source.y == target.y - 1)) ||
          (source.y == target.y && (source.x == target.x + 1 || source.x == target.x - 1));
        return source.x > -1 && source.x < this.xSize && source.y > -1 && source.y < this.ySize &&
          target.x > -1 && target.x < this.xSize && target.y > -1 && target.y < this.ySize &&
          nearCell;
      },
      fadeCircle() {
        //判断选出要消除的格子
        this.calcToRemoveList();
        //删除并下沉
        this.removeAndDownCell();
      },
      calcToRemoveList() {
        this.removeList = [];
        for (let i = 0; i < this.xSize; i++) {
          for (let j = 0; j < this.ySize; j++) {
            let key = i + '_' + j;
            if (this.maps[i][j] && this.removeList.indexOf(key) == -1) {
              let rowList = [];
              let colList = [];
              this.sameCellColorLeft(i, j, this.maps[i][j].color, rowList);
              this.sameCellColorRight(i, j, this.maps[i][j].color, rowList);
              this.sameCellColorUp(i, j, this.maps[i][j].color, colList);
              this.sameCellColorDown(i, j, this.maps[i][j].color, colList);
              if (rowList.length > 2) {
                rowList.forEach((cellKey) => {
                  this.removeList.push(cellKey);
                });
              }
              if (colList.length > 2) {
                colList.forEach((cellKey) => {
                  this.removeList.push(cellKey);
                });
              }
            }
          }
        }
      },
      removeAndDownCell() {
        if (this.removeList.length == 0) {
          this.removingFlag = false;
          this.sourceCell = null;
          return;
        }
        this.removeList.forEach((cellKey) => {
          this.maps[cellKey.split('_')[0]][cellKey.split('_')[1]].color = ' ';
        });
        this.refreshMaps();
        window.setTimeout(() => {
          this.downCell()
        }, 500);
      },
      downCell() {
        this.initColors();
        this.removeList.sort();
        this.removeList.forEach((cellKey) => {
          let x = cellKey.split('_')[0];
          for (let i = cellKey.split('_')[1]; i > 0; i--) {
            this.maps[x][i].color = this.maps[x][i - 1].color;
          }
          let index = parseInt(Math.random() * (this.colors.length), 10);
          this.maps[x][0].color = this.colors[index];
        });
        this.refreshMaps();
        window.setTimeout(() => {
          this.fadeCircle();
        }, 500);
      },
      sameCellColorLeft(x, y, color, sameList) {
        if (sameList.indexOf(x + '_' + y) == -1) {
          sameList.push(x + '_' + y);
        }
        let nextX = x - 1;
        if (nextX >= 0 && this.maps[nextX][y] && this.maps[nextX][y].color == color) {
          this.sameCellColorLeft(nextX, y, color, sameList);
        }
      },
      sameCellColorRight(x, y, color, sameList) {
        if (sameList.indexOf(x + '_' + y) == -1) {
          sameList.push(x + '_' + y);
        }
        let nextX = x + 1;
        if (nextX < this.xSize && this.maps[nextX][y] && this.maps[nextX][y].color == color) {
          this.sameCellColorRight(nextX, y, color, sameList);
        }
      },
      sameCellColorUp(x, y, color, sameList) {
        if (sameList.indexOf(x + '_' + y) == -1) {
          sameList.push(x + '_' + y);
        }
        let nextY = y - 1;
        if (nextY >= 0 && this.maps[x][nextY] && this.maps[x][nextY].color == color) {
          this.sameCellColorUp(x, nextY, color, sameList);
        }
      },
      sameCellColorDown(x, y, color, sameList) {
        if (sameList.indexOf(x + '_' + y) == -1) {
          sameList.push(x + '_' + y);
        }
        let nextY = y + 1;
        if (nextY < this.ySize && this.maps[x][nextY] && this.maps[x][nextY].color == color) {
          this.sameCellColorDown(x, nextY, color, sameList);
        }
      },
      mouseClick(cell) {
        if (this.removingFlag) return;
        if (this.sourceCell != null) {
          this.maps[this.sourceCell.x][this.sourceCell.y].click = false;
          if (!this.near(this.sourceCell, cell)) {
            cell.click = true;
            this.sourceCell = cell;
            this.refreshMaps();
            console.log('not near');
            return;
          }
          this.removingFlag = true;
          this.targetCell = cell;
          window.setTimeout(() => {
            this.move(this.sourceCell, cell);
          }, 500)
        } else {
          this.sourceCell = cell;
          this.sourceCell.click = true;
          this.refreshMaps();
        }
      },
      isMoveUp(cell) {
        if (!this.removingFlag) return false
        if (this.sourceCell.x != this.targetCell.x) return false;
        if (cell.x == this.sourceCell.x && cell.y == this.sourceCell.y) {
          return cell.y == this.targetCell.y + 1
        }
        if (cell.x == this.targetCell.x && cell.y == this.targetCell.y) {
          return cell.y == this.sourceCell.y + 1
        }
      },
      isMoveDown(cell) {
        if (!this.removingFlag) return false
        if (this.sourceCell.x != this.targetCell.x) return false;
        if (cell.x == this.sourceCell.x && cell.y == this.sourceCell.y) {
          return cell.y == this.targetCell.y - 1
        }
        if (cell.x == this.targetCell.x && cell.y == this.targetCell.y) {
          return cell.y == this.sourceCell.y - 1
        }
      },
      isMoveLeft(cell) {
        if (!this.removingFlag) return false
        if (this.sourceCell.y != this.targetCell.y) return false;
        if (cell.x == this.sourceCell.x && cell.y == this.sourceCell.y) {
          return cell.x == this.targetCell.x + 1
        }
        if (cell.x == this.targetCell.x && cell.y == this.targetCell.y) {
          return cell.x == this.sourceCell.x + 1
        }
      },
      isMoveRight(cell) {
        if (!this.removingFlag) return false
        if (this.sourceCell.y != this.targetCell.y) return false;
        if (cell.x == this.sourceCell.x && cell.y == this.sourceCell.y) {
          return cell.x == this.targetCell.x - 1
        }
        if (cell.x == this.targetCell.x && cell.y == this.targetCell.y) {
          return cell.x == this.sourceCell.x - 1
        }
      },
      refreshMaps() {
        this.showMaps = [];
        this.showMaps = this.maps;
        //this.printMaps();
      },
      //打印
      printMaps() {
        let s = ' \t';
        for (let k = 0; k < this.xSize; k++) {
          s += k + '\t';
        }
        console.log(s);
        for (let i = 0; i < this.xSize; i++) {
          let str = i + '\t';
          for (let j = 0; j < this.ySize; j++) {
            str += this.maps[j][i].color + '\t';
          }
          console.log(str);
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .board {
    display: flex;
  }

  .row {
    border-top: solid 4px gold;
    border-bottom: solid 4px gold;
  }

  .row_left {
    border-left: solid 4px gold;
  }

  .row_right {
    border-right: solid 4px gold;
  }

  .cell {
    width: 50px;
    height: 50px;
    text-align: center;
    border: solid 2px gold;
    user-select: none;
    position: relative;
    border-radius: 50%;
  }

  .cell_click {
    border: dashed 2px blue;
  }

  .cell_r {
    background-image: url("../assets/pic/R.jpg");
  }

  .cell_g {
    background-image: url("../assets/pic/G.png");
  }

  .cell_b {
    background-image: url("../assets/pic/B.jpg");
  }

  .cell_y {
    background-image: url("../assets/pic/Y.jpg");
  }

  .moveDown {
    animation: moveDown 0.5s;
  }

  .moveUp {
    animation: moveUp 0.5s;
  }

  .moveLeft {
    animation: moveLeft 0.5s;
  }

  .moveRight {
    animation: moveRight 0.5s;
  }

  @keyframes moveDown {
    from {
      top: 0
    }
    to {
      top: 50px
    }
  }

  @keyframes moveUp {
    from {
      top: 0
    }
    to {
      top: -50px
    }
  }

  @keyframes moveLeft {
    from {
      left: 0
    }
    to {
      left: -50px
    }
  }

  @keyframes moveRight {
    from {
      left: 0
    }
    to {
      left: 50px
    }
  }
</style>
