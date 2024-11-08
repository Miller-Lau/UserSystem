<template>
    <div class="edit">
        <el-button type="primary"
        round @click="openDialog">新增</el-button>
        <el-table :data="tableData" style="width:100%">
            <el-table-column prop="id" label="ID" width="100%"></el-table-column>
            <el-table-column prop="name" label="姓名" width="100%"></el-table-column>
            <el-table-column prop="age" label="年龄" width="100%"></el-table-column>
            <el-table-column label="操作" width="180">
                <template slot-scope="scope">
                    <el-button size="mini" @click="editData(scope.row.id)">编辑</el-button>
                    <el-button size="mini" type="danger" @click="deleteRow(scope.row.id)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-dialog :visible.sync="dialogVisible" title="编辑信息">
            <el-form :model="user">
                <el-form-item label="姓名"
                :label-width="80">
                <el-input v-model="user.name"></el-input>
                </el-form-item>
                <el-form-item label="年龄"
                :label-width="80">
                <el-input v-model="user.age" type="number"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible=false">取消</el-button>
                <el-button type="primary" @click="saveData()">保存</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    import axios from 'axios'
    export default{
        data(){
            return{
                tableData:[],
                dialogVisible:false,
                user:{
                    id:null,
                    name:'',
                    age:''
                },
                isEdit:false
            }
        },
        mounted(){
            this.fetchData()
        },
        methods:{
            fetchData(){
                axios.get('/api/show')
            .then(response => {
                this.tableData = response.data
            })
            .catch(error =>{
                console.error("Error:",error)
            })
            },
            openDialog(){
                this.isEdit = false
                this.user ={id:null, name:'',age:''}
                this.dialogVisible = true
            },
            saveData(){
                if(this.user.id === null){
                    axios.post('/api/save',this.user)
                    .then(response => {
                        console.log(response.data)
                        //$message from elementui
                        this.$message.success("保存成功!");
                        this.dialogVisible = false
                        this.fetchData();
                    })
                    .catch(error =>{
                        console.error("Error:",error)
                        this.$message.error("保存失败");
                    })
                }else{
                    axios.post('/api/update',this.user)
                    .then(response => {
                        //$message from elementui
                        this.$message.success("用户更新成功!");
                        this.dialogVisible = false
                        this.fetchData();
                    })
                    .catch(error =>{
                        console.error("Error:",error)
                        this.$message.error("更新失败");
                    })
                }
           
          },
          deleteRow(id){
            axios({
                method:'get',
                url:'/api/delete/'+id,
                timeout:5000
            })
            .then(response => {
                console.log(response.data);
                this.$message.success("删除成功!");
                this.dialogVisible = false
                this.fetchData();
            }).catch(error =>{
                console.error("Error:",error)
                this.$message.error("删除失败");
            })
          },
          editData(id){
            axios.get('/api/find/'+id)
            .then(response => {
                this.user = response.data
                this.dialogVisible = true
                this.isEdit = true
            }).catch(error =>{
                console.error("Error:",error)
                this.$message.error("获取失败");
            })
          }
        }
        
    }
</script>

<style scoped>
.dialog-footer{
    text-align: right;
}
</style>