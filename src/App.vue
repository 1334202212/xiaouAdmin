<template>
  <el-container>
    <el-aside width="200px">
      <!-- 侧边栏 -->
      <side />
    </el-aside>
    <el-container>
      <el-header>{{name}}</el-header>

      <el-main>
        <router-view></router-view>
      </el-main>

    </el-container>
  </el-container>
</template>

<script>
import Side from "./components/Side";
export default {
  name: "App",
  components: {
    Side,
  },
  computed: {
    name(){
       return this.$store.state.user.username
    }
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
html,
body,
#app,
.el-container {
  width: 100%;
  height: 100%;
}
.el-header {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 50px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  height: 100%;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  height: 100%;
}
.el-button--primary{margin: 10px 0;}
.demo-ruleForm{width:600px;
             height:500px;
             margin-top: 10px;
}
#menuadd-box{width: 100%;
             height: 100%;
             background: white;
             }
.el-main{background: white;}
</style>
