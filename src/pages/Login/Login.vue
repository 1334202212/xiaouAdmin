 <template>
  <div>
    <el-form  label-width="80px" >

      <el-form-item label="用户名">
        <el-input v-model="name"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="psd"></el-input>
      </el-form-item>
    </el-form>

    <el-button @click="login" type="primary">提交</el-button>
  </div>
</template>
 
 <script>
export default {
    data() {
        return {
            name:'',
            psd:'',
        }
    },

    methods: {
       login(){
            this.$http.post('/userlogin',{username:this.name,password:this.psd}).then(res=>{ 
                console.log(res)
                        if(res.data.code == 200){
                            this.$store.commit('setuser', res.data.list);
                            this.$router.push('/menu')
                        }
              })
       }
    },
};
</script>
 
 <style scoped>
</style>