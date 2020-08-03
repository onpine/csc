<template>
  <div id="my_data">
    <dl>
      <dt>
        <b>UID:</b>
      </dt>
      <dd v-show="true">
        <span>{{user.id}}</span>
      </dd>
    </dl>
    <dl>
      <dt>
        <b>帐号:</b>
      </dt>
      <dd v-show="true">
        <span>{{user.uid}}</span>
      </dd>
    </dl>
    <dl>
      <dt>
        <b>昵称:</b>
      </dt>
      <dd v-show="status">
        <span>{{user.username}}</span>
      </dd>
      <dd>
        <input
          v-show="! status"
          v-model="user.username"
          type="text"
          name="data_username"
          id="data_username"
          placeholder="昵称"
        />
      </dd>
    </dl>
    <dl v-show="true">
      <dt>
        <b>邮箱:</b>
      </dt>
      <dd>
        <span>{{user.time}}</span>
      </dd>
    </dl>
    <!-- <dl v-show="!status">
      <dt>
        <b>密码:</b>
      </dt>
      <dd>
        <input
          v-show="! status"
          v-model="user.password"
          type="text"
          name="data_password"
          id="data_password"
          placeholder="密码"
        />
      </dd>
    </dl>-->
    <dl>
      <dt>
        <b>位置:</b>
      </dt>
      <dd v-show="status">
        <span>{{user.clazz}}</span>
      </dd>
      <dd>
        <input
          v-show="! status"
          v-model="user.clazz"
          type="text"
          name="data_clazz"
          id="data_clazz"
          placeholder="班级/办公室"
        />
      </dd>
    </dl>
    <dl>
      <dt>
        <b>院系:</b>
      </dt>
      <dd v-show="status">
        <span>{{user.college}}</span>
      </dd>
      <dd>
        <input
          v-show="! status"
          v-model="user.college"
          type="text"
          name="data_department"
          id="data_department"
          placeholder="院系"
        />
      </dd>
    </dl>

    <dl>
      <dd>
        <input v-show="status" @click="change()" type="button" value="修改" />
        <input v-show="! status" @click="changeinfor()" type="submit" value="确认" />
      </dd>
      <dd>
        <input v-show="! status" @click="change()" type="button" value="取消" />
      </dd>
    </dl>
  </div>
</template>
<script>
import Axios from "axios";
import store from "../store";
import qs from "qs";
export default {
  data() {
    return {
      user: {
        imgpath:
          "https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2603095696,3544091004&fm=26&gp=0.jpg",
      },
      webid: this.$store.getters.getuser.uid,
      status: true,
      id: 181360214,
      // email: "",
      // username: "",
      // clazz: "",
      // college: "",
      // identity: "",
    };
  },
  methods: {
    /**
     * changeinfor修改用户信息
     * POST 请求头: Authorization
     */
    changeinfor() {
      let that = this;
      Axios.post(
        "/user/changeinfor",
        qs.stringify({
          uid: this.user.uid,
          username: this.user.username,
          college: this.user.college,
          clazz: this.user.clazz,
        }),
        {
          headers: { Authorization: this.$store.getters.getsessionId },
        }
      )
        .then((Response) => {
          console.log(Response.data.json);
          switch (Response.data.json.code) {
            case 200:
              that.user = { ...that.user, ...Response.data.json.user };
              that.status = true;
              alert("修改..." + Response.data.json.msg);
              break;

            default:
              alert("修改..." + Response.data.json.exception);
              break;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    /**
     * 从cookie检查登录状态
     */
    loggingCheck() {
      var kie = document.cookie.split("; ");
      var key = false;
      for (var item of kie) {
        var one = item.split("=");
        //将cookie里的sessionId数据set到store里
        if (one[0] == "sessionId") {
          key = true;
          //sessionId不为空,有登录
          if (one[1] != "") {
            this.$store.commit("setsessionId", one[1]);
            if (this.loginstatus()) {
              // console.log("loggingHeader已登录..." + one[0] + "..." + one[1]);
              // alert("loggingHeader已登录..." + one[0] + "..." + one[1]);
            } else {
              // console.log("loggingHeader未登录，空值");
              // alert("loggingHeader未登录，空值");
              this.$router.push({ path: "/HelloWorld/Rogon" });
            }
            //验证已登录后，由store中sessionId获取登录状态,将登陆状态放入store

            return;
          } else {
            // console.log("loggingHeader未登录，空值");
            // alert("loggingHeader未登录，空值");
            this.$router.push({ path: "/HelloWorld/Rogon" });
          }
        }
      }
      //cookie无sessionId
      if (!key) {
        // console.log("loggingHeader=无登录记录！cookie无sessionId");
        // alert("loggingHeader=无登录记录！cookie无sessionId");
        this.$router.push({ path: "/HelloWorld/Rogon" });
      }
    },
    /**
     * 验证已登录后，由store中sessionId获取登录状态,将登陆状态放入store
     */
    loginstatus() {
      let that = this;
      let session = this.$store.getters.getsessionId;
      //图片提前填充
      let val = true;
      Axios.get("/user/loginstatus", {
        params: {},
        headers: {
          Authorization: session,
        },
      })
        .then((Response) => {
          if (Response.data.json.code == 200) {
            // console.log("username=" + Response.data.json.user.username);
            //将登陆状态放入store
            that.$store.commit("setuser", Response.data.json.user);
            // console.log(Response.data.json.user);
            that.user = { ...this.user, ...this.$store.getters.getuser };
            that.imgsrc =
              "http://121.199.27.93/img/" + Response.data.json.user.imgpath;

            val = true;
          } else {
            that.imgsrc =
              "https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2603095696,3544091004&fm=26&gp=0.jpg";
            that.username = "username";
            // console.log(
            //   Response.data.json.code + "..." + Response.data.json.exception
            // );
            this.$store.commit("setsessionId", "");

            document.cookie = "user='',path=/";
            document.cookie = "sessionId='',path=/";
            val = false;
            // this.$router.push({ path: "/HelloWorld/Rogon" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
      return val;
    },
    change() {
      this.status = !this.status;
      console.log(this.status);
    },
    changesubmit() {
      alert("确认更改");
    },
  },
  created() {
    // this.webid = this.$store.getters.getuser.uid;
    // console.log(this.$store.getters.getuser);
  },
  mounted() {
    // console.log(this.$store.getters.getuser);
    this.loggingCheck();
    document.title = "我的信息 - CodeSharingCommunity";
  },
};
</script>
<style scoped>
#my_data {
  height: auto;
  width: auto;
}
dl {
  display: block;
  width: 80%;
  padding: 0 10%;
  float: left;
  text-align: left;
  height: 36px;
  /* background-color: lightgoldenrodyellow; */
}
dt,
dd {
  display: inline-block;
  height: 36px;
  margin-left: 18px;
  float: left;
}
dt b {
  font-size: 20px;
  line-height: 36px;
}
dd span {
  line-height: 36px;
  height: 36px;
  font-size: 14px;
}
dd input {
  border-radius: 5px;
}
dd input[type="text"] {
  display: inline-block;
  height: 30px;
  width: 160px;
  outline: none;
  border: black 2px solid;
  border-radius: 5px;
  padding-left: 5px;
}
dd input[type="button"] {
  background-color: cornflowerblue;
  display: inline-block;
  width: 80px;
  line-height: 30px;
  outline: none;
  cursor: pointer;
  border: none;
}
dd input[type="submit"] {
  background-color: dodgerblue;
  display: inline-block;
  width: 80px;
  line-height: 30px;
  outline: none;
  cursor: pointer;
  border: none;
}
</style>