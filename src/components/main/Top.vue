<template>
  <div>
    <div class="top">
      <mu-appbar title="wangdunwen's pages">
        <mu-icon-button icon="menu" slot="left" @click="toggle(true)" class='pageList'/>
        <mu-icon-button icon="home" slot="left" @click="home"/>
        <mu-text-field icon="search" class="appbar-search-field" slot="right" hintText="请输入搜索内容" style="color:white;"/>
        <mu-icon-button slot="right" @click="showLogin">
          <mu-avatar icon="person" :size="28" :iconSize="24" color="grey" backgroundColor="white" v-show="login_avatar"/>
          <mu-avatar src="https://s.gravatar.com/avatar/29fd983b2f7704afbd9029121d8fb7a4?s=80" 
          :size="28" :iconSize="24" color="grey" backgroundColor="white" v-show="admin_avatar"/>
        </mu-icon-button>
      </mu-appbar>
    </div>

    <mu-dialog :open="dialogLogin" title="登陆" @close="closeLogin">
      <mu-card-text class='inputCard'>
        <el-input placeholder="请输入用户名" v-model="user_name">
          <template slot="prepend">用户名：</template>
        </el-input>
        <el-input placeholder="请输入密码" v-model="user_pwd" style="margin-top: 20px;" type="password">
          <template slot="prepend">密&nbsp;&nbsp;&nbsp;码：</template>
        </el-input>
      </mu-card-text>
      <mu-flat-button slot="actions" @click="closeLogin" primary label="取消"/>
      <mu-flat-button slot="actions" primary @click="login" label="验证"/>
    </mu-dialog>

    <!-- 抽屉 -->
    <mu-drawer :open="drawOpen" :docked="docked" @close="toggle()" style="background-color: white;opacity: 1.0;width: 300px;">
      <mu-list @itemClick="docked ? '' : toggle()">
        <mu-list-item title="<<返回主页" style='color: #009688'/>
          <left style='display:block;'></left>
          <!-- <mu-list-item title="Menu Item 1"/>
          <mu-list-item title="Menu Item 2"/>
          <mu-list-item title="Menu Item 3"/>
          <mu-list-item v-if="docked" @click.native="open = false" title="Close"/> -->
        </mu-list>
    </mu-drawer>
  </div>
</template>

<script>
import left from './Left';

export default {
  name: 'top',
  data () {
    return {
      dialogLogin: false,
      user_name: "",
      user_pwd: "",
      login_avatar: true,
      admin_avatar: false,
      drawOpen: false,
      docked: true
    }
  },
  mounted() { 

  },
  components: {
    left
  },
  methods: {
    showLogin () {
      this.dialogLogin = true;
    },
    closeLogin () {
      this.dialogLogin = false;
    },
    toggle (flag) {
      this.drawOpen = !this.drawOpen
      this.docked = !flag
    },
    login () {
      if (this.user_name !== "" && this.user_pwd !== "") {
        if (this.user_name === "wangdunwen_root" && this.user_pwd === "wdw1992519!") {
          this.login_avatar = false;
          this.admin_avatar = true;
          this.$store.state.login.name = 'wangdunwen';
          this.$store.state.login.email = 'dwwang@mail.ie.ac.cn';
          this.$store.state.login.website = 'http://www.wangdunwen.com';
          this.current_login = "admin";
        }
      } else {
        this.$notify({
          title: '警告',
          message: '请输入用户名或密码！',
          type: 'warning'
        });
      }
      this.dialogLogin = false;
    },
    home () {
      this.$router.push({
        name: 'main'
      });
    }
  }
}
</script>

<style scoped lang='less'>
.top {
  position: absolute;
  width: 100%;
  display: flex;
  flex-direction: align-self;
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

.inputCard {

}

.pageList {
  display: none;
}

@media only screen and (max-width: 650px) {
  .appbar-search-field {
    display: none;
  }

  .inputCard {
    padding: 0px;
  }
}

@media only screen and (max-width: 800px) {
  .pageList {
    display: flex;
    margin-right: -1rem;
    margin-left: -1rem;
  }
}
</style>