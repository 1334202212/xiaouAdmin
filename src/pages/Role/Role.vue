<template>
  <div>
      <!-- 面包屑导航 -->
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>角色列表</el-breadcrumb-item>
      </el-breadcrumb>

      <!-- 添加按钮 -->
    <el-button type="primary"  @click="goAdd">添加</el-button>

    <el-table
      :data="tableData"
      style="width: 100%;margin-bottom: 20px;"
      row-key="id"
      border
      default-expand-all
      :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
    >
      <el-table-column prop="id" label="角色编号"  width="130"></el-table-column>
      <el-table-column prop="rolename" label="角色名称"></el-table-column>

        <el-table-column label="状态" prop="status">
                <template slot-scope="scope">
                    <el-tag>{{ scope.row.status | menuadd }}</el-tag>
                </template>      
            </el-table-column>

      <el-table-column label="操作" >
      <template slot-scope="scope">
         <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-popconfirm title="你确定要删除吗"
                                   @onConfirm="del(scope.row.id)">
                        <el-button slot="reference"
                                   size="mini"
                                   type="danger">删除</el-button>
                    </el-popconfirm>
      </template>
    </el-table-column>
    </el-table>

  </div>
</template>

<script>
export default {

  data() {
    return {
      tableData: [],
    };
  },
    mounted () {
         this.getlist();
       },
    methods: {
      getlist(){
           this.$http.get('/rolelist').then(res =>{
          this.tableData = res.data.list;
          console.log(res.data.list)
     })
      },
        handleEdit (index, row) {
            this.$router.push('role/edit?id=' + row.id)
        },
        handleDelete (index, row) {
            console.log(index, row);
        },
    
        goAdd () {
          this.$router.push('/role/add')
        },
        del(id){
            this.$http.post('/roledelete',{id}).then(res=>{
                 if(res.data.code == 200){
                      this.getlist();
                      this.$message({
                      message: '删除成功',
                      type: 'success'
                    });
                 }
            })
        }
       
    },
};
</script>

<style scoped>
</style>