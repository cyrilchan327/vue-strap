<template>
  <li v-if="$parent.navbar||$parent.menu||$parent._tabset" v-el:dropdown class="dropdown {{disabled&&'disabled'}}" :class="classes">
      <a v-if="text" v-el:btn class="dropdown-toggle" role="button" :class="{disabled: disabled}"
        @click="toggle()"
        @blur="blur()"
        @keyup.esc="show = false"
      >
        {{ text }}
        <span class="caret"></span>
      </a>
      <button type="button" class="secret" v-el:btn
        @click="toggle()"
        @blur="blur()"
        @keyup.esc="show = false"
        :disabled="disabled"
      ></button>
      <slot v-else name="button"></slot>
    <slot v-if="slots['dropdown-menu']" name="dropdown-menu"></slot>
    <ul v-else class="dropdown-menu">
      <slot></slot>
    </ul>
  </li>
  <div v-else v-el:dropdown class="btn-group" :class="classes">
      <button v-if="text" v-el:btn type="button" class="btn btn-{{type||'default'}} dropdown-toggle"
        @click="toggle()"
        @blur="blur"
        @keyup.esc="show = false"
        :disabled="disabled"
      >
        {{ text }}
        <span class="caret"></span>
      </button>
      <slot v-else name="button"></slot>
    <slot v-if="slots['dropdown-menu']" name="dropdown-menu"></slot>
    <ul v-else class="dropdown-menu">
      <slot></slot>
    </ul>
  </div>
</template>
<script>
import coerceBoolean from './utils/coerceBoolean.js'

export default {
  props: {
    show: {
      twoWay: true,
      type: Boolean,
      coerce: coerceBoolean,
      default: false
    },
    'class': null,
    disabled: {
      type: Boolean,
      coerce: coerceBoolean,
      default: false
    },
    text: {
      type: String,
      default: null
    },
    type: {
      type: String,
      default: null
    }
  },
  computed: {
    classes () {
      return [{open: this.show}, this.class]
    },
    button () {
      if (this.text) return this.$els.btn
      return this.$els.dropdown.querySelector('[data-toggle="dropdown"]')
    },
    menu () {
      return !this.$parent || this.$parent.navbar
    },
    submenu () {
      return this.$parent && (this.$parent.menu || this.$parent.submenu)
    },
    slots () {
      return this._slotContents
    }
  },
  methods: {
    blur () {
      this._hide = setTimeout(() => {
        this._hide = null
        this.show = false
      }, 100)
    },
    unblur () {
      if (this._hide) {
        clearTimeout(this._hide)
        this._hide = null
      }
    },
    focus () {
      this.button.focus()
    },
    toggle (e) {
      if (e) e.preventDefault()
      if (this.disabled) { return }
      this.show = !this.show
      this.focus()
      this.unblur()
    }
  },
  ready () {
    const el = this.$els.dropdown
    if (!this.text) {
      const toggle = el.querySelector('[data-toggle="dropdown"]')
      if (toggle) {
        toggle.addEventListener('click', this.toggle)
        toggle.addEventListener('blur', this.blur)
      }
    }
    el.querySelector('ul').addEventListener('click', (e) => {
      if (e.target.nodeName.toLowerCase() === 'a') this.blur()
    })
  }
}
</script>

<style scoped>
.secret {
  position: absolute;
  clip: rect(0 0 0 0);
  overflow: hidden;
  margin: -1px;
  height: 1px;
  width: 1px;
  padding: 0;
  border: 0;
}
</style>