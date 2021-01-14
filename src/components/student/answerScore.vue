<template>
  <div class="score">
    <div class="bgfff">
      <h2 class="title1">{{examtype}}-{{examname}}</h2>

      <el-row>
        <el-col :span="10"><div class="grid-content bg-purple">
          <div class="score1">
            <span>得分</span>
            <p class="scored">{{score}}</p>
          </div>
        </div></el-col>
        <el-col :span="14"><div class="grid-content bg-purple-light">
          <div class="clearfix">
            <ul class="pull-left list-unstyled introSc">
              <li><img src="https://lstatic.educity.cn/educity/pc/uc/images/sc1.png"><em>总分：</em><span>{{totalScore}}</span> 分</li>
              <li><img src="https://lstatic.educity.cn/educity/pc/uc/images/sc2.png"><em>答题时间：</em><span>{{totalTime}}</span> 分钟</li>
              <li><img src="https://lstatic.educity.cn/educity/pc/uc/images/sc4.png"><em>考生：</em><span>{{userInfo.name}}</span></li>

            </ul>
            <ul class="pull-left list-unstyled introSc">
              <li><img src="https://lstatic.educity.cn/educity/pc/uc/images/sc1.png"><em>及格分：</em><span>{{totalScore * 0.6}}</span></li>
              <li><img src="https://lstatic.educity.cn/educity/pc/uc/images/sc2.png"><em>实际用时：</em><span>{{time}}</span> 分钟</li>
            </ul>
          </div>
        </div></el-col>
      </el-row>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      isTransition: false, //是否渲染完成
      score: 0, //总分
      imgShow: false, //不及格图片显示
      imgSrc: {
        fail1: require("@/assets/img/cry1.gif"),
        fail2: require('@/assets/img/cry2.jpg'),
        pass1: require('@/assets/img/good1.jpg'),
        pass2: require('@/assets/img/good2.gif'),
        file1: require('@/assets/img/scrBg.png')
      },
       userInfo: { //用户信息
           name: null,
           id: null
       },
      startTime: null, //考试开始时间
      endTime: null, //考试结束时间
      time: null,  //实际考试时长
      totalScore: null, //总分数
      totalTime: null,  //考试总时长
      examname: null,
      examtype: null,
    }
  },
  created() {
    this.transiton()
    this.getScore()
    this.getCookies()
  },
  methods: {
    transiton() {  //一秒后过渡
      setTimeout(() => {
        this.isTransition = true
        this.imgShow = true
      },1000)
    },
    getCookies(){
          this.userInfo.name = this.$cookies.get("cname")
          this.userInfo.id = this.$cookies.get("cid")
      },
    getScore() {
      let score = this.$route.query.score
      let startTime = this.$route.query.startTime
      let endTime = this.$route.query.endTime
      let time = this.$route.query.time
      let totalScore = this.$route.query.totalScore
      let totalTime = this.$route.query.totalTime
      let examname = this.$route.query.examname
      let examtype = this.$route.query.examtype
      this.score = score
      this.startTime = startTime
      this.endTime = endTime
      this.time = time
      this.totalScore = totalScore
      this.totalTime = totalTime
      this.examname = examname
      this.examtype = examtype
    }
  }
}
</script>

<style lang="scss" scoped>
.show {
  display: flex;
  justify-content: center;
  align-items: center;
  img {
    width: 160px;
    height: 160px;
  }
  .img1Transform {
    opacity: 1 !important;
    transform: translateX(30px) !important;
    transition: all 0.6s ease !important;
  }
  .img2Transform {
    opacity: 1 !important;
    transform: translateX(-30px) !important;
    transition: all 0.6s ease !important;
  }
  .img1 {
    margin-top: 70px;
    opacity: 0;
    transform: translateX(0px);
    transition: all 0.6s ease;
  }
  .img2 {
    margin-top: 30px;
    opacity: 0;
    transform: translateX(0px);
    transition: all 0.6s ease;
  }
}
.time {
  padding: 0px 70px;
  li {
    display: flex;
    justify-content: space-around;
    padding: 10px;
    margin: 20px 0px;
  }
  li:nth-child(1) {
    background-color: #fcf8e3;
  }
  li:nth-child(2) {
    background-color: #e9f5e9;
  }
}
.border {
  border: 6px solid #36aafd !important;
  transition: all 2s ease;
  width: 160px !important;
  height: 160px !important;
  transform: rotate(360deg) !important;
  opacity: 1 !important;
}
.score {
  max-width: 900px;
  margin: 0 auto;
  margin-top: 20px;
  min-height: 520px;
  .title {
    margin: 60px 0px 30px 0px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    .name {
      font-size: 26px;
      color: inherit;
      font-weight: 500;
    }
    .description {
      font-size: 14px;
      color: #888;
    }
  }
  .total {
    border: 1px solid #dbdbdb;
    background-color: #fff;
    padding: 40px;
    .look {
      border-bottom: 1px solid #dbdbdb;
      padding: 0px 0px 14px 14px;
      color: #36aafd;
    }
    .number {
      opacity: 0;
      border: 6px solid #fff;
      transform: rotate(0deg);
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      margin: 0 auto;
      width: 160px;
      height: 160px;
      border-radius: 50%;
      margin-top: 80px;
      margin-bottom: 20px;
      transition: all 1s ease;

      span:nth-child(1) {
        font-size: 36px;
        font-weight: 600;
      }
      span:nth-child(2) {
        font-size: 14px;
      }
    }
  }
}
.bgfff{
  background-color: #fff;
}
.title1 {
  font-size: 24px;
  color: #333;
  font-weight: normal;
  padding-bottom: 30px;
  margin: 0;
  margin-bottom: 30px;
  line-height: 1em;
  border-bottom: 1px dashed #ececec;
  text-align: center;
}
.score1 {
  margin: 0 auto;
  background: url(../../assets/img/scrBg.png) no-repeat;
  width: 248px;
  height: 244px;
  background-size: 100% 100%;
}
.scored {
  margin: 0;
  text-align: center;
  font-size: 100px;
  font-weight: bold;
  color: #ff9a00;
  line-height: 1em;
}
.score1>span {
  font-size: 18px;
  color: #666;
  display: block;
  text-align: center;
  padding: 50px 0 5px;
}
.introSc {
  width: 230px;
  margin: 0;
  float: left!important;
  padding-left: 0;
  list-style: none;
}
.introSc>li {
  margin-bottom: 15px;
  font-size: 16px;
  color: #666;
}
.bgfff {
  background: #fff;
  padding: 30px;
  box-sizing: border-box;
  margin-bottom: 30px;
}
.clearfix{
  margin: 35px 0 0 70px;
}
</style>

