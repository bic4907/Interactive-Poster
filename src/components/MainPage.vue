<template>
  <div id="container"></div>
</template>

<script>
import { Application, Graphics, Ticker } from 'pixi.js';

export default {
  name: "MainPage",
  data: () => {

  },
  mounted() {
    console.log('mounted!')
    this.game = new Application({ width: 600, height: 1000, backgroundColor: '#eeeeee' })
    this.container = document.querySelector('#container');

    this.container.appendChild(this.game.view);

    let BOX_SIZE = 30
    let rect = new Graphics();

    let CANVAS_SIZE = 600
    rect.beginFill(0xffffff);
    rect.lineStyle(3, 0xcccccc, 1);
    rect.drawRect(CANVAS_SIZE / 2 - BOX_SIZE / 2, 1000 - BOX_SIZE, BOX_SIZE, BOX_SIZE);
    rect.endFill();

    this.game.stage.addChild(rect);


//Set Animation
    let jumping = false;

    const
        axis = 'y',
        direction = -1, //to Top
        gravity = 1,
        power = 20,
        jumpAt = rect[axis];

    const jump = () => {
      if (jumping) return;
      jumping = true;

      let time = 0;

      const tick = deltaMs => {
        const jumpHeight = (-gravity / 2) * Math.pow(time, 2) + power * time;

        if (jumpHeight < 0) {
          jumping = false;
          Ticker.shared.remove(tick);
          rect[axis] = jumpAt;
          return;
        }

        rect[axis] = jumpAt + (jumpHeight * direction);
        time += deltaMs;
      }

      Ticker.shared.add(tick);
    }

    this.game.view.addEventListener('click', jump);
    this.game.view.addEventListener('touchend', jump);
  }
}
</script>

<style scoped>
* { margin: 0; border: 0; padding: 0; }
html { width: 100%; height: 100%; }
body { min-height: 100%; background-color: #f7f7f7; display: flex; justify-content: center; align-items: center; }
</style>