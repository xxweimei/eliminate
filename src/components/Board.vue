<template>
  <div>
    <div class="board">
      <div v-for="row in maps" class="row">
        <div v-for="cell in row" class="cell"
             v-bind:class="{cell_r:cell.color=='R',cell_b:cell.color=='B',cell_g:cell.color=='G',cell_y:cell.color=='Y'}">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data(){
      return {
        maps: [],
        colors: [],
        xSize: 10,
        ySize: 10
      }
    },
    mounted(){
      let tempMaps = this.initMaps();
      while (this.isDieMap(tempMaps)) {
        tempMaps = this.initMaps();
      }
      this.maps = tempMaps;
    },
    computed: {},
    methods: {
      //初始化地图
      initMaps(){
        let tempMaps = [];
        for (let i = 0; i < this.ySize; i++) {
          tempMaps[i] = [];
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
              tempMaps[i][j] = cell;
              if (this.isLine(i, j, tempMaps)) {
                j = j - 1;
                this.colors.splice(index, 1);
              } else {
                this.initColors();
              }
            } else {
              return this.initMaps();
            }
          }
        }
        return tempMaps;
      },
      //初始化颜色
      initColors() {
        this.colors = ['R', 'Y', 'B', 'G'];
      },
      //判断当前坐标元素是否可消除
      isLine(x, y, tempMaps) {
        let lx1 = x - 1 > -1;
        let lx2 = x - 2 > -1;
        let bx1 = x + 1 < this.xSize;
        let bx2 = x + 2 < this.xSize;
        let ly1 = y - 1 > -1;
        let ly2 = y - 2 > -1;
        let by1 = y + 1 < this.ySize;
        let by2 = y + 2 < this.ySize;
        if (ly1 && by1 && this.isCellColorEqual(tempMaps[x][y - 1], tempMaps[x][y], tempMaps[x][y + 1])) {
          return true;
        }
        if (lx1 && bx1 && this.isCellColorEqual(tempMaps[x - 1][y], tempMaps[x][y], tempMaps[x + 1][y])) {
          return true;
        }
        if (ly2 && this.isCellColorEqual(tempMaps[x][y], tempMaps[x][y - 1], tempMaps[x][y - 2])) {
          return true;
        }
        if (by2 && this.isCellColorEqual(tempMaps[x][y], tempMaps[x][y + 1], tempMaps[x][y + 2])) {
          return true;
        }
        if (lx2 && this.isCellColorEqual(tempMaps[x][y], tempMaps[x - 1][y], tempMaps[x - 2][y])) {
          return true;
        }
        if (bx2 && this.isCellColorEqual(tempMaps[x][y], tempMaps[x + 1][y], tempMaps[x + 2][y])) {
          return true;
        }
        return false;
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
      isDieMap(tempMaps) {
        return false;
      },
      //打印
      printMaps() {
        let s = ' \t';
        for (let k = 0; k < this.xSize; k++) {
          s += k + '\t';
        }
        console.log(s);
        for (let i = 0; i < this.ySize; i++) {
          let str = i + '\t';
          for (let j = 0; j < this.xSize; j++) {
            str += this.maps[i][j].color + '\t';
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

  .cell {
    width: 30px;
    height: 30px;
    text-align: center;
    border: solid black 1px;
  }

  .cell_r {
    background-color: red;
  }

  .cell_y {
    background-color: yellow;
  }

  .cell_b {
    background-color: blue;
  }

  .cell_g {
    background-color: green;
  }
</style>
