// 我的练习页面
<template>
  <div id="myExam">
<!--    <el-container class="type-nav">-->
<!--      <el-header class="nav-title"><h3>我的练习</h3></el-header>-->
<!--      <el-container class="nav-item">-->
<!--       <el-aside width="200px"><a class="top-icon el-icon-notebook-1"><br/><font style="font-size: 13px">章节练习</font></a></el-aside>-->
<!--        <el-aside width="200px"><a class="top-icon el-icon-collection"><br/><font style="font-size: 13px">高频错题</font></a></el-aside>-->
<!--      </el-container>-->
<!--    </el-container>-->

    <div class="wrapper">
      <ul class="top">
        <li class="order">练习列表</li>
        <li class="search-li"><div class="icon"><input type="text" placeholder="试卷名称" class="search" v-model="key"><i class="el-icon-search"></i></div></li>
        <li><el-button type="primary" @click="search()">搜索试卷</el-button></li>
      </ul>
      <el-row :gutter="20">
        <el-col :span="16" :offset="1">
          <ul class="paper" v-loading="loading">
            <li class="item" v-for="(item,index) in pagination.records" :key="index">
              <div>
                <el-row :gutter="24">
                  <el-col :span="21">
                    <h4 @click="toExamMsg(item.examCode)">{{item.source}}</h4>
                    <p class="name">{{item.source}}-{{item.description}}</p>
                  </el-col>

                  <el-col :span="3">
                    <el-button @click="toExamMsg(item.examCode)" type="primary" plain style="float: right;margin: 20px 20px 5px 10px;">开始练习</el-button>
                  </el-col>

                </el-row>


              </div>

              <!--            <div class="info" >-->
              <!--              <i class="el-icon-loading"></i><span>{{item.examDate.substr(0,10)}}</span>-->
              <!--              <i class="iconfont icon-icon-time"></i><span v-if="item.totalTime != null">限时{{item.totalTime}}分钟</span>-->
              <!--              <i class="iconfont icon-fenshu"></i><span>满分{{item.totalScore}}分</span>-->
              <!--              <el-button type="primary" plain style="float: right;margin: 0px 20px 5px 0px;right: 30px">开始考试</el-button>-->
              <!--            </div>-->
            </li>
          </ul>
        </el-col>
        <el-col :span="7">
          <div class="hot-paper">
            <div class="ecv2_com_h3">
              热门推荐
            </div>
            <ul>
              <li><a> <img src="https://lstatic.educity.cn/educity/pc/cmsng/img/19.png" alt=""> <h3>网络直播班</h3> <p>直播+录播 双向教学</p></a> </li>
              <li><a> <img src="https://lstatic.educity.cn/educity/pc/cmsng/img/20.png" alt=""> <h3>视频课程</h3> <p>零基础在线学习</p></a> </li>
              <li><a> <img src="https://lstatic.educity.cn/educity/pc/cmsng/img/21.png" alt=""> <h3>在线题库</h3> <p>历年真题自测估分</p></a> </li>
            </ul>
          </div></el-col>
      </el-row>



      <div class="pagination">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pagination.current"
          :page-sizes="[6, 10, 20, 40]"
          :page-size="pagination.size"
          layout="total, sizes, prev, pager, next, jumper"
          :total="pagination.total">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  // name: 'myExam'
  data() {
    return {
      loading: false,
      key: null, //搜索关键字
      allExam: null, //所有考试信息
      pagination: { //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6 //每页条数
      }
    }
  },
  created() {
    this.getExamInfo()
    this.loading = true
  },
  // watch: {

  // },
  methods: {
    //获取当前所有考试信息
    getExamInfo() {
      this.$axios(`/api/getpractices/${this.pagination.current}/${this.pagination.size}`).then(res => {
        this.pagination = res.data.data
        this.loading = false
        console.log(this.pagination)
      }).catch(error => {
        console.log(error)
      })
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val
      this.getExamInfo()
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val
      this.getExamInfo()
    },
    //搜索试卷
    search() {
      this.$axios('/api/exams').then(res => {
        if(res.data.code == 200) {
          let allExam = res.data.data
          let newPage = allExam.filter(item => {
            return item.source.includes(this.key)
          })
          this.pagination.records = newPage
        }
      })
    },
    //跳转到试卷详情页
    toExamMsg(examCode) {
      this.$router.push({path: '/examMsg', query: {examCode: examCode}})
      console.log(examCode)
    }
  }
}
</script>


