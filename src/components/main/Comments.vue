<template>
    <mu-card style="margin-top: 50px;width: 100%;" id='current_comment'>
        <mu-card-header title="添加新评论" style="display:flex;flex-direction:row;justify-content: space-between;">
            <span style="cursor:pointer;color:grey;" @click="cancel_reply" v-show="show_reply">取消回复</span>
        </mu-card-header>
        <mu-card-text style="display:flex;flex-direction:row;flex-wrap:wrap;">
            <div class='commentName'>
                昵称<span style="color:red">*</span>
                <el-input placeholder="请输入昵称" v-model="input_name"></el-input>
            </div>
            <div class='commentEmail'>
                邮箱<span style="color:red">*</span>
                <el-input placeholder="请输入邮箱" type="email" v-model="input_email"></el-input>
            </div>
            <div class='commentWebsite'>
                网站
                <el-input placeholder="请输入网址" v-model="input_website"></el-input>
            </div>
            <div style="width: 100%;">
                <br/>
                内容<span style="color:red">*</span>
                <el-input
                  type="textarea"
                  :autosize="{ minRows: 5, maxRows: 5}"
                  placeholder="请输入内容"
                  v-model="input_comments">
                </el-input>
                <br/>
                <br/>
            </div>
            <el-button type="primary" @click="submit_comment">提交</el-button>
        </mu-card-text>
        <mu-card-text style="width: 100%;">
            <div v-for="comment in comments" style="margin-top: 20px;">
                <el-card class="box-card" style="width: 100%;">
                    <mu-list-item :title="comment.name" disabled>
                        <mu-avatar slot="left" v-if="comment.email === 'dwwang@mail.ie.ac.cn'" src="https://s.gravatar.com/avatar/29fd983b2f7704afbd9029121d8fb7a4?s=80"/>
                        <mu-avatar slot="left" v-else icon="person"/>
                    </mu-list-item>
                    <mu-card-text>{{ comment.comments }}</mu-card-text>
                    <mu-card-text style="font-size:12px;">
                        {{ comment.time }}&nbsp;&nbsp;&nbsp;
                        <i class="el-icon-edit-outline" style="cursor:pointer;" @click="reply(comment)">&nbsp;回复</i>
                    </mu-card-text>
                    <!-- 如果有子评论 -->
                    <div v-if="comment.child.length > 0">
                        <el-card class="box-card" style="width: 100%;background-color:#EDEDED;margin-top:10px;" v-for="(child, index) in comment.child" :key="index">
                            <mu-list-item :title="child.name + ' ' + (index+1) + '楼'" disabled color="blue">
                                <mu-avatar slot="left" v-if="child.email === 'dwwang@mail.ie.ac.cn'" 
                                src="https://s.gravatar.com/avatar/29fd983b2f7704afbd9029121d8fb7a4?s=80"/>
                                <mu-avatar slot="left" v-else icon="person"/>
                            </mu-list-item>
                            <mu-card-text><span style="color:blue;">@{{ child.target }} </span>&nbsp; {{ child.comments }}</mu-card-text>
                            <mu-card-text style="font-size:12px;">
                                {{ child.time }}&nbsp;&nbsp;&nbsp;
                                <i class="el-icon-edit-outline" style="cursor:pointer;" @click="reply(comment, child.name)">&nbsp;回复</i>
                            </mu-card-text>
                        </el-card>
                    </div>
                </el-card>
            </div>
        </mu-card-text>
    </mu-card>
</template>

<script>
import axios from 'axios';

