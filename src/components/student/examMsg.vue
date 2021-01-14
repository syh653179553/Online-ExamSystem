// 点击试卷后的缩略信息
<template>
  <div id="msg">
    <div class="ecv2_tiku_doInterWrap ecv2_tiku_doInterWrap_333">
      <div class="ecv2_tiku_doInterBox">
        <div class="ecv2_inner">
          <h3>{{examData.source}}</h3>
          <div class="ecv2_detail fl">
            <div class="fl">
              <ul>
                <li>总&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分：<span>{{score[0]+score[1]+score[2]}}</span></li>
                <li>及格分数：<span>{{((score[0]+score[1]+score[2])*0.6).toFixed(0)}}分</span></li>
                <li>总&nbsp;&nbsp;题&nbsp;&nbsp;数：<span>{{topicCount[0]+topicCount[1]+topicCount[2]}}</span></li>
              </ul>
            </div>
            <div class="fr">
              <ul>
                <li>考试时间：<span>{{examData.totalTime}}分钟</span></li>
                <li>试卷类型：<span>{{examData.type}}</span></li>
              </ul>
            </div>
            <div class="ecv2_btns">
              <a href="javascript:;" class="ecv2_ksBtn kaoshi" @click="toAnswer(examData.examCode)">开始考试</a>
            </div>
          </div>
          <div class="ecv2_intro fr" style="height: auto;">
            <h4>题型介绍</h4>
            <p> 选择题<br> 不定项选择，每个题有一个正确选项。<br> </p>
            <p> 填空题<br> 不定个数填空，每个空有固定的正确答案。<br> </p>
            <p> 判断题<br> 不定项选择，每个题有一个正确选项。<br> </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dialogVisible: false, //对话框属性
      activeName: '0',  //默认打开序号
      topicCount: [],//每种类型题目的总数
      score: [],  //每种类型分数的总数
      examData: { //考试信息
        // source: null,
        // totalScore: null,
      },
      topic: {  //试卷信息

      },
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    //初始化页面数据
    init() {
      let examCode = this.$route.query.examCode //获取路由传递过来的试卷编号
      this.$axios(`/api/exam/${examCode}`).then(res => {  //通过examCode请求试卷详细信息
        res.data.data.examDate = res.data.data.examDate.substr(0,10)
        this.examData = { ...res.data.data}
        let paperId = this.examData.paperId
        this.$axios(`/api/paper/${paperId}`).then(res => {  //通过paperId获取试题题目信息
          this.topic = {...res.data}
          let keys = Object.keys(this.topic) //对象转数组
          keys.forEach(e => {
            let data = this.topic[e]
            this.topicCount.push(data.length)
            let currentScore = 0
            for(let i = 0; i< data.length; i++) { //循环每种题型,计算出总分
              currentScore += data[i].score
            }
            this.score.push(currentScore) //把每种题型总分存入score
          })
        })
      })
    },
    toAnswer(id) {
      this.$router.push({path:"/answer",query:{examCode: id}})
    },
  }
}
</script>

<style lang="scss" scoped>
.bottom {
  .right{
    .el-button{
      color: #409EFF;
      border-color: #c6e2ff;
      background-color: #ecf5ff;
    }
  }
}
.right {
  margin-left: auto;
}
.content .title span {
  margin-right: 10px;
}
#msg{
  min-height: 540px;
}
 .content .title {
  font-size: 20px;
  margin: 0px;
  display: flex;
  align-items: center;
}
.content {
  margin-top: 20px;
  background-color: #fff;
}
.content .header {
  padding: 10px 30px;
}
.wrapper .info {
  margin: 20px 0px 0px 20px;
  border-top: 1px solid #eee;
  padding: 20px 0px 10px 0px;
}
.wrapper .info a {
  color: #88949b;
  font-size: 14px;
}
.wrapper .info a:hover {
  color: #0195ff;
}
.wrapper .bottom .btn {
  cursor: pointer;
  padding: 5px 10px;
  border: 1px solid #88949b;
  border-radius: 4px;
}
.wrapper .bottom {
  display: flex;
  margin-left: 20px;
  color: #999;
  font-size: 14px;
  align-items: center;
}
.wrapper .bottom li {
  margin-right: 14px;
}
#msg {
  background-color: #eee;
  width: 980px;
  margin: 0 auto;
}
#msg .title {
  margin: 20px;
}
#msg .wrapper {
  background-color: #fff;
  padding: 10px;
}
.wrapper .top {
  display: flex;
  margin: 20px;
  align-items: center;
}
.wrapper .top .right {
  margin-left: auto;
}
.wrapper .top .example {
  color: #333;
  font-size: 22px;
  font-weight: 700;
}
.wrapper .top li i {
  margin-left: 20px;
  color: #88949b;
}
.wrapper .right .count {
  margin-right: 60px;
  color: #fff;
  padding: 4px 10px;
  background-color: #88949b;
  border-top-left-radius: 4px;
  border-bottom-left-radius: 4px;
  border: 1px solid #88949b;
}
.wrapper .right .score {
  position: absolute;
  left: 53px;
  top: -5px;
  padding: 1px 12px;
  font-size: 20px;
  color: #88949b;
  border: 1px dashed #88949b;
  border-left: none;
  border-top-right-radius: 4px;
  border-bottom-right-radius: 4px;
  font-weight: bold;
}
.wrapper .right div {
  position: relative;
}

