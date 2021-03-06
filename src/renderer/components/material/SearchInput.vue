<template lang="pug">
div(:class="[$style.search, focus ? $style.active : '', big ? $style.big : '', small ? $style.small : '']")
  div(:class="$style.form")
    input(:placeholder="placeholder" v-model.trim="text"
          @focus="handleFocus" @blur="handleBlur" @input="$emit('input', text)"
          @change="sendEvent('change')"
          @keyup.enter="handleSearch")
    button(type="button" @click="handleSearch")
      slot
        svg(version='1.1' xmlns='http://www.w3.org/2000/svg' xlink='http://www.w3.org/1999/xlink' height='100%' viewBox='0 0 30.239 30.239' space='preserve')
          use(xlink:href='#icon-search')
  //- transition(name="custom-classes-transition"
  //-             enter-active-class="animated flipInX"
  //-             leave-active-class="animated flipOutX")
  div(v-if="list" :class="$style.list" :style="listStyle")
    ul(ref="dom_list")
      li(v-for="(item, index) in list" :key="item" @click="handleTemplistClick(index)")
        span {{item}}
</template>

<script>
export default {
  props: {
    placeholder: {
      type: String,
      default: 'Search for something...',
    },
    list: {
      type: Array,
    },
    visibleList: {
      type: Boolean,
      default: false,
    },
    value: {
      type: String,
      default: '',
    },
    big: {
      type: Boolean,
      default: false,
    },
    small: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      isShow: false,
      text: '',
      index: null,
      focus: false,
      listStyle: {
        height: 0,
      },
    }
  },
  watch: {
    list(n) {
      if (!this.visibleList) return
      this.$nextTick(() => {
        this.listStyle.height = this.$refs.dom_list.scrollHeight + 'px'
      })
    },
    value(n) {
      this.text = n
    },
    visibleList(n) {
      n ? this.showList() : this.hideList()
    },
  },
  methods: {
    handleTemplistClick(index) {
      this.sendEvent('listClick', index)
    },
    handleFocus() {
      this.focus = true
      this.sendEvent('focus')
    },
    handleBlur() {
      this.focus = false
      this.sendEvent('blur')
    },
    handleSearch() {
      this.hideList()
      this.sendEvent('submit')
    },
    showList() {
      this.isShow = true
      this.listStyle.height = this.$refs.dom_list.scrollHeight + 'px'
    },
    hideList() {
      this.isShow = false
      this.listStyle.height = 0
    },
    sendEvent(action, data) {
      this.$emit('event', {
        action,
        data,
      })
    },
  },
}
</script>


<style lang="less" module>
@import '../../assets/styles/layout.less';

.search {
  border-radius: 3px;
  transition: box-shadow .4s ease, background-color @transition-theme;
  display: flex;
  flex-flow: column nowrap;
  width: 240px;
  background-color: @color-search-form-background;

  &.active {
    box-shadow: 0 1px 5px 0 rgba(0,0,0,.2);
    .form {
      input {
        border-bottom-left-radius: 0;
      }
      button {
        border-bottom-right-radius: 0;
      }
    }
  }
  .form {
    display: flex;
    height: @height-toolbar / 2;
    position: relative;
    input {
      flex: auto;
      // border: 1px solid;
      border-top-left-radius: 3px;
      border-bottom-left-radius: 3px;
      background-color: transparent;
      // border-bottom: 2px solid @color-theme;
      // border-color: @color-theme;
      border: none;

      outline: none;
      // height: @height-toolbar * .7;
      padding: 0 5px;
      overflow: hidden;
      &::placeholder {
        color: @color-btn;
      }
    }
    button {
      flex: none;
      border: none;
      // background-color: @color-search-form-background;
      background-color: transparent;
      outline: none;
      border-top-right-radius: 3px;
      border-bottom-right-radius: 3px;
      cursor: pointer;
      height: 100%;
      padding: 5px 7px;
      color: @color-btn;
      transition: background-color .2s ease;

      &:hover {
        background-color: @color-theme-hover;
      }
      &:active {
        background-color: @color-theme-active;
      }
    }
  }
  .list {
    // background-color: @color-search-form-background;
    font-size: 13px;
    transition: .3s ease;
    height: 0;
    transition-property: height;
    overflow: hidden;
    li {
      cursor: pointer;
      padding: 8px 5px;
      transition: background-color .2s ease;
      line-height: 1.3;
      span {
        .mixin-ellipsis-2;
      }

      &:hover {
        background-color: @color-search-list-hover;
      }
      &:last-child {
        border-bottom-left-radius: 3px;
        border-bottom-right-radius: 3px;
      }
    }
  }
}

.big {
  width: 500px;
  // input {
  //   line-height: 30px;
  // }
  .form {
    height: 30px;
    button {
      padding: 6px 10px;
    }
  }
}

each(@themes, {
  :global(#container.@{value}) {

    .search {
      background-color: ~'@{color-@{value}-search-form-background}';
      &.active {
        box-shadow: 0 1px 5px 0 rgba(0,0,0,.2);
      }

      .form {
        input {
          &::placeholder {
            color: ~'@{color-@{value}-btn}';
          }
        }
        button {
          color: ~'@{color-@{value}-btn}';

          &:hover {
            background-color: ~'@{color-@{value}-theme-hover}';
          }
          &:active {
            background-color: ~'@{color-@{value}-theme-active}';
          }
        }
      }
      .list {
        li {
          &:hover {
            background-color: ~'@{color-@{value}-search-list-hover}';
          }
        }
      }
    }
  }
})

</style>
