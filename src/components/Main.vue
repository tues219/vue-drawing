/* eslint-disable vue/script-indent */
<template>
  <div class="wrapper" :style="{width:width + 'px'}">
    <div class="options">
      <stroke class="kit" :selected="selected['stroke']" @click.native="setSelect('stroke')"></stroke>
      <eraser-control
        class="kit"
        :selected="selected['eraser']"
        @click.native="setSelect('eraser')"
      ></eraser-control>
      <thickness class="kit" @thick-change="changeThick"></thickness>
      <!-- <text-control
        class="kit"
        :selected="selected['text-control']"
        @click.native="setSelect('text-control')"
      ></text-control>-->
      <undo class="kit" @click.native="undo"></undo>
      <clean class="kit" @click.native="cleanCanvas"></clean>
      <export-control class="export" @click.native="exportCanvas"></export-control>
      <color-board class="kit" @color-selected="changeColor"></color-board>
    </div>
    <div class="anchor">
      <textarea ref="textBox" class="text-box"></textarea>
    </div>
    <canvas
      :width="width"
      :height="height"
      id="vue-drawing-main-canvas"
    >We are very sorry that this browser doesn't support canvas.</canvas>
  </div>
</template>

<script>
import VueDrawing from "./js/VueDrawing";
import Stroke from "./kits/stroke";
import EraserControl from "./kits/eraser";
import ColorBoard from "./kits/color-board";
import Thickness from "./kits/thickness";
import Undo from "./kits/undo";
import Clean from "./kits/clean";
import ExportControl from "./kits/export-control";
// import TextControl from "./kits/text-control";
// import TextInput from "./js/TextInput";
import Eraser from "./js/Eraser";

export default {
  name: "vue-drawing",
  components: {
    // TextControl,
    Clean,
    Undo,
    Thickness,
    ColorBoard,
    EraserControl,
    Stroke,
    ExportControl,
  },
  props: {
    // width: {
    //   type: Number,
    //   default: 500,
    // },
    // height: {
    //   type: Number,
    //   default: 300,
    // },
  },
  data() {
    return {
      drawing: null,
      selected: {
        stroke: true,
        eraser: false,
        // "text-control": false,
      },
      // textControlStyle: null,
      // textValue: null,
      // showText: false,
    };
  },
  computed: {
    width() {
      if (window.innerWidth < window.innerHeight) {
        return window.innerWidth * 0.8;
      } else {
        return window.innerHeight * 0.8;
      }
    },
    height() {
      return this.width;
    },
  },
  methods: {
    setSelect(name) {
      for (let i in this.selected) {
        this.selected[i] = i === name;
      }
      this[name]();
    },
    stroke() {
      this.drawing.style.strokeWidth = 2;
      this.drawing.restoreTool();
    },
    eraser() {
      this.drawing.style.strokeWidth = 15;
      let eraser = new Eraser();
      this.drawing.use(eraser);
    },
    // "text-control"() {
    //   let textInput = new TextInput(this.$refs.textBox);
    //   this.drawing.use(textInput);
    // },
    changeColor(color) {
      this.drawing.style.strokeColor = color;
    },
    changeThick(thick) {
      this.drawing.style.strokeWidth = thick;
    },
    undo() {
      let length = this.drawing.history.container.length;
      console.log("length", length);
      if (length > 0) {
        this.drawing.history.container[length - 1].removeSegments();
        this.drawing.history.container.pop();
      }
    },
    cleanCanvas() {
      this.drawing.clean();
    },
    exportCanvas() {
      this.$emit(
        "image-export",
        this.drawing.project.exportSVG({ asString: true })
      );
      // console.log(this.drawing.project.exportSVG({ asString: true }));
    },
  },
  mounted() {
    this.drawing = new VueDrawing("vue-drawing-main-canvas");
  },
};
</script>

<style scoped lang="scss">
.wrapper {
  // width: 500px;
  // position: relative;
  // margin: 0 auto;
}

.options {
  overflow: hidden;
}

#vue-drawing-main-canvas {
  border: solid black 1px;
  cursor: crosshair;
  position: relative;
}

.kit {
  float: left;
  margin: 0 0 1em 1em;
}

.text-box {
  display: none;
  position: absolute;
  width: 0;
  height: 0;
  z-index: 100;
  border: dashed blueviolet 1px;
}

.anchor {
  position: relative;
  width: 0;
  height: 0;
}

.export {
  float: right;
}
</style>
