<template>
  <div
    class="ds-avatar"
    :class="[
      `ds-size-${this.size}`,
      online && 'is-online'
    ]"
    :style="styles"
  >
    <ds-flex
      v-if="!hasImage || error"
      style="height: 100%">
      <ds-flex-item centered>
        <template v-if="isAnonymus">
          <ds-icon name="eye-slash" />
        </template>
        <template v-else>
          {{ userInitials }}
        </template>
      </ds-flex-item>
    </ds-flex>
    <img
      v-if="image && !error"
      :src="image"
      @error="onError"
    >
  </div>
</template>

<script>
import helpers from './lib/helpers.js'
import { tokens } from '@@/tokens'
import camelCase from 'lodash/camelCase'
import upperFirst from 'lodash/upperFirst'

export default {
  name: 'DsAvatar',
  props: {
    backgroundColor: { type: String, default: null },
    name: { type: String, default: 'Anonymus' },
    /**
     * The size used for the avatar.
     * @options small|base|large
     */
    size: {
      type: String,
      default: 'base',
      validator: value => {
        return value.match(/(small|base|large|x-large)/)
      }
    },
    image: { type: String, default: null },
    online: { type: Boolean, default: false }
  },
  data() {
    return {
      error: false
    }
  },
  computed: {
    isAnonymus() {
      return !this.name || this.name.toLowerCase() === 'anonymus'
    },
    styles() {
      let size = this.sizeValue
      if (Number.isInteger(Number(size))) {
        size = `${size}px`
      }
      const nummericSize = Number.parseInt(size)
      return {
        width: size,
        height: size,
        backgroundColor: this.hasImage ? 'white' : this.background,
        fontSize: Math.floor(nummericSize / 2.5) + 'px',
        fontWeight: 'bold',
        color: this.fontColor
      }
    },
    sizeValue() {
      return tokens[`sizeAvatar${upperFirst(camelCase(this.size))}`]
    },
    hasImage() {
      return Boolean(this.image) && !this.error
    },
    userInitials() {
      return helpers.initials(this.name)
    },
    background() {
      return (
        this.backgroundColor ||
        helpers.randomBackgroundColor(
          this.name.length,
          helpers.backgroundColors
        )
      )
    },
    fontColor() {
      return this.color || helpers.lightenColor(this.background, 200)
    }
  },
  methods: {
    onError() {
      this.error = true
    },
    updateSize() {
      if (this.hasImage) {
        return
      }
      try {
        this.size = this.$refs.avatar.getBoundingClientRect().width
      } catch (err) {
        // nothing
      }
    }
  }
}
</script>


<style lang="scss" src="./style.scss">
</style>

<docs src="./demo.md"></docs>
