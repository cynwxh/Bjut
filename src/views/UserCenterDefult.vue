<template>
  <div>
    <div class="info-box">
      <div class="head">Personal information</div>
      <div class="body">
        <p>{{userInfo.p_title}}&nbsp;{{userInfo.p_first_name}}&nbsp;{{userInfo.p_last_name}}</p>
        <p>Affiliation: {{userInfo.p_affiliation}}</p>
        <p>Email: {{userInfo.p_email}}</p>
        <p>Address: {{userInfo.p_address}}</p>
        <button @click="modifyUserInfo">Modify</button>
      </div>
    </div>
    <div class="info-box">
      <div class="head">Abstract List</div>
      <div class="body">
        <table class="table1" border="1">
          <tr>
            <td>ID</td>
            <td>Abstract Title</td>
            <td></td>
          </tr>
          <tr v-for="item in ab_list" :key="item.number">
            <td>{{item.number}}</td>
            <td>{{item.paper_title}}</td>
            <td>
              <a :href="item.file">Download</a>
            </td>
          </tr>
        </table>
        <button @click="toAbstractSubmission">Submit an Abstract</button>
      </div>
    </div>
    <div class="info-box">
      <div class="head">Full-Paper List</div>
      <div class="body">
        <table class="table2" border="1">
          <tr>
            <td>ID</td>
            <td>Paper Title</td>
            <td></td>
          </tr>
          <tr v-for="item in full_list" :key="item.number">
            <td>{{item.number}}</td>
            <td>{{item.paper_title}}</td>
            <td>
              <a :href="item.file">Download</a>
            </td>
          </tr>
        </table>
        <button @click="toFullPaper">Submit a Full Paper</button>
      </div>
    </div>
    <div class="info-box">
      <div class="head">Registration Information</div>
      <div class="body">
        <table class="table3" border="1">
          <tr>
            <td>{{isPay==false?"Registration fee":"Category"}}</td>
            <td>{{Participation.registration_fee}}</td>
          </tr>
          <tr>
            <td>{{isPay==false?"Accommodation reservation":"Fee"}}</td>
            <td>{{Participation.hotel}}</td>
          </tr>
          <tr>
            <td>{{isPay==false?"Tour visit":"Tour Reservation"}}</td>
            <td></td>
          </tr>
        </table>
        <button @click="toRegistration">Registration Now</button>
      </div>
    </div>
  </div>
</template>
<script>
import { setTimeout } from "timers";
export default {
  data() {
    return {
      head: this.$route.matched[this.$route.matched.length - 1].meta.title,
      userInfo: {},
      ab_list: [],
      full_list: [],
      isPay:false,
      Participation: {
        hotel: "",
        registration_fee: "",
        url_now: ""
      }
    };
  },
  methods: {
    //点击跳转修改个人信息页面
    modifyUserInfo() {
      this.$router.push({ path: "/userInfo" });
    },
    //点击按钮跳转AbstractSubmission
    toAbstractSubmission() {
      if (this.userInfo.p_first_name) {
        this.$router.push({ path: "/abstractSubmission" });
      } else {
        this.$alert(
          "You haven't improved your personal information yet. Please improve your personal information first! Click OK to go to the Personal Information page!",
          "Tips"
        ).then(() => {
          this.$router.push({ path: "/userInfo" });
        });
      }
    },
    //点击按钮跳转FullPaper
    toFullPaper() {
      if (this.ab_list.length != 0) {
        this.$router.push({ path: "/fullpaper" });
      } else {
        this.$alert(
          "You haven't uploaded the abstract yet, please upload the abstract first! Click OK to upload the summary page!",
          "Tips"
        ).then(() => {
          this.$router.push({ path: "/abstractSubmission?id=9" });
        });
      }
    },
    //点击按钮跳转Registration
    toRegistration(){
      this.$router.push({path:'/registration'})
    }
  },
  created() {
    this.axios({
      url: "/gaojian/index.php",
      method: "post",
      params: this.params({
        act: "list_full",
        p_id: sessionStorage.userId
      })
    })
      .then(res => {
        this.ab_list = res.data.result.Ab_LIST;
        this.full_list = res.data.result.FULL_LIST;
      })
      .catch(err => {
        console.log(err);
      });
    this.axios({
      method: "post",
      url: "/gaojian/index.php",
      params: this.params({
        act: "personnel_info",
        p_id: sessionStorage.userId
      })
    })
      .then(res => {
        this.userInfo = res.data.result;
      })
      .catch(err => {
        console.log(err);
      });
    this.axios({
      url: "/gaojian/index.php",
      method: "post",
      params: this.params({
        act: "index"
      })
    })
      .then(res => {
        this.Participation.hotel = res.data.result.About_us.hotel;
        this.Participation.registration_fee =
          res.data.result.About_us.registration_fee;
        this.Participation.url_now = res.data.result.About_us.url_now;
      })
      .catch(err => {
        console.log(err);
      });
  }
};
</script>
<style lang="less" scoped>
.info-box {
  width: 100%;
  background: #fff;
  border: 1px solid #eaeaea;
  margin-top: 20px;
  .head {
    width: 100%;
    height: 40px;
    background: url("../assets/img/userCenterHeadBg.png") no-repeat center;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    padding-left: 18px;
    color: #fff;
    box-sizing: border-box;
  }
  .body {
    width: 100%;
    padding: 20px;
    box-sizing: border-box;
    button,
    a.button {
      width: 260px;
      display: flex;
      padding: 15px 40px;
      box-sizing: border-box;
      background: #2aace8;
      color: #fefefe;
      border: 0;
      margin: 20px auto;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    p {
      font-family: ArialMT;
      font-size: 16px;
      color: #444;
      line-height: 32px;
    }
    table {
      border: 1px solid #eaeaea;
      margin-top: 20px;
      width: 100%;
      tr td {
        height: 66px;
        text-align: center;
        border: 1px solid #eaeaea;
        a {
          color: #2aace8;
        }
      }
      tr td:nth-child(1),
      tr td:nth-child(3) {
        width: 198px;
      }
    }
    .table1,
    .table2 {
      tr:first-child td {
        font-weight: 700;
      }
    }
  }
}
</style>


