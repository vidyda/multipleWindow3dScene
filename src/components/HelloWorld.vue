<template>
  <div class="wrap" id="wrap">
    <div class="cube" v-for="(item, index) in allInfo" :id="item.id" :key="item.id" :style="{
      left: item.left, top: item.top,
      transform: `rotateX(${tenMill + num * 360}deg) rotateY(${tenMill + num * 360}deg)`,
      transitionDuration: transitionDuration,
    }">
      <div :style="{ 'border-color': item.color }" class="face front"></div>
      <div :style="{ 'border-color': item.color }" class="face back"></div>
      <div :style="{ 'border-color': item.color }" class="face right"></div>
      <div :style="{ 'border-color': item.color }" class="face left"></div>
      <div :style="{ 'border-color': item.color }" class="face top"></div>
      <div :style="{ 'border-color': item.color }" class="face bottom"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      saveKey: 'sharedData',
      id: '',
      allInfo: [],
      seconds: 0,
      transitionDuration: '0s',
      num: 0,
      tenMill: 0,
      timer: null,
    }
  },
  created() {

    this.id = this.generateUUID();
    this.timer = setInterval(() => {
      this.allInfo = this.getinfo();
    }, 500);

    this.checkSecond();
    setInterval(() => {
      this.checkSecond();
      this.transitionDuration = '0.1s';
    }, 100);

  },
  mounted() {
    window.addEventListener('beforeunload', this.clearActiveBox);
  },
  methods: {
    checkSecond() {
      var now = new Date();
      var seconds = now.getSeconds();
      var mill = now.getMilliseconds();
      if (seconds == 0 && Math.floor(mill / 100) == 0) {
        this.num++;
      }
      this.tenMill = (seconds * 10 + mill / 100) * 6 / 10;
    },
    generateRandomColor() {
      var r = Math.floor(Math.random() * 256);
      var g = Math.floor(Math.random() * 256);
      var b = Math.floor(Math.random() * 256);

      var hexR = r.toString(16).padStart(2, '0');
      var hexG = g.toString(16).padStart(2, '0');
      var hexB = b.toString(16).padStart(2, '0');

      var hexColor = '#' + hexR + hexG + hexB;

      return hexColor;
    },
    generateUUID() {
      return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
        var r = Math.random() * 16 | 0,
          v = c == 'x' ? r : (r & 0x3 | 0x8);
        return v.toString(16);
      });
    },
    getinfo() {
      var elePosition = this.getElementPosition();
      var object = {
        id: this.id,
        position: elePosition,
        color: this.generateRandomColor(),
      }
      var allInfo = localStorage.getItem(this.saveKey);
      if (allInfo != undefined) {
        allInfo = JSON.parse(allInfo);
        let index = allInfo.findIndex(item => item.id === this.id);
        if (index !== -1) {
          allInfo[index].position = object.position;
        } else {
          allInfo.push(object);
        }
      } else {
        allInfo = [object];
      }
      localStorage.setItem(this.saveKey, JSON.stringify(allInfo));
      return this.getPositionInfo(allInfo);
    },
    getPositionInfo(arr) {
      var position = this.getWindowPosition();
      arr.forEach(element => {
        element.left = (element.position.x - position.x - 50) + 'px';
        element.top = (element.position.y - position.y - 50) + 'px';
      });
      return arr;
    },
    getElementPosition() {
      var position = this.getWindowPosition();
      const windowInfo = this.getWindowInfo();
      var x = position.x + windowInfo.width / 2;
      var y = position.y + windowInfo.heright / 2;
      return { x: x, y: y }

    },
    getWindowPosition() {
      const browserScreenX = window.screenX || window.screenLeft;
      const browserScreenY = window.screenY || window.screenTop;
      return { x: browserScreenX, y: browserScreenY, }
    },
    getScreenInfo() {
      const screenWidth = window.screen.width;
      const screenHeight = window.screen.height;
      return { width: screenWidth, heright: screenHeight, }
    },
    getWindowInfo() {
      const windowWidth = window.innerWidth;
      const windowHeight = window.innerHeight;
      return { width: windowWidth, heright: windowHeight, }
    },
    clearActiveBox() {
      var target = localStorage.getItem(this.saveKey);
      clearInterval(this.timer);
      if (target != undefined) {
        target = JSON.parse(target);
        target = target.filter(item => item.id != this.id);
        localStorage.setItem(this.saveKey, JSON.stringify(target));
      }
    }
  },
}
</script>

<style >
h1,
h2 {
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

:root {
  --wrap-size: 100px;
  --inner-size: 50px;
}

* {
  padding: 0;
  margin: 0;
}

.box {
  position: absolute;
  width: 100px;
  height: 100px;
  border: 1px solid red;
  transition: all .5s linear;
}

.wrap {
  width: 100%;
  height: 100vh;
  position: relative;
  background-color: #000;
  overflow: hidden;
}


.cube {
  width: var(--wrap-size);
  height: var(--wrap-size);
  position: absolute;
  transform-style: preserve-3d;
  transition: all 0s linear;
}

.face {
  position: absolute;
  width: var(--wrap-size);
  height: var(--wrap-size);
  border: 1px solid #f00;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  font-weight: bold;
}

.front {
  transform: translateZ(var(--inner-size));
}

.back {
  transform: rotateY(180deg) translateZ(var(--inner-size));
}

.right {
  transform: rotateY(90deg) translateZ(var(--inner-size));
}

.left {
  transform: rotateY(-90deg) translateZ(var(--inner-size));
}

.top {
  transform: rotateX(90deg) translateZ(var(--inner-size));
}

.bottom {
  transform: rotateX(-90deg) translateZ(var(--inner-size));
}


@keyframes spin {
  0% {
    transform: rotateX(0) rotateY(0);
  }

  100% {
    transform: rotateX(360deg) rotateY(360deg);
  }
}
</style>
