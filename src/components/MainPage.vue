<template>
  <div id="container"></div>
</template>

<script>
import { Application, Sprite, Texture, Ticker } from 'pixi.js';

export default {
  name: "MainPage",
  data: () => {
    return {
      width: 400,
      height: 600,

      jumping: false,
      actor: null,

      textures: {},
      curr_img: 'left_leg',
    }
  },
  created() {
    // Load left leg and right left texture
    this.textures['left_leg'] = Texture.from(require('../assets/left_leg.png'))
    this.textures['right_leg'] = Texture.from(require('../assets/right_leg.png'))
    this.textures['typo_title'] = Texture.from(require('../assets/typo_title.png'))
  },
  mounted() {

    this.game = new Application({ width: this.width, height: this.height, backgroundColor: '#eeeeee' })
    this.container = document.querySelector('#container');

    this.container.appendChild(this.game.view);

    // Spawn Actors
    let actor = new Sprite(this.textures[this.curr_img]);
    actor.x = 120
    actor.y = 330
    actor.width = 140;
    actor.height = 200;
    this.actor = actor
    this.game.stage.addChild(this.actor)

    // Spawn Game Title
    let title = new Sprite(this.textures['typo_title'])
    title.x = 50
    title.y = 100
    title.width = 300;
    title.height = 60;
    this.game.stage.addChild(title)


    this.game.view.addEventListener('click', this.jump);
    this.game.view.addEventListener('touchend', this.jump);
    this.game.view.addEventListener('keypress', this.jump);
  },
  methods: {
    jump: function() {
      let self = this
      if (self.jumping) return;
      self.jumping = true;

      let gravity = 1
      let power = 15
      let time = 0
      let jumpAt = self.actor['y'];

      let prev_height = null
      let already_changed = false


      const tick = deltaMs => {
        const jumpHeight = (-gravity / 2) * Math.pow(time, 2) + power * time;


        if (jumpHeight < 0) {
          self.jumping = false;
          Ticker.shared.remove(tick);
          self.actor['y'] = jumpAt;
          return;
        }

        self.actor['y'] = jumpAt + (jumpHeight * -1);

        if (already_changed == false && prev_height != null && (self.actor['y'] < prev_height)) {
          self.toggleImage()
          already_changed = true
        }
        prev_height = self.actor['y']

        time += deltaMs;
      }

      Ticker.shared.add(tick);
    },
    toggleImage: function() {
      if(this.curr_img == 'left_leg') {
        this.curr_img = 'right_leg'
      } else {
        this.curr_img = 'left_leg'
      }

      console.log(this.curr_img)
      if(this.actor != null) {
        this.actor.texture = this.textures[this.curr_img]
      }
    },
  }
}
</script>

<style scoped>
* { margin: 0; border: 0; padding: 0; }
html { width: 100%; height: 100%; }
body { min-height: 100%; background-color: #f7f7f7; display: flex; justify-content: center; align-items: center; }
</style>