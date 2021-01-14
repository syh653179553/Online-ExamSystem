// 科目页面
<template>
  <div class="all">
    <el-table :data="pagination.records" border>
      <el-table-column fixed="left" prop="subjectid" label="学科编号" width="200"></el-table-column>
      <el-table-column fixed="left" prop="sname" label="学科名称" width="180"></el-table-column>
      <el-table-column fixed="right" label="操作" width="350">
        <template slot-scope="scope">
          <el-button @click="checkGrade(scope.row.subjectid)" type="primary" size="small">编辑</el-button>
          <el-button @click="findSection(scope.row.subjectid)" type="primary" size="small">详细</el-button>
          <el-button @click="addSection(scope.row.subjectid)" type="primary" size="small">添加章节</el-button>
          <el-button @click="deleteById(scope.row.subjectid)" type="danger" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[6, 10]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page">
    </el-pagination>
    <!-- 编辑对话框-->
    <el-dialog
      title="编辑学科信息"
      :visible.sync="dialogVisible"
      width="30%">
      <section class="update">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="学科编号">
            <el-input v-model="form.subjectid"></el-input>
          </el-form-item>
          <el-form-item label="学科名称">
            <el-input v-model="form.sname"></el-input>
          </el-form-item>
        </el-form>
      </section>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit()">确 定</el-button>
      </span>
    </el-dialog>
    <!--章节对话框 -->
    <el-dialog
    title="章节信息"
      :visible.sync="dialogVisibl"
      width="30%">
        <el-table :data="sections">
        <el-table-column prop="sectionName" label="章节名称" width="180px"></el-table-column>
        <el-table-column  label="操作" width="180">
        <template slot-scope="scope">
          <el-button @click="deleteBysectionId(scope.row.sectionId)" type="danger" size="small">删除</el-button>
        </template>
      </el-table-column>
        </el-table>
    </el-dialog >
    <!--添加章节对话框-->
    <el-dialog
    title="添加章节"
    :visible.sync="dialogVisib"
    width="30%">
    <section class="insert">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="章节名称">
            <el-input v-model="form.sectionName"></el-input>
          </el-form-item>
        </el-form>
      </section>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisib = false">取 消</el-button>
        <el-button type="primary" @click="submit2()">添加</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6, //每页条数
      },
      form:{
        subjectid:null,
        sectionName:null,
      },
      sections:null,
      subject:null,
      dialogVisible: false, //对话框
      dialogVisibl: false, //章节对话框
      dialogVisib: false, //添加章节对话框
      form: {}, //保存点击以后当前试卷的信息
    };
  },
  created() {
    this.getSubjectInfo();
  },
  methods: {
    getSubjectInfo() {
      //分页查询所有试卷信息
      this.$axios(`/api/subjects/${this.pagination.current}/${this.pagination.size}`).then(res => {
        this.pagination = res.data.data;
      }).catch(error => {});
    },

    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getSubjectInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getSubjectInfo();
    },
    checkGrade(subjectid) { //修改学科信息信息
      this.dialogVisible = true
      this.$axios(`/api/subject/${subjectid}`).then(res => {
        this.form = res.data.data
      })
    },
    findSection(subjectid){
        this.dialogVisibl = true;
        this.subject = subjectid
        this.$axios(`/api/sections/${subjectid}`).then(res =>{
            this.sections = res.data.data
        })
    },
    addSection(subjectid){
        this.dialogVisib = true;
        this.form.subjectid = subjectid;
    },
    deleteById(subjectid) { //删除当前学科
      this.$confirm("确定删除当前学科吗？删除后无法恢复","Warning",{
        confirmButtonText: '确定删除',
        cancelButtonText: '算了,留着吧',
        type: 'danger'
      }).then(()=> { //确认删除
        this.$axios({
          url: `/api/subject/${subjectid}`,
          method: 'delete',
        }).then(res => {
          this.getSubjectInfo()
        })
      }).catch(() => {

      })
    },
    deleteBysectionId(sectionId){
      this.$confirm("确定删除当前章节吗？删除后无法恢复","warning",{
        confirmButtonText: '确定删除',
        cancelButtonText: '算了,留着吧',
        type: 'danger'
      }).then(()=> {
        this.$axios({
          url: `/api/section/${sectionId}`,
          method: 'delete',
        }).then(res => {
          this.findSection(this.subject)
        })
      }).catch(() => {
          console.log(1111)

      })
    },
    submit() { //提交更改
      this.dialogVisible = false
      this.$axios({
        url: '/api/subject',
        method: 'put',
        data: {
          ...this.form
        }
      }).then(res => {
        console.log(res)
        if(res.data.code ==200) {
          this.$message({
            message: '更新成功',
            type: 'success'
          })
        }
        this.getSubjectInfo()
      })
    },
    submit2(){
      this.dialogVisib = false
      this.$axios({
        url:'/api/section',
        method: 'post',
        data:{
          ...this.form
        }
      }).then(res => {
        console.log(res)
        if(res.data.code==200){
          this.$message({
            message:'添加成功',
            type:'success'
          })
        }
        this.getSubjectInfo()
      })
    },
    handleClose(done) { //关闭提醒
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
        }).catch(_ => {});
    },
  }
};
</script>
<style lang="scss" scoped>
.all {
  padding: 0px 40px;
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #dd5862 !important;
  }
}
.el-table .warning-row {
  background: #000 !important;
}

.el-table .success-row {
  background: #dd5862;
}
</style>
