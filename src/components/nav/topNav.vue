<template>
  <el-header>
    <el-row :gutter="20">
      <el-col :span="1">
        <div class="logo"
             @click="jumpTo('/main')">
          <img src="../../assets/logo.png"
               class="logo"></img>
        </div>
      </el-col>
      <el-col :span="13">
        <el-input placeholder="请输入内容"
                  prefix-icon="el-icon-search"
                  v-model="input">
        </el-input>
      </el-col>
      <el-col :span="7">
        <el-menu :default-active="defaultActiveIndex"
                 class="el-menu-demo"
                 mode="horizontal"
                 @select="handleSelect"
                 :router="true">
          <el-menu-item index="/enterpriseManager">动态</el-menu-item>
          <el-menu-item index="/Forum">论坛</el-menu-item>
          <el-menu-item index="/systemManager">历史</el-menu-item>
          <el-menu-item index="/systemManager">消息</el-menu-item>
          <el-menu-item index="/systemManager">发布</el-menu-item>
        </el-menu>

      </el-col>
      <el-col :span="2"
              class="touxiang"
              @click="jumpTo('/user')">
        <el-avatar shape="circle">user</el-avatar>
        <!-- <span>用户名</span> -->
      </el-col>
      <el-col :span="1">
        <div class="topbar-account topbar-btn">
          <el-dropdown trigger="click">
            <i class="el-icon-setting"
               style="margin-right: 15px"></i>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>
                <div @click="jumpTo('/user')"><span style="color: #555;font-size: 14px;">个人中心</span></div>
              </el-dropdown-item>
              <!-- <el-dropdown-item>
                  <div @click="jumpTo('/user/changepwd')"><span style="color: #555;font-size: 14px;">修改密码</span></div>
                </el-dropdown-item> -->
              <el-dropdown-item divided
                                @click.native="logout">退出登录</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </el-col>
    </el-row>
  </el-header>
</template>

<script>
import 'element-ui/lib/theme-chalk/display.css';
import { road } from '../../road.js'

export default {
  name: 'TopNav',
  data () {
    return {

    }
  },
  created () {
    road.$on('goto', (url) => {
      if (url === "/login") {
        localStorage.removeItem('access-user');
        this.$router.push(url);
      }
    });
    // 组件创建完后获取数据
    this.fetchNavData();
  },
  methods: {
    jumpTo (url) {
      this.$router.push(url); //用go刷新
    },
    handleSelect (index) {
      this.defaultActiveIndex = index;
    },
    logout () {
      //logout
      this.$confirm('确认退出吗?', '提示', {
        confirmButtonClass: 'el-button--warning'
      }).then(() => {
        //确认
        localStorage.removeItem('access-user');
        road.$emit('goto', '/login');
      }).catch(() => { });
    },
    fetchNavData () { // 初始化菜单激活项
      let cur_path = this.$route.path; //获取当前路由
      let routers = this.$router.options.routes; // 获取路由对象
      let nav_type = "", nav_name = "";
      for (var i = 0; i < routers.length; i++) {
        let children = routers[i].children;
        if (children) {
          for (let j = 0; j < children.length; j++) {
            if (children[j].path === cur_path) {
              nav_type = routers[i].type;
              nav_name = routers[i].name;
              break;
            }
            // 如果该菜单下还有子菜单
            if (children[j].children) {
              let grandChildren = children[j].children;
              for (let z = 0; z < grandChildren.length; z++) {
                if (grandChildren[z].path === cur_path) {
                  nav_type = routers[i].type;
                  nav_name = routers[i].name;
                  break;
                }
              }
            }
          }
        }
      }
      this.$store.state.topNavState = nav_type;
      this.$store.state.leftNavState = nav_name;
      if (nav_type == "home") {
        this.defaultActiveIndex = "/";
      } else {
        this.defaultActiveIndex = "/" + nav_name + "Manager";
      }
    }
  },
  mounted () {
    let user = window.localStorage.getItem('access-user');
    if (user) {
      user = JSON.parse(user);
      this.nickname = user.nickname || '';
      this.companyName = user.companyName || '';
    }
  },
  watch: {
    '$route': function (to, from) { // 路由改变时执行
      //console.info("to.path:" + to.path);
      this.fetchNavData();
    }
  }
}
</script>

<style>
.el-header {
  /* background-color: #dfe0e0; */
  text-align: right;
  font-size: 15px;
  border-bottom: 1px solid #dfe0e0;
  color: #333;
  line-height: 60px;
}

.el-menu-demo {
  transform: translateX(35%);
}

.logo {
  width: 100%;
  height: 100%;
  transform: translateX(5%) translateY(9%);
}

.touxiang {
  text-align: right;
  transform: translateX(10%) translateY(13%);
}
</style>
