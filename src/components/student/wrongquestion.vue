// 高频错题页面
<template>
  <div id="myExam">
    <div class="wrapper">
      <ul class="top">
        <li class="order">易错题列表</li>
        <li class="search-li">
          <el-select v-model="currentsubject" placeholder="请选择" @change="getQuestionInfo">
            <el-option
              v-for="(item,index) in subjectlist"
              :key="index"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
          <el-select v-model="currentquestiontype" placeholder="请选择" @change="getQuestionInfo">
            <el-option
              v-for="(item,index2) in typelist"
              :key="index2"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        <li><el-button type="primary" @click="getQuestionInfo()">搜索试题</el-button></li>
      </ul>
      <el-row :gutter="20">
        <el-col :span="16" :offset="1">
          <ul class="paper" v-loading="loading">
              <el-collapse v-for="(item,index) in pagination.records" :key="index">
                <el-collapse-item :title=item.question>
                  <div v-if="currentquestiontype == '选择题'">
                    <el-radio-group >
                      <el-radio :label="1">A、{{item.answerA}}</el-radio>
                      <el-radio :label="2">B、{{item.answerB}}</el-radio>
                      <el-radio :label="3">C、{{item.answerC}}</el-radio>
                      <el-radio :label="4">D、{{item.answerD}}</el-radio>
                    </el-radio-group>
                    <div class="analysis" >
                      <ul>
                        <li> <el-tag type="success">正确答案：</el-tag><span class="right">{{item.rightAnswer}}</span></li>
                        <li><el-tag>题目解析：</el-tag></li>
                        <li>{{item.analysis == null ? '暂无解析': item.analysis}}</li>
                      </ul>
                  </div>
                 </div>
                  <div v-if="currentquestiontype == '填空题'">
                    <div class="analysis" >
                      <ul>
                        <li> <el-tag type="success">正确答案：</el-tag><span class="right">{{item.answer}}</span></li>
                        <li><el-tag>题目解析：</el-tag></li>
                        <li>{{item.analysis == null ? '暂无解析': item.analysis}}</li>
                      </ul>
                    </div>
                  </div>
                  <div v-if="currentquestiontype == '判断题'">
                    <el-radio-group>
                      <el-radio :label="1">正确</el-radio>
                      <el-radio :label="2">错误</el-radio>
                    </el-radio-group>
                    <div class="analysis" >
                      <ul>
                        <li> <el-tag type="success">正确答案：</el-tag><span class="right">{{item.answer}}</span></li>
                        <li><el-tag>题目解析：</el-tag></li>
                        <li>{{item.analysis == null ? '暂无解析': item.analysis}}</li>
                      </ul>
                    </div>
                  </div>
                </el-collapse-item>
              </el-collapse>

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
                currentsubject: '数据库',//当前所查询的试题科目
                currentquestiontype: '选择题',//当前所查询的题目类型
                pagination: { //分页后的考试信息
                    current: 1, //当前页
                    total: null, //记录条数
                    size: 6 ,//每页条数
                },
                subjectlist: [{
                    index: 1,
                    value: '计算机网络',
                    label: '计算机网络'
                }, {
                    index: 2,
                    value: '数据库',
                    label: '数据库'
                }],
                typelist:[{
                    index: 1,
                    value: '选择题',
                    label: '选择题'
                },{
                    index: 2,
                    value: '填空题',
                    label: '填空题'
                },{
                    index: 3,
                    value: '判断题',
                    label: '判断题'
                }]
            }
        },
        created() {
            this.getQuestionInfo()
            this.loading = true
        },
        // watch: {

        // },
        methods: {
            //获取当前所有考试信息
            getQuestionInfo() {
                        this.$axios(`/api/getQuestion/${this.currentsubject}/${this.currentquestiontype}/${this.pagination.current}/${this.pagination.size}`).then(res => {
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
                this.getQuestionInfo()
            },
            //改变当前页码，重新发送请求
            handleCurrentChange(val) {
                this.pagination.current = val
                this.getQuestionInfo()
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
    min-height: 550px;
  }
  .el-radio-group label {
    margin: 5px 0px;
  }
  .el-radio-group {
    display: flex;
    flex-direction:column;
  }
  .type-nav{
    background-color: #FFFFFF;
    margin-top: 10px;
    height: 150px;
  }
  .analysis {
    margin-top: 10px;
    .right {
      color: #2776df;
      font-size: 18px;
      border: 1px solid #2776df;
      padding: 0px 6px;
      border-radius: 4px;
      margin-left: 20px;
      margin-right: 15px;
    }
    ul li:nth-child(2) {
      margin: 10px 0px;
    }
    ul li:nth-child(3) {
      padding: 10px;
      background-color: #d3c6c9;
      border-radius: 4px;
    }
  }
  .analysis span:nth-child(1) {
    font-size: 18px;
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
  }
  .paper .item .info {
    font-size: 14px;
    color: #88949b;
  }
  .paper .item .name {
    font-size: 14px;
    color: #88949b;
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
  /deep/ .el-collapse-item__header{
    line-height: 20px;
  }

</style>

