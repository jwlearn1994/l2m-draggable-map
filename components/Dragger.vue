<template>
  <div
    :class="['dragger', { draggable }]"
    :style="style"
    draggable
    @mousedown.stop="dragStart"
    @mousemove.prevent.stop="dragMove"
    @mouseup="dragEnd"
    @mouseleave="dragEnd"
    @touchstart.stop="dragStart"
    @touchmove.prevent.stop="dragMove"
    @touchend="dragEnd"
  >
    <slot></slot>
    <span
      class="resizer"
      ref="resizer"
      v-if="resizable"
      @mousedown.stop="resizeStart"
      @mousemove.prevent.stop="resizeMove"
      @mouseup="resizeEnd"
      @mouseleave="resizeEnd"
      @touchstart.stop="resizeStart"
      @touchmove.prevent.stop="resizeMove"
      @touchend="resizeEnd"
    ></span>
    <span
      v-if="deletable"
      class="deleter"
      ref="deleter"
      @dblclick.stop="$emit('delete', $event)"
    ></span>
  </div>
</template>

<script>
export default {
  name: 'Dragger',
  props: {
    draggable: {
      type: Boolean,
      default: true,
    },
    deletable: {
      type: Boolean,
      default: true,
    },
    resizable: {
      type: Boolean,
      default: true,
    },
    map: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['delete'],
  data() {
    return {
      dragging: false,
      resizing: false,
      parentStyle: {
        width: 0,
        height: 0,
      },
      style: {},
    };
  },

  created() {
    // non-reactive data
    this.offset = {};
    this.tempStyle = {};
    // map limit
    if (this.map) {
      this.x_limit = 0;
      this.y_limit = 0;
    }
  },

  methods: {
    getClient(e) {
      if (e.clientX) {
        return { x: e.clientX, y: e.clientY };
      }
      const touch = e.touches?.[0] || {};
      return {
        x: touch.clientX,
        y: touch.clientY
      };
    },
    getElStyle(el, pos = 'parent') {
      if (pos === 'parent') {
        const style = window.getComputedStyle(el, null);
        return {
          style,
          left: parseInt(style.getPropertyValue('left'), 10),
          top: parseInt(style.getPropertyValue('top'), 10),
        };
      } else if (pos === 'screen') {
        const style = el.getBoundingClientRect();
        return {
          style,
          left: style.left,
          top: style.top,
        };
      }
    },
    dragStart(ev) {
      const vm = this;
      if (!vm.draggable) return;
      const style = vm.getElStyle(vm.$el, 'parent');
      const { style: parentStyle } = vm.getElStyle(vm.$el.parentNode, 'parent');
      const client = vm.getClient(ev);
      vm.dragging = true;
      // set offset for use later
      vm.offset = {
        x: style.left - client.x,
        y: style.top - client.y,
      };
      // set parentStyle for use later
      vm.parentStyle = parentStyle;
      // set map limit
      if (vm.map) {
        const windowWidth = parseInt(parentStyle.width);
        const windowHeight = parseInt(parentStyle.height);
        vm.x_limit = -(parseInt(style.style.width) - windowWidth) / windowWidth * 100;
        vm.y_limit = -(parseInt(style.style.height) - windowHeight) / windowHeight * 100;
      }
    },
    dragMove(ev) {
      if (this.dragging) {
        const vm = this;
        const { offset, parentStyle, style, x_limit, y_limit } = vm;
        const client = vm.getClient(ev);
        const pWidth = parseInt(parentStyle.width);
        const pHeight = parseInt(parentStyle.height);
        const newLeft = (client.x + parseInt(String(offset.x), 10)) * 100 / pWidth;
        const newTop = (client.y + parseInt(String(offset.y), 10)) * 100 / pHeight;
        vm.style = {
          ...style,
          ...(vm.map ? {
            left: newLeft > 0
              ? 0
              : newLeft > x_limit
                ? newLeft + '%'
                : x_limit + '%',
            top: newTop > 0
              ? 0
              : newTop > y_limit
                ? newTop + '%'
                : y_limit + '%',
          } : {
            left: newLeft + '%',
            top: newTop + '%',
          })
        };
      }
    },
    dragEnd() {
      this.dragging = false;
    },
    resizeStart() {
      const vm = this;
      vm.resizing = true;
      vm.tempStyle = vm.getElStyle(vm.$el, 'screen');
    },
    resizeMove(ev) {
      if (this.resizing) {
        const vm = this;
        const { parentStyle, tempStyle, style } = vm;
        const client = vm.getClient(ev);
        const pWidth = parseInt(parentStyle.width);
        const pHeight = parseInt(parentStyle.height);
        vm.style = {
          ...style,
          width: (client.x - parseInt(tempStyle.left, 10)) * 100 / pWidth + '%',
          height: (client.y - parseInt(tempStyle.top, 10)) * 100 / pHeight + '%',
        };
      }
    },
    resizeEnd() {
      this.resizing = false;
    },

    reset() {
      this.style = {};
    },
  },
};
</script>

<style lang="scss">
.dragger {
  position: absolute;
  background-clip: padding-box;
  font-size: 12px;

  &.draggable {
    border: 1px dashed #bbb;
    cursor: move;
  }

  .resizer {
    position: absolute;
    display: none;
    right: 0;
    bottom: 0;
    width: 13px;
    height: 13px;
    border: 1px solid;
    background: #000;
    transform: translate(50%, 50%);
    cursor: se-resize;
  }

  .deleter {
    position: absolute;
    display: none;
    right: 0;
    top: 0;
    width: 10px;
    height: 10px;
    border: 1px solid red;
    background: red;
    transform: translate(50%, -50%);
    cursor: pointer;
  }

  &.active {
    border-color: #007bff;
    border-width: 3px;
  }

  &:hover {
    border-color: #000;
    > .resizer,
    > .deleter {
      display: block;
    }
  }
}
</style>