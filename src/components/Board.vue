<template>
  <div>
    <div class="board">
      <div v-for="row in maps" class="box">
        <div v-for="cell in row">
          box
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data(){
      return {
        maps: []
      }
    },
    mounted(){
      for (let i = 0; i < 10; i++) {
        let row = [];
        for (let j = 0; j < 10; j++) {
            row.push({type: 0})
        }
        this.maps.push(row)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .board {
    display: flex;
  }

  .box {

  }
</style>
