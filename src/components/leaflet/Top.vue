<template>
    <div class="top">
        <mu-appbar title="Leaflet" v-show="appbarShow">
            <mu-icon-button slot="left" @click="takeBack">
                <mu-icon value="keyboard_arrow_left" :size="24"/>
            </mu-icon-button>
            <mu-text-field icon="search" class="appbar-search-field"  slot="right" hintText="请输入搜索内容"/>
            <mu-icon-menu
                icon="more_vert"
                :anchorOrigin="leftTop"
                :targetOrigin="leftTop" slot="right">
                <mu-menu-item title="test1" />
                <mu-menu-item title="test2" />
                <mu-menu-item title="test3" />
                <mu-menu-item title="test4" />
                <mu-menu-item title="test5" />
            </mu-icon-menu>
            <mu-icon-button icon="menu" slot="right" @click="toggle(true)"/>
        </mu-appbar>
        <!-- 底部返回按钮 -->
        <mu-avatar icon="keyboard_arrow_right" color="white" backgroundColor="#009688" :size="50" 
        style="margin-top:10px;cursor:pointer;" :iconSize="24" v-show="!appbarShow" @click="takeBack"/> 
        <!-- 抽屉 -->
        <mu-drawer right :open="drawOpen" :docked="docked" @close="toggle()" style="background-color: #B0E2FF;opacity: 0.9;">
            <mu-list @itemClick="docked ? '' : toggle()">
                <mu-list-item title="Menu Item 1"/>
                <mu-list-item title="Menu Item 2"/>
                <mu-list-item title="Menu Item 3"/>
                <mu-list-item v-if="docked" @click.native="open = false" title="Close"/>
            </mu-list>
        </mu-drawer>
    </div>    
</template>

<script>
export default {
  name: 'top',
  data () {
      return {
          activeIndex: '1',
          appbarShow: false,
          leftTop: {horizontal: 'left', vertical: 'top'},
          drawOpen: false,
          docked: true
      }
  },
  methods: {
      handleSelect(key, keyPath) {
          console.log(key, keyPath);
      },
      takeBack() {
          this.appbarShow = !this.appbarShow;
      },
      toggle(flag) {
          this.drawOpen = !this.drawOpen
          this.docked = !flag
      }
  },
  mounted() { 
      
  }
}
</script>

<style lang='less' scoped>
    .top {
        position: absolute;
        width: 100%;
        display: flex;
        flex-direction: align-self;
        z-index: 1000;
    }

    .left {
        position: absolute;
        top: 10%;
        height: 10%;
        width: 50px;
    }

    .appbar-search-field{
        color: #FFF;
        margin-bottom: 0;
        &.focus-state {
          color: #FFF;
        }
        .mu-text-field-hint {
          color: fade(#FFF, 54%);
        }
        .mu-text-field-input {
          color: #FFF;
        }
        .mu-text-field-focus-line {
          background-color: #FFF;
        }
    }
</style>