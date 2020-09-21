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
      <el-form-item label="菜单名称" prop="title">
        <el-input v-model="ruleForm.title"></el-input>
      </el-form-item>

      <el-form-item label="上级菜单" prop="pid">
        <el-select v-model="ruleForm.pid" placeholder="请选择活动区域">
          <el-option label="顶级菜单" :value="0"></el-option>
          <el-option :label="item.title" :value="item.id" v-for="(item) in list" :key="item.id"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="菜单类型" prop="type">
        <el-radio-group v-model="ruleForm.type">
          <el-radio :label="1">目录</el-radio>
          <el-radio :label="2">菜单</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item label="菜单图标" prop="icon" v-show="ruleForm.type == 1">
        <el-input v-model="ruleForm.icon"></el-input>
      </el-form-item>

      <el-form-item label="菜单地址" prop="url" v-show="ruleForm.type == 2">
        <el-input v-model="ruleForm.url"></el-input>
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
        title: "", // 菜单名称
        pid: "", // 上级分类
        icon: "", // 图标
        status: true, // 状态   1正常2禁用   number类型（提交）
        type: "", // 菜单类型 1目录2菜单
        url: "", // 菜单地址
      },
      rules: {
        title: [{ required: true, message: "请输入菜单名称", trigger: "blur" }],
        pid: [{ required: true, message: "请选择上级菜单", trigger: "change" }],
      },
    };
  },
    mounted() {
      this.id = this.$route.query.id
      // 如果有编辑传来的id
          if(this.id){
            // 那么
            this.title = '菜单修改';
            this.buttonTitle = '修改'

          //  回显编辑数据
          this.$http.get('/menuinfo',{id:this.id}).then(res =>{
            this.ruleForm = res.data.list;
             let {status} = res.data.list
             this.ruleForm = {...res.data.list,status:status == 1? true : false } 
          })

          // 如过没有编辑传来的值
          }else{
             this.title = '菜单添加';
             this.buttonTitle = '添加'
          }
      this.getList();
  },
  methods: {
    getList() {
      this.$http.get("/menulist", { istree: true }).then((res) => {
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
              .post("/menuedit", { ...this.ruleForm, id: this.id })
              .then((res) => {
                console.log(res);
                if (res.data.code == 200) {
                  this.$router.back();
                }
              });
          } else {
            this.$http.post("/menuadd", this.ruleForm).then((res) => {
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