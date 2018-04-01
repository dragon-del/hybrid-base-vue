<template>
  <div class="hello">
    <div class="hello-icon_pad" ref="pad">
      <div class="hello-icon_pad-icon" v-for="(p,i) in generatePosition(15)" :key="i" :style="{top:p.y+'px',left:p.x+'px'}">
        <div class="hello-icon_pad-icon-img"></div>
        <div class="hello-icon_pad-icon-name"></div>
      </div>
    </div>
      <v-touch ref="list" @panstart="start" @panmove="move" @panend="end" class="hello-list">
        <v-touch style="height:20px;width: 100%;"></v-touch>
        <v-touch v-for="(value,i) in values" :key="i" class="hello-list-body">
          {{value}}
        </v-touch>
      </v-touch>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      padElement: {},
      values: [],
      listTop: 500,
      startTop: 0
    }
  },
  methods: {
    scroll (e) {
      console.log(e)
    },
    start (e) {
      this.startTop = e.center.y
      this.listTop = this.$refs.list.$el.style.top.split('px')[0]
      this.$refs.list.$el.transition = ''
    },
    move (e) {
      // const top = (Number(this.listTop) + e.center.y - this.startTop)
      // if (top > 100 && top < 400) {
      //   this.$refs.list.$el.style.top = top + 'px'
      // } else {
      //   console.log(1)
      // }
      console.log(e.center.y - this.startTop)
      this.$refs.list.$el.scrollTop = e.center.y - this.startTop
    },
    end (e) {
      console.log(3)
    },
    generatePosition (n) {
      const xMax = this.padElement.clientWidth
      let yMax = this.padElement.clientHeight
      if (!xMax || !yMax) {
        return
      }
      const position = []
      const iconRandius = 80
      let count = 0
      while (position.length < n && count < 1000) {
        count++
        if (count % n === 0) {
          console.log(yMax)
          yMax += yMax
        }
        const iconX = Math.floor(Math.random() * xMax)
        const iconY = Math.floor(Math.random() * yMax)
        let isValid = true
        const checkStartX = Math.max(iconX - iconRandius, 0)
        const checkStartY = Math.max(iconY - iconRandius, 0)
        const checkEndX = Math.min(iconX + iconRandius * 2, 99)
        const checkEndY = Math.min(iconY + iconRandius * 2, 99)
        const checkPosition = _.filter(position, p => {
          return (checkStartX < p.x < checkEndX) && (checkStartY < p.y < checkEndY)
        })
        for (let index = 0; index < checkPosition.length; index++) {
          const element = position[index]
          const treeDistanceSquared = (iconX - element.x) * (iconX - element.x) + (iconY - element.y) * (iconY - element.y)
          const radiusSumSquared = (iconRandius) * (iconRandius)
          if (treeDistanceSquared < radiusSumSquared) {
            isValid = false
          }
          if (iconX < 10 || (iconX + iconRandius + 10) > xMax) {
            isValid = false
          }
        }
        if (isValid) {
          position.push({x: iconX, y: iconY})
        }
      }
      return position
    }
  },
  mounted () {
    const pad = this.$refs.pad
    this.padElement = pad
    for (let index = 0; index < 100; index++) {
      this.values.push(index)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.hello{
  position: absolute;
  top:0;
  left: 0;
  right: 0;
  bottom: 0;
  &-icon_pad{
  position: absolute;
  top:100px;
  left: 0;
  right: 0;
  bottom: 0;
  overflow-y: auto;
    &-icon{
      position:  absolute;width:  50px;height:  70px;background-color:grey;
      &-img{
        border-radius: 50px;
        width: 100%;
        height: 50px;
        background-color: greenyellow;
      }
      &-name{
        height: 20px;
        width: 100%;
      }
    }
  }
  &-list{
    position: absolute;
    top:300px;
    left: 0;
    right: 0;
    bottom: 0;
    overflow-y: auto;
    border: solid;
    &-body{
      height: 20px;
      background-color: cornflowerblue;
    }
  }
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
}
</style>
