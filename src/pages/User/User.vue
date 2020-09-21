<template>
  <div>
      <!-- 面包屑导航 -->
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path:'/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>管理员列表</el-breadcrumb-item>
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
      <el-table-column prop="id" label="用户编号"  width="180"></el-table-column>
      <el-table-column prop="username" label="用户名"  width="180"></el-table-column>
      <el-table-column  label="所属角色"  width="180">
                 <template slot-scope="scope">
                    <el-tag>{{ scope.row.roleid | userfomat(rolelist) }}</el-tag>
                </template> 
      </el-table-column>

        <el-table-column
                             label="状态">
                <template slot-scope="scope">
                    <el-tag>{{ scope.row.status | menuadd }}</el-tag>
                </template>      
       </el-table-column>

      <el-table-column label="操作" sortable width="180">
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
      rolelist:[],
    };
  },
  filters:{
    userfomat(roleid,rolelist){
        if(rolelist.length > 0){
          let item = rolelist.find(item=>{
            return item.id = roleid
          });
          return item.rolename;
        }
      }
  },
    mounted () {
        this.getList();
        this.$http.get('/rolelist').then(res =>{
            this.rolelist = res.data.list
        })
    },
    methods: {
        getList () {
            this.$http.get("/userlist", {size:10,page:1}).then(res => {
                // console.log(res)
                this.tableData = res.data.list
            })
        },
        handleEdit (index, row) {
            this.$router.push('/useredit?id=' + row.uid)
            console.log(row.id)
        },
        handleDelete (index, row) {
            console.log(index, row);
        },
    
        goAdd () {
            this.$router.push("/useradd")
        },
        del(id){
            this.$http.post('/userdelete',{id}).then(res=>{
                if(res.data.code == 200){
                  this.getList();

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