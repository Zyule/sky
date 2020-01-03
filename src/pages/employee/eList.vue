<template>
<div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="employee">
        <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
        <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
        <el-table-column fixed="left" prop="realname" label="姓名"></el-table-column>
        <el-table-column prop="gender" label="性别"></el-table-column>
        <el-table-column prop="telephone" width="200" label="手机号"></el-table-column>
        <el-table-column prop="idCard" width="200" label="身份证号"></el-table-column>
        <el-table-column prop="bankCard" width="200" label="银行卡号"></el-table-column>
        <el-table-column prop="操作">
            <template v-slot="slot">
                 <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                 <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination
    layout="prev, pager, next" :total="50">
    </el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
        title="录入员工信息"
        :visible.sync="visible"
        width="60%">
        ---{{form}}
        <el-form>
            <el-form label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"/>
            </el-form-item>
            <el-form-item label="密码">
                <el-input v-model="form.password" type="password"/>
            </el-form-item>
            <el-form-item label="姓名">
                <el-input v-model="form.realname"/>
            </el-form-item>
            <el-form-item label="性别">
                <el-input v-model="form.gender" type="gender"/>
            </el-form-item>
            <el-form-item label="手机号码">
                <el-input v-model="form.telephone" type="telephone"/>
            </el-form-item>
            <el-form-item label="身份证号">
                <el-input v-model="form.idCard" type="idCard"/>
            </el-form-item>
            <el-form-item label="银行卡号">
                <el-input v-model="form.bankCard" type="bankCard"/>
            </el-form-item>
            </el-form>
        </el-form>

        <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
        </span>
    </el-dialog>
    <!-- /模态框 -->
    </div>    
    </template>

   <script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{

    loadData(){
      let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.employee = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/waiter/saveOrUpdate";
      //前端向后台发送请求，完成数据的保存数据
      request({
        url,
        method:"POST",
        //告诉给后台我的请求体中放的是查询字符串
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        //请求体中的数据，将this。form转换为查询字符串发送给后台
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      })
      
    },
    toUpdateHandler(){
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      employee:[],
      form:{
        type:"employee"
      }
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>
    <style scoped>
    </style>