export default {
  name: 'comments',
  data () {
    return {
        input_name: "",
        input_email: "",
        input_website: "",
        input_comments: "",
        show_reply: false,
        comments: [
        ],
        replyInfo: {
            target: "",
            id: ""
        },
        type: ''
    }
  },
  methods: {
      submit_comment() {
          if(this.input_name === "") {
              this.$notify.error({
                  title: '错误',
                  message: '请输入昵称！'
              });
              return;
          }
          else if(this.input_email === "") {
              this.$notify.error({
                  title: '错误',
                  message: '请输入邮箱！'
              });
              return;
          }
          else if(this.input_comments === "") {
              this.$notify.error({
                  title: '错误',
                  message: '评论内容不能为空！'
              });
              return;
          }
          else if(!this.isEmail(this.input_email)) {
              this.$notify.error({
                  title: '错误',
                  message: '非法的邮箱格式！'
              });
              return;
          }

          let current_time = new Date();
          let id = 'wdw_comment_' + current_time.getTime();

          let comment = {
              name: this.input_name,
              email: this.input_email,
              comments: this.input_comments,
              website: this.input_website,
              time: current_time.getTime(),
          }

          if(this.replyInfo.target !== "" && this.replyInfo.id !== "") {
              comment.target = this.replyInfo.target;
              for(let cm of this.comments) {
                  if(cm.id === this.replyInfo.id) {
                      cm.child.push(comment);

                      axios.post('http://101.201.232.4/github.io/updateChild', cm)
                          .then((response) => {
                              // 插入评论列表
                              let date = new Date();
                              date.setTime(comment.time);
                              comment.time = date.toLocaleString();
                              console.log(response.data);
                          }).catch((error) => {
                              console.log(error);
                          })
                      this.show_reply = false;
                      this.replyInfo.target = "";
                      this.replyInfo.id = "";
                      break;
                  }
              }
          }else {
              comment.id = id;
              comment.child = [];
              comment.type = this.type;

              axios.post('http://101.201.232.4/github.io/comment', comment)
                  .then((response) => {

                      // 插入评论列表
                      let date = new Date();
                      date.setTime(comment.time);
                      comment.time = date.toLocaleString();
                      this.comments.push(comment);
                  }).catch((error) => {
                      console.log(error);
                  })
          }

          this.$notify({
              title: '成功',
              message: '评论成功！',
              type: 'success'
          });

          // 清空表格，昵称、邮箱、网站不需要重置
          // this.input_name = "";
          // this.input_email = "";
          this.input_comments = "";
          // this.input_website = "";
      },
      isEmail(str) {
          return /^(\w+)(\.\w+)*@(\w+)(\.\w+)*.(\w+)$/i.test(str);
      },
      reply(comment, name) {
          document.querySelector("#current_comment").scrollIntoView();

          this.show_reply = true;

          if(name === undefined) {
              this.replyInfo.target = comment.name;
              this.replyInfo.id = comment.id;
          }else {
              this.replyInfo.target = name;
              this.replyInfo.id = comment.id;
          }
      },
      cancel_reply() {
          this.show_reply = false;
          this.replyInfo.target = "";
          this.replyInfo.id = "";
      },
      updateComments() {
          this.comments = [];
          if(this.$route.query.hasOwnProperty('article')) {
              this.type = this.$route.query.article;
          }else {
              this.type = "main";
          }

          axios.get('http://101.201.232.4/github.io/getComments')
              .then((response) => {
                  // console.log(response);
                  for(let comment of response.data) {
                      if(comment.type == this.type) {
                          comment.child = JSON.parse(comment.child);
                          let date = new Date();
                          date.setTime(comment.time);
                          comment.time = date.toLocaleString();

                          if(comment.child.length !== 0) {
                              for(let child of comment.child) {
                                  let date1 = new Date();
                                  date1.setTime(child.time);
                                  child.time = date1.toLocaleString();
                              }
                          }
                          this.comments.push(comment);
                      }
                  }
              }).catch((error) => {
                  console.log(error);
              });
      },
      updateLogin() {
          this.input_name = this.$store.state.login.name
          this.input_email= this.$store.state.login.email
          this.input_website = this.$store.state.login.website
      }
  },
  watch: {
      // 如果路由有变化，会再次执行该方法
      '$route': 'updateComments',
      '$store.state.login.name': 'updateLogin'
  },
  mounted() { 
      this.updateComments();
  },
  computed: {
    // getName: function() {
    //     return this.$store.state.login.name;
    // },
    // getEmail: function() {
    //     return this.$store.state.login.email;
    // },
    // getWebsite: function() {
    //     return this.$store.state.login.website;
    // }
  }
}
</script>

<style scoped>
    .text {
        font-size: 14px;
    }

    .item {
        margin-bottom: 18px;
    }

    .clearfix:before,
    .clearfix:after {
        display: table;
        content: "";
    }
    .clearfix:after {
        clear: both
    }

    .box-card {
        width: 100%;
    }

    .commentName {
        width: 30%;
        margin-right:5%;
    }

    .commentEmail {
        width: 30%;
        margin-right:5%;
    }

    .commentWebsite {
        width: 30%;
    }

    @media only screen and (max-width: 600px) {
        .commentName {
            width: 100%;
            margin-right: 0%;
        }

        .commentEmail {
            width: 100%;
            margin-right: 0%;
        }

        .commentWebsite {
            width: 100%;
        }
    }
</style>