<style lang="scss" scoped>
  #myExam{
    min-height: 540px;
  }
  .type-nav{
    background-color: #FFFFFF;
    margin-top: 10px;
    height: 150px;
  }
  .type-nav header h3{
    font-weight: normal;
  }
  .nav-title{
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
  }
  .top-icon{
    font-size: 35px;
    color: #333333;
  }
  .nav-item{
    display: flex;
    align-items: center;
    text-align: center;
    border-bottom: 1px solid #eee;
  }
  .top-icon:hover{
    color: #0195ff;
  }
  .top-icon:active{
    color: #0195ff;
    border-bottom: #0195ff;
    border-bottom-width: 3px;
  }

  .pagination {
    padding: 20px 0px 30px 0px;
    .el-pagination {
      display: flex;
      justify-content: center;
    }
  }
  .paper {
    h4 {
      cursor: pointer;
    }
    width:650px;
  }
  .paper .item a {
    color: #000;
  }
  .wrapper .top .order {
    cursor: pointer;
  }
  .wrapper .top .order:hover {
    color: #0195ff;
    border-bottom: 2px solid #0195ff;
  }
  .wrapper .top .order:visited {
    color: #0195ff;
    border-bottom: 2px solid #0195ff;
  }
  .item .info i {
    margin-right: 5px;
    color: #0195ff;
  }
  .item .info span {
    margin-right: 14px;
  }
  .paper .item {
    width: 630px;
    border-radius: 4px;
    padding-left: 20px;
    border-bottom: 1px solid #eee;
    box-shadow: 0 0 4px 2px rgba(217,222,234,0.3);
    transition: all 0.6s ease;
    margin: 0;
  }
  .paper .item:hover {
    box-shadow: 0 0 4px 2px rgba(140, 193, 248, 0.45);
    transform: scale(1.03);
  }
  .paper .item .info {
    font-size: 14px;
    color: #88949b;
  }
  .paper .item .name {
    font-size: 14px;
    color: #88949b;
  }
  .paper * {
    margin: 20px 0;
  }
  .wrapper .paper {
    justify-content: space-around;
    flex-wrap: wrap;
  }
  .top .el-icon-search {
    position: absolute;
    right: 10px;
    top: 10px;
  }
  .top .icon {
    position: relative;
  }
  .wrapper .top {
    border-bottom: 1px solid #eee;
    margin-bottom: 20px;
  }
  #myExam .search-li {
    margin-left: auto;
  }
  .top .search-li {
    margin-left: auto;
  }
  .top li {
    display: flex;
    align-items: center;
  }
  .top .search {
    margin-left: auto;
    padding: 10px;
    border-radius: 4px;
    border: 1px solid #eee;
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
  }
  .top .search:hover {
    color: #0195ff;
    border-color: #0195ff;
  }
  .wrapper .top {
    display: flex;
  }
  .wrapper .top li {
    margin: 20px;
  }
  #myExam {
    width: 980px;
    margin: 0 auto;
  }
  #myExam .title {
    margin: 20px;
  }
  #myExam .wrapper {
    background-color: #fff;
  }
  .ecv2_com_h3{
    position: relative;
    font-size: 18px;
    color: #1f3d4d;
    padding-left: 15px;
    padding-bottom: 3px;
  }
  .ecv2_com_h3:before {
    position: absolute;
    content: '';
    width: 4px;
    height: 26px;
    left: 0;
    top: 0;
    border-top: 0;
    margin: auto;
    background: #2590d7;
  }
  .hot-paper ul li {
    position: relative;
    width: 260px;
    height: 80px;
    box-sizing: border-box;
    padding-left: 22px;
    color: #667780;
    font-size: 14px;
    padding-top: 28px;
    margin-bottom: 10px;
    z-index: 1;
  }
  .hot-paper ul li img {
    width: 100%;
    height: 110%;
    position: absolute;
    left: 0;
    top: 0;
    z-index: -1;
  }
  .hot-paper ul li h3 {
    font-size: 16px;
    color: #1f3d4d;
    margin: 0 0 10px;
  }
  .item div{
    margin: 0;
  }
</style>

