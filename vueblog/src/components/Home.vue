<template>
  <el-container class="home_container">

    <el-header> <!--页面最上面一整行 包着两个div-->
      <div class="home_title">V部落博客管理平台</div><!--一个div显示一下名字-->
      <div class="home_userinfoContainer">         <!--这个div显示当前用户名字及四个下拉菜单-->
        <el-dropdown @command="handleCommand">     <!--el-dropdown下拉菜单  @command带指令事件的下拉菜单-->
          <span class="el-dropdown-link home_userinfo"><!--显示当前用户名字 且后面跟一个i图标-->
            {{currentUserName}}<i class="el-icon-arrow-down el-icon--right home_userinfo"></i>
          </span>
          <el-dropdown-menu slot="dropdown"><!--这里直接复制使用el-dropdown即可-->
            <el-dropdown-item command="sysMsg">系统消息</el-dropdown-item>
            <el-dropdown-item command="MyArticle">我的文章</el-dropdown-item>
            <el-dropdown-item command="MyHome">个人主页</el-dropdown-item>
            <el-dropdown-item command="logout" divided>退出登录</el-dropdown-item><!--command传输一个字符串指令 通过handleCommand方法判断-->
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </el-header>

    <el-container> <!--el-container构建整个页面框架-->
      <el-aside width="200px"><!--el-aside构建左侧菜单--> <!--el-menu左侧菜单内容-->
        <el-menu
          default-active="0"
          class="el-menu-vertical-demo" style="background-color: #ECECEC" router>
          <template v-for="(item,index) in this.$router.options.routes" v-if="!item.hidden"><!--template对应 el-submenu 菜单名-->

            <!--这里是文章管理下拉菜单及他的两个子菜单 通过v-if="item.children.length判断是否有孩子路由来实现-->
            <el-submenu :index="index+''" v-if="item.children.length>1" :key="index"><!--el-submenu可展开的菜单-->
              <template slot="title">      <!--如果路由有孩子路由，才有submenu这个可展开的菜单  就是文章管理这个菜单-->
                <i :class="item.iconCls"></i><!--i 设置菜单图标-->
                <span>{{item.name}}</span>
              </template>
              <el-menu-item v-for="child in item.children" v-if="!child.hidden" :index="child.path" :key="child.path">
                {{child.name}}    <!--遍历孩子路由 给el-menu-item赋予名字-->
              </el-menu-item>  <!--el-menu-item为菜单的子节点，不可再展开遍历孩子路由-->
            </el-submenu>

            <!--这里遍历出下面三个菜单栏-->
            <template v-else><!--如果没有孩子路由 则直接使用根路由-->
              <el-menu-item :index="item.children[0].path">
                <i :class="item.children[0].iconCls"></i>
                <span slot="title">{{item.children[0].name}}</span>
              </el-menu-item>
            </template>

          </template>
        </el-menu>
      </el-aside>

      <el-container>
        <el-main><!--el-breadcrumb面包屑  即点击文章列表后 显示： 首页>文章列表-->
          <el-breadcrumb separator-class="el-icon-arrow-right"><!--通过设置 separator-class 可使用相应的 iconfont 作为分隔符-->
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item><!--点击面包屑首页即可跳到首页页面-->
            <el-breadcrumb-item v-text="this.$router.currentRoute.name"></el-breadcrumb-item><!--首页之后的面包屑name-->
          </el-breadcrumb>
          <keep-alive>
            <router-view v-if="this.$route.meta.keepAlive"></router-view>
          </keep-alive>
          <router-view v-if="!this.$route.meta.keepAlive"></router-view>
        </el-main>
      </el-container>

    </el-container>
  </el-container>
</template>
<script>
  import {getRequest} from '../utils/api'
  export default{
    methods: {
      handleCommand(command){
        var _this = this;
        if (command == 'logout') {
          this.$confirm('注销登录吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(function () {
            getRequest("/logout")
            _this.currentUserName = '游客';
            _this.$router.replace({path: '/'});
          }, function () {
            //取消
          })
        }
      }
    },
    mounted: function () {
      // this.$alert('为了确保所有的小伙伴都能看到完整的数据演示，数据库只开放了查询权限和部分字段的更新权限，其他权限都不具备，完整权限的演示需要大家在自己本地部署后，换一个正常的数据库用户后即可查看，这点请大家悉知!', '友情提示', {
      //   confirmButtonText: '确定',
      //   callback: action => {
      //   }
      // });
      var _this = this;
      getRequest("/currentUserName").then(function (msg) {
        _this.currentUserName = msg.data;
      }, function (msg) {
        _this.currentUserName = '游客';
      });
    },
    data(){
      return {
        currentUserName: ''
      }
    }
  }
</script>
<style>
  .home_container {
    height: 100%;
    position: absolute;
    top: 0px;
    left: 0px;
    width: 100%;
  }

  .el-header {
    background-color: #20a0ff;
    color: #333;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .el-aside {
    background-color: #ECECEC;
  }

  .el-main {
    background-color: #fff;
    color: #000;
    text-align: center;
  }

  .home_title {
    color: #fff;
    font-size: 22px;
    display: inline;
  }

  .home_userinfo {
    color: #fff;
    cursor: pointer;
  }

  .home_userinfoContainer {
    display: inline;
    margin-right: 20px;
  }
</style>