.ecv2_tiku_doInterBox .ecv2_inner {
  padding: 40px 100px;
  overflow: hidden;
  border-bottom: solid 1px #e5e5e5;
}
.ecv2_tiku_doInterBox .ecv2_inner>h3 {
  text-align: center;
  font-size: 20px;
  font-weight: normal;
  padding-bottom: 40px;
  margin-bottom: 16px;
  clear: both;
  border-bottom: solid 1px #e5e5e5;
  font-size: 26px;
}
.ecv2_tiku_doInterBox .ecv2_detail {
  padding: 30px 25px 0;
  width: 400px;
  font-size: 14px;
  color: #999999;
}
.ecv2_tiku_doInterBox .ecv2_intro {
  width: 314px;
  height: 246px !important;
  font-size: 14px;
  line-height: 30px;
  box-sizing: border-box;
  padding: 25px 20px 60px;
  background-color: #f2f7fa;
  overflow-y: auto;
}
.ecv2_tiku_inInterList {
  clear: both;
  overflow: hidden;
  padding: 20px 100px;
}
.ecv2_tiku_doInterWrap {
  position: relative;
  background-size: 134px 47px;
  /* padding: 40px 100px; */
  background: #fff;
  color: #1f3d4d;
}
.fl{
  float: left;
}
.fr{
  float: right;
}
.fl ul li{
  margin-bottom: 20px;
}
.ecv2_tiku_doInterBox .ecv2_btns {
  clear: both;
  text-align: center;
  padding-top: 50px;
}
.ecv2_tiku_doInterBox .ecv2_intro {
  width: 314px;
  height: 288px !important;
  font-size: 14px;
  line-height: 30px;
  box-sizing: border-box;
  padding: 25px 20px 60px;
  background-color: #f2f7fa;
  overflow-y:hidden
}
.ecv2_tiku_doInterBox .ecv2_intro>h4 {
  position: relative;
  line-height: initial;
  margin-bottom: 10px;
  padding-left: 26px;
  font-size: 18px;
  font-weight: normal;
}
.ecv2_tiku_doInterBox .ecv2_intro>h4:before {
  width: 4px;
  height: 24px;
  left: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  position: absolute;
  content: '';
  background-color: #2590d7;
}
.ecv2_ksBtn {
  display: inline-block;
  width: 140px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  font-size: 18px;
  color: #ffffff;
  background-color: #2590d7;
  box-shadow: 0px 3px 3px 0px rgba(79, 174, 235, 0.29);
  border-radius: 20px;
}
.ecv2_tiku_inInterList {
  clear: both;
  overflow: hidden;
  padding: 20px 100px;
}
.ecv2_tiku_inInterList>h3 {
  font-size: 18px;
  font-weight: normal;
  position: relative;
  padding-left: 26px;
  margin-bottom: 40px;
}
.ecv2_tiku_inInterList>h3:before {
  position: absolute;
  content: '';
  width: 4px;
  height: 24px;
  top: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  background-color: #2590d7;
}
.ecv2_tiku_inInterList li {
  float: left;
  width: 50%;
  margin-bottom: 24px;
}
.ecv2_tiku_inInterList li a {
  color: #1f3d4d;
  font-size: 16px;
  display: block;
  width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  padding-right: 40px;
  box-sizing: border-box;
}
</style>
