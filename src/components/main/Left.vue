<template>
  <div class="left">
    <!-- 个人简介 -->
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>个人简介</span>
      </div>
      <div v-for="info in infos" class="text item">
        {{ info }}
      </div>
      <div class="text item">
        博客园：<a href='https://blog.csdn.net/Spider_wang' style="color:#009688;">SpiderWang的博客</a>
      </div>
      <div class="text item">
        博客：<a href='http://www.wangdunwen.com' style="color:#009688;">个人博客</a>
      </div>
    </el-card>
    <br/><br/>

    <!-- 文章列表 -->
    <div v-for="(item,index) in getArticles" :key="index">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>{{ index }}文章列表</span>
        </div>
        <div class="text item" v-for="article in getArticles[index]">
          <transition>
            <router-link :to="{ name: 'main', query: { article: article.fileName }}" style="color:#009688;">
              {{article.title}} <br/>{{article.createdTime}}
            </router-link>
          </transition>
        </div>
        <p align='right' style="margin-bottom:-1rem;">
          <el-button type="text" style="color:#009688;" @click="more(index)">更多</el-button>
        </p>
      </el-card>
      <br/><br/>
    </div>

    <div style="display:flex;width:100%;justify-content:center;margin-top:50px;">
      <img src="static/images/earth.gif"/>
    </div>
  </div>
</template>

<script>
import markdowns from '../../assets/js/markdowns.js';

export default {
  name: 'left',
  data () {
    return {
      infos: [
        "姓名：王敦文",
        "性别：男",
        "GitHub：@wangdunwen",
        "职业：前端工程师、Web GIS开发",
        "爱好：足球，篮球，旅游等",
        "签名：enjoy life！"
      ]
    }
  },
  mounted() { 

  },
  methods: {
    more(type) {
      // console.log(type);
      switch(type) {
        case 'Cesium': this.$router.push({name: 'page', query: {type: type}});;break;
        case 'JavaScript': this.$router.push({name: 'page', query: {type: type}});;break;
        case 'Git': this.$router.push({name: 'page', query: {type: type}});;break;
      }
    }
  },
  computed: {
    getArticles: function () {
      let articleCaches = {};

      for (let article of markdowns.articles) {
        let hasTag = false;

        for (let tag in articleCaches) {
          if (tag === article.type) {
            hasTag = true;
            articleCaches[tag].push(article);
          }
        }

        if (!hasTag) {
          articleCaches[article.type] = [article];
        }
      }

      for (let tag in articleCaches) {
        let tmp = articleCaches[tag];

        articleCaches[tag] = tmp.length < 5 ? tmp : tmp.slice(tmp.length - 5, tmp.length).reverse();
      }

      return articleCaches;
    }
  }
}
</script>

<style scoped>
.left {
    
}

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
</style>