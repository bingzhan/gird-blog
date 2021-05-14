<template>
  <div class="layout">
    <section class="page-header" :style="{backgroundImage: `url(${GRIDSOME_API_URL}${general.cover.url})`,backgroundSize: 'cover'}">
      <div style="position: absolute; top: 20px; right: 20px; z-index: 2;">
        <el-tooltip effect="dark" :content="fullButton?'退出':'全屏'" placement="bottom-end">
          <el-button @click="fullClick" :icon="fullButton?'el-icon-close':'el-icon-rank'" circle></el-button>
        </el-tooltip>
      </div>
      <div v-for="item in general.note" :style="{
          position: 'absolute',
          left: 100 * Math.random() + 'vw',
          top: 400 * Math.random() + 'px',
          zIndex: 1
        }">
        <font :style="{fontSize: (20 + 20*Math.random()) +'px', color: 'rgb(255, 255, 255)'}"><b>♪</b></font>
      </div>
      <h1 class="project-name">{{general.title}}</h1>
      <h2 class="project-tagline">{{general.subtitle}}</h2>
      <a href="https://github.com/bingzhan" target="_blank" class="btn">GitHub主页</a>
      <a href="https://github.com/GitHub-Laziji/vblog" target="_blank" class="btn">源码</a>
    </section>

    <div style="position: relative; z-index: 2; margin: -30px auto auto; width: 64rem;">
      <el-card shadow="never" :body-style="{ padding: '0px' }">
        <el-row>
          <el-col :span="18">
            <el-menu @select="selectTopbar" :default-active="topbar.active" mode="horizontal" menu-trigger="click">
              <el-submenu index="#more">
                <template slot="title">了解博主</template>
                <el-menu-item index="#githubHome">github主页</el-menu-item>
                <el-menu-item index="#blog">其他博客</el-menu-item>
              </el-submenu>
              <el-submenu index="#webSites" v-if="$static.webSites.edges.length>0">
                <template slot="title">其他网站</template>
                <el-menu-item :index="'#webSites-'+index" v-for="(item,index) in $static.webSites.edges" :key="'#webSites'+index">{{item.node.content}}</el-menu-item>
              </el-submenu>
            </el-menu>
          </el-col>
          <el-col :span="4" style="text-align: right;">
            <div style="font-size: 20px; color: rgb(96, 98, 102); margin-top: 5px;"><b>{{general.username}}</b></div>
            <div style="color: rgb(96, 98, 102);"><i class="el-icon-location"></i>&nbsp;{{general.city}}<br></div>
          </el-col>
          <el-col :span="2" style="text-align: center;">
            <img v-popover:bigAvatar :src="`${GRIDSOME_API_URL}${general.avatar.url}`" style="margin-top: 4px; margin-right: 10px; width: 52px; height: 52px; border-radius: 5px; border: 1px solid rgb(235, 238, 245);">
            <el-popover ref="bigAvatar" placement="top-start" :title="general.username" width="200" trigger="hover">
              <i class="el-icon-location"></i>&emsp;{{general.city}}<br>
              <img :src="`${GRIDSOME_API_URL}${general.avatar.url}`" style="width: 200px; height: 200px;" />
            </el-popover>
          </el-col>
        </el-row>
      </el-card>
    </div>

    <section class="main-content">
      <el-row>
        <el-col :span="6" style="padding-right: 10px;">
          <el-card shadow="never">
            <el-menu :default-active="nav.active">
              <el-menu-item index="star-off">
                <g-link to="/">
                  <i class="el-icon-star-off"></i><span>最新动态</span>
                </g-link>
              </el-menu-item>
              <el-menu-item index="mobile-phone">
                <g-link to="/social/">
                  <i class="el-icon-mobile-phone"></i><span>社交圈</span>
                </g-link>
              </el-menu-item>
              <el-menu-item index="edit-outline">
                <g-link to="/blogs/">
                  <i class="el-icon-edit-outline"></i><span>博客列表</span>
                </g-link>
              </el-menu-item>
              <el-menu-item index="service">
                <g-link to="/about/">
                  <i class="el-icon-service"></i><span>开源项目</span>
                </g-link>
              </el-menu-item>
              <el-menu-item index="printer">
                <g-link to="/about/">
                  <i class="el-icon-printer"></i><span>使用帮助</span>
                </g-link>
              </el-menu-item>
              <el-menu-item index="document">
                <g-link to="/about/">
                  <i class="el-icon-document"></i><span>README.md</span>
                </g-link>
              </el-menu-item>
            </el-menu>
          </el-card>
        </el-col>
        <el-col :span="18" style="padding-left: 10px;">
          <slot/>
        </el-col>
      </el-row>
    </section>
    <section class="foot">
      <div style="border-top: 1px solid rgb(225, 228, 232) !important; padding: 45px 0px;">
        <el-row>
          <el-col :span="10">
            <div>
      				© 2018 GitHub-Laziji&emsp;&emsp;
      				<a href="https://github.com/GitHub-Laziji" target="_blank">Profile</a>&emsp;&emsp;
      				<a href="https://github.com/GitHub-Laziji/vblog" target="_blank">VBlog</a>
            </div>
          </el-col>
          <el-col :span="4">
            <div style="text-align: center; font-size: 18px;"><i class="el-icon-location-outline"></i></div>
          </el-col>
          <el-col :span="10">
            <div style="float: right;"><a href="https://developer.github.com" target="_blank">GitHub-API</a>&emsp;&emsp;
      				<a href="https://cn.vuejs.org/" target="_blank">Vue.js</a>&emsp;&emsp;
      				<a href="http://element.eleme.io/" target="_blank">Element</a>
            </div>
          </el-col>
        </el-row>
      </div>
    </section>
  </div>
</template>

<static-query>
  query {
    metadata {
      siteName
    }
    general: allStrapiGeneral{
      edges{
        node{
          title,
          subtitle,
          note,
          cover{
            url
          },
          username,
          city,
          avatar{
            url
          }
        }
      }
    }
    webSites: allWebSites{
      edges{
        node{
          content
        }
      }
    }
  }
</static-query>
<script>
  export default {
    data() {
      return {
        topbar: {
          active: '#more'
        },
        nav: {
          active: 'star-off'
        },
        fullButton: false,
      }
    },
    computed: {
      GRIDSOME_API_URL() {
        return process.env.GRIDSOME_API_URL;
      },
      general() {
        return this.$static.general.edges[0].node;
      }
    },
    methods: {
      fullClick() {
        if (!this.fullButton) {
          this.fullButton = true;
          const element = document.documentElement;
          if (element.requestFullscreen) {
            return element.requestFullscreen();
          } else if (element.webkitRequestFullScreen) {
            return element.webkitRequestFullScreen();
          } else if (element.mozRequestFullScreen) {
            return element.mozRequestFullScreen();
          } else {
            return element.msRequestFullscreen();
          }
        } else {
          this.fullButton = false;
          const element = document.documentElement;
          if (element.requestFullscreen) {
            return document.exitFullscreen();
          } else if (element.webkitRequestFullScreen) {
            return document.webkitCancelFullScreen();
          } else if (element.mozRequestFullScreen) {
            return document.mozCancelFullScreen();
          } else {
            return document.msExitFullscreen();
          }
        }
      },
      selectTopbar() {
        console.log(123, this.$page);
      },
    },
    mounted() {
      console.log(111, this.$static);
    }
  }
</script>
