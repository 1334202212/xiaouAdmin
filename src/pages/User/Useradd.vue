<template>
  <div id="menuadd-box">
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path:'/home'}">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path:'/menu'}">菜单管理</el-breadcrumb-item>
      <el-breadcrumb-item>{{title}}</el-breadcrumb-item>
    </el-breadcrumb>

    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="100px"
      class="demo-ruleForm"
    >
      <el-form-item label="所属角色" prop="roleid">
        <el-select v-model="ruleForm.roleid" placeholder="请选择角色">
          <el-option :label="item.rolename" :value="item.id" v-for="(item) in list" :key="item.id"></el-option>
        </el-select>
      </el-form-item>

        <el-form-item label="用户名" prop="username">
        <el-input v-model="ruleForm.username"></el-input>
      </el-form-item>

          <el-form-item label="密码" prop="password">
        <el-input v-model="ruleForm.password"></el-input>
      </el-form-item>

      <el-form-item label="状态" prop="status">
        <el-switch v-model="ruleForm.status"></el-switch>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">{{ buttonTitle }}</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      id: "",
      title: "", // 面包屑最后的文字
      buttonTitle: "", // 最后按钮的文
      list: [],
      ruleForm: {
       roleid:'',
       username:'',
       password:'',
       status:true,
      },
      rules: {
        username: [{ required: true, message: "请输入用户名", trigger: "blur" }],
         roleid: [{ required: true, message: "请选择所属角色", trigger: "change" }],
      },
    };
  },
    mounted() {
      this.id = this.$route.query.id
      // 如果有编辑传来的id
          if(this.id){
            // 那么
            this.title = '用户修改';
            this.buttonTitle = '修改'

          //  回显编辑数据
          this.$http.get('/userinfo',{uid:this.id}).then(res =>{
            this.ruleForm = res.data.list;
             let {status} = res.data.list
             this.ruleForm = {...res.data.list,status:status == 1? true : false ,password:''} 
          })

          // 如过没有编辑传来的值
          }else{
             this.title = '用户添加';
             this.buttonTitle = '添加'
          }
      this.getList();
  },
  methods: {
    getList() {
      this.$http.get("/rolelist",).then((res) => {
        console.log(res);
        this.list = res.data.list;
      });
    },

    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 验证通过，调用添加的接口
          this.ruleForm.status = this.ruleForm.status ? 1 : 2;
         if (this.id) {
            this.$http
              .post("/useredit", { ...this.ruleForm, uid: this.id })
              .then((res) => {
                console.log(res);
                if (res.data.code == 200) {
                  this.$router.back();
                }
              });
          } else {
            this.$http.post("/useradd", this.ruleForm).then((res) => {
              console.log(res);
              if (res.data.code == 200) {
                this.$router.back();
              }
            });
          }
          
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
  },
};
</script>
   
<style scoped>
.el-container {
  width: 600px;
  height: 500px;
}
</style>