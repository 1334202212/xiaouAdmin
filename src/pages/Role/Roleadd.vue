<template>
  <div id="menuadd-box">
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path:'/home'}">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path:'/menu'}">角色列表</el-breadcrumb-item>
      <el-breadcrumb-item>{{title}}</el-breadcrumb-item>
    </el-breadcrumb>

    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="100px"
      class="demo-ruleForm"
    >
      <el-form-item label="角色名称" prop="rolename">
        <el-input v-model="ruleForm.rolename"></el-input>
      </el-form-item>
       
      <el-form-item label="角色权限" prop="menus">
               <el-tree
                :data="data"
                show-checkbox
                ref="mytree"
                node-key="id"
                check-strictly
                :default-expanded-keys="[1]"
                :default-checked-keys="checkedArr"
                :props="defaultProps">
                </el-tree>
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
      checkedArr:[],
      ruleForm: {
        rolename:'',
          menus:'',
          status:true,
      },
        data: [],
        defaultProps: {
          children: 'children',
          label: 'title'
        },
      rules: {
        rolename: [{ required: true, message: "请输入菜单名称", trigger: "blur" }],
      },
    };
  },
    mounted() {
       this.id = this.$route.query.id;

         if(this.id){
          this.title = '角色修改';
          this.buttonTitle = '修改';
            this.$http.get('/roleinfo',{id:this.id}).then(res=>{
                 let { status } = res.data.list
                this.ruleForm = { ...res.data.list, status: status == 1 ? true : false }
                this.checkedArr = res.data.list.menus.split(',');
            });  
         }else{
           this.title = '角色添加';
           this.buttonTitle = '添加'
         }

       this.$http.get('/menulist',{istree:true}).then(res =>{
                console.log(res)
           this.data = res.data.list;
       }) 
    },
  methods: {

    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 验证通过，调用添加的接口
                   this.ruleForm.status = this.ruleForm.status ? 1 : 2 
                  this.ruleForm.menus = this.$refs.mytree.getCheckedKeys();
              if(this.id){
                 this.$http.post('/roleedit',{ ...this.ruleForm, id: this.id }).then(res=>{
                         if(res.data.code == 200){
                          this.$router.back();
                    }
                  })
              }else{
                 this.$http.post('/roleadd',this.ruleForm).then((res) =>{
                  console.log(res)
                    if(res.data.code == 200){
                      this.$router.back();
                    }
             })
              }
        } else {
          console.log("error submit!!");
          return false;
        }
      });
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