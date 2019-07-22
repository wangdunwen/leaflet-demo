<template>
  <div class="right">
    <mu-card v-if="!$route.query.hasOwnProperty('article')">
      <mu-card-header title="wangdunwen" subTitle="项目展示">
        <mu-avatar src="https://s.gravatar.com/avatar/29fd983b2f7704afbd9029121d8fb7a4?s=80" slot="avatar"/>
      </mu-card-header>
      <mu-card-media title="Punk Hazrd" subTitle="Dragon">
        <img src="static/images/pank.jpg" />
      </mu-card-media>

      <mu-card-text>
        2017/12/24<br/>
        近一年半一直在做基于Web GIS的前端开发，用过很多框架，如OpenLayers3、Leaflet、Cesium等开源框架，
        并经历了从jquery+require.js+r.js开发 -> react+webpack重构项目 -> angular+webpack重构项目，
        最后稳定到现在通过vue+webpack进行项目开发。<br/><br/>下面是两个在Vue框架上基于Cesium和基于Leaflet的Demo。
      </mu-card-text>
      <mu-card-actions style="display:flex;align-items:center;flex-direction:row;justify-content: center;">
        <div @click="turnToCesium" class="demo-show">
          <mu-paper class="demo-paper" circle :zDepth="5" style="background-image: url(static/images/cesium.png);background-size:10rem 10rem;" title='cesium'/>
          <mu-raised-button label="Cesium" class="demo-raised-button" primary/>
        </div>
        <div @click="turnToLeaflet" class="demo-show">
          <mu-paper class="demo-paper" circle :zDepth="5" style="background-image: url(static/images/leaflet.png);background-size:10rem 10rem;"/>
          <mu-raised-button label="Leaflet" class="demo-raised-button" primary/>
        </div>
      </mu-card-actions>

      <br/>

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
    </mu-card>

    <mu-card v-else>
      <mu-card-header title="wangdunwen" :subTitle="article.tags">
        <mu-avatar src="https://s.gravatar.com/avatar/29fd983b2f7704afbd9029121d8fb7a4?s=80" slot="avatar"/>
      </mu-card-header>
      <mu-card-media :title="article.title" :subTitle="article.createdTime">
        <img src="static/images/pank.jpg" />
      </mu-card-media>

      <mu-card-text class="markdownClass">
        <div v-html="compiledMarkdown" v-highlight style="width: 100%;max-width: 750px;" class="markdown-body">
          {{ markdown }}
        </div>

        <!-- <vue-markdown :source="markdown">{{markdown}}</vue-markdown> -->
      </mu-card-text>

      <!-- <mu-card-text>
        <p align="right">created by @wangdunwen</p>
        <p align="right">转载请注明作者及链接</p>
      </mu-card-text> -->
    </mu-card>

    <comments></comments>

    <div class="footer">
      <div>Copyright <mu-icon value="copyright" :size="16" />2018 Designed by wangdunwen.</div>
    </div>
  </div>
</template>

<script>
import comments from './Comments';
// import vueMarkdown from 'vue-markdown';
import Marked from 'marked';
import markdowns from '../../assets/js/markdowns.js';
import hljs from 'highlight.js';
import 'github-markdown-css';

export default {
  name: 'right',
  data () {
    return {
      markdown: "",
      article: {},
      type: ""
    }
  },
  components: {
    comments,
    // vueMarkdown,
    hljs
  },
  methods: {
    turnToCesium() {
      this.$router.replace('/cesium');
    },
    turnToLeaflet() {
      this.$router.replace('/leaflet');
    },
    updateContents() {
      if(this.$route.query.hasOwnProperty('article')) {
        this.type = this.$route.query.article;
        this.article = markdowns.findArticle(this.type);
        this.markdown = markdowns.findMarkdown(this.type);
      }
    },
    markdownSet() {
      Marked.setOptions({
        renderer: new Marked.Renderer(),
        gfm: true,
        tables: true,
        breaks: false,
        pedantic: false,
        sanitize: false,
        smartLists: true,
        smartypants: false
      });
    },
    more (type) {
      switch (type) {
        case 'Cesium': this.$router.push({name: 'page', query: {type: type}});;break;
        case 'JavaScript': this.$router.push({name: 'page', query: {type: type}});;break;
        case 'Git': this.$router.push({name: 'page', query: {type: type}});;break;
      }
    }
  },
  watch: {
      // 如果路由有变化，会再次执行该方法
      '$route': 'updateContents'
  },
  mounted() { 
      this.markdownSet();
      this.updateContents();
      // hljs.highlightCode();
      // hljs.initHighlightingOnLoad();
  },
  computed: {
    compiledMarkdown: function() {
      return Marked(this.markdown, { sanitize: true })
    },
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
.right {
  position: absolute;
  top: 13%;
  left: 340px;
  right: 3%; 
  /*display: flex;
  flex-direction: column;*/
}

.bottom {
  position: absolute;
  left: 300px;
  right: 20px; 
  width: 100%;
  height: 150px;
  background: red;
  display: flex;
  justify-content: center;
}

.footer {
  position: absolute;
  margin-top: 100px;
  width: 100%;
  height: 1.5rem;
  display: flex;
  flex-direction: row;
  /*text-align: center;*/
  justify-content: center;
  color: black;
  /*align-items: center;*/
}

.demo-paper {
  display: inline-block;
  height: 10rem;
  width: 10rem;
  margin: 3.5rem;
  text-align: center;
  cursor: pointer;
} 

.demo-show {
  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;
}

.content {
  padding: 10px;
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
  display: none;
}

.markdownClass {
  display: flex;
  flex-direction: row;
  justify-content: center;
  width: 100%;
  align-items: center;
}

html, body, #editor {
  margin: 0;
  height: 100%;
  font-family: 'Helvetica Neue', Arial, sans-serif;
  color: #333;
}

textarea, #editor div {
  display: inline-block;
  width: 49%;
  height: 100%;
  vertical-align: top;
  box-sizing: border-box;
  padding: 0 20px;
}

textarea {
  border: none;
  border-right: 1px solid #ccc;
  resize: none;
  outline: none;
  background-color: #f6f6f6;
  font-size: 14px;
  font-family: 'Monaco', courier, monospace;
  padding: 20px;
}

code {
  color: #f66;
}
/*code {
 color: #fff;
}*/

pre{
  background-color: #666;
  padding: 10px 10px;
}

blockquote{
  border-left: 6px solid #ddd;
  margin: 30px 0;
  padding-left: 20px;
}
blockquote blockquote{
  border: none;
  text-align: right;
}

@media only screen and (max-width: 800px) {
  .right {
    left: 0%;
    right: 0%;
    top: 10%;
  }

  .footer {
    margin-top: 2rem;
  }

  .box-card {
    display: inline-block;
  }
}

@media only screen and (max-width: 500px) {
  .demo-paper {
    display: inline-block;
    height: 10rem;
    width: 10rem;
    margin: 1rem;
    text-align: center;
    cursor: pointer;
  } 
}
</style>