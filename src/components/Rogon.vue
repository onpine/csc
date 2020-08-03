<template>
  <div id="rogon">
    <div id="UpList">
      <ul>
        <li>账号</li>
        <li>
          <input type="text" name="uid" v-model="user.uid" placeholder="学号/工号" id="upuser" />
        </li>
        <li>密码</li>
        <li>
          <input
            type="password"
            name="password"
            v-model="user.password"
            placeholder="password"
            id="password"
          />
        </li>
        <li>验证码</li>
        <li class="code">
          <input type="text" v-model="user.code" placeholder="验证码" id="code" />
          <img @click="getcodeforlogin()" id="imgCode" :src="imgsrc" alt="验证码" />
        </li>

        <li>
          <button type="submit" @click="uppush()">登录</button>
        </li>
        <li style="margin:0">
          <a @click="changego('/HelloWorld/ChangePassword')">忘记密码</a>
          <!-- <a @click="exit()">退出</a> -->
          <!-- <a @click="Tcookie()">cookie</a> -->
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import store from "../store";
import qs from "qs";
export default {
  name: "Rogon",
  data() {
    return {
      user: {
        uid: "",
        password: "",
        hit: "",
        code: "",
        flag: 0,
      },
      userStatus: {},
      AccessToken: "",
      // imgsrc: require("../assets/caotu.png")
      imgsrc: "",
      fromRoute: "HelloWorld",
    };
  },
  methods: {
    //退出
    exit() {
      let that = this;
      axios
        .get("/user/logout", {
          params: {},
          headers: {
            //必须加Authorization请求头
            Authorization: this.AccessToken,
          },
        })
        .then((Response) => {
          console.log("登出：" + Response.data.msg);
          document.cookie = "sessionId=" + "; path=/;";
          that.$store.commit("setuser", "");
          that.$store.commit("setsessionId", "");
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //刷新图片验证码
    getcodeforlogin() {
      let that = this;
      axios
        .get("/user/getcodeforlogin", {
          params: {},
          headers: {
            "X-Requested-With": "XMLHttpRequest",
          },
        })
        .then((Response) => {
          let resobj = {};
          resobj = { ...resobj, ...Response.data.json };
          if (resobj.msg != "success") {
            alert("获取验证码：" + resobj.code);
          } else {
            console.log("获取验证码：" + resobj.msg);
            that.imgsrc = resobj.img;
            that.user.hit = resobj.token;
            document.cookie = "token=" + that.user.hit;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //登录提交，检查请求验证
    uppush() {
      let that = this;
      axios
        .post(
          "/user/login",
          qs.stringify({
            uid: this.user.uid,
            password: this.user.password,
            hit: this.user.hit,
            code: this.user.code,
            flag: this.user.flag,
          })
        )
        .then((Response) => {
          if (Response.data.json.code == 200) {
            this.userStatus = {
              ...this.userStatus,
              ...Response.data.json.user,
            };
            //最终时间
            // var date = new Date();
            // var milsecond = that.getExpireTime();
            // date.setTime(date.getTime() + milsecond);
            /**
             * 记录user
             *  cookie与store
             * */
            // document.cookie =
            //   "user=" +
            //   JSON.stringify(this.userStatus) +
            //   "; path=/;expires = " +
            //   date.toGMTString();
            that.$store.commit("setuser", this.userStatus);
            /**
             * 记录sessionId
             *  cookie与store
             */
            console.log("AccessToken=" + Response.data.json.AccessToken);
            that.AccessToken = Response.data.json.AccessToken;
            document.cookie =
              "sessionId=" + Response.data.json.AccessToken + "; path=/;";
            that.$store.commit("setsessionId", Response.data.json.AccessToken);
            /**
             * 成功提示与页面跳转
             */
            alert(Response.data.json.msg);

            /**
             * 未成功的测试，从LoggingStatus路径下返回时未能获取from路径
             * 当登录成功时，判断是页面回跳或是进入默认页面
             */

            if (Response.data.json.msg == "success") {
              if (that.fromRoute.split("/")[1] == "LoggingStatus") {
                that.$router.go(-1);
              } else {
                that.$router.push({ path: "/LoggingStatus/PersonalCenter" });
              }
            }
          } else {
            console.log(Response.data.json);
            if (Response.data.json.exception == "你已登录") {
              alert(Response.data.json.exception);
              that.$router.push({ path: "/LoggingStatus/PersonalCenter" });
            } else {
              alert(
                Response.data.json.code + "..." + Response.data.json.exception
              );
            }
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //测试cookie
    Tcookie() {
      //最终时间
      var date = new Date();
      console.log(date.toGMTString());
      var milsecond = this.getExpireTime();
      date.setTime(date.getTime() + milsecond);
      console.log(date.toGMTString());
      console.log(JSON.stringify(this.user));
      document.cookie =
        "user=" +
        JSON.stringify(this.user) +
        "; path=/;expires = " +
        date.toGMTString();
    },
    //延时到今日结束
    getExpireTime() {
      var date = new Date();
      var hour = 23 - date.getHours();
      var min = 59 - date.getMinutes();
      var sec = 59 - date.getSeconds();
      var ms = (3600 * hour + 60 * min + sec) * 1000;
      return ms;
    },
    //父组件动态引用组件，本登录组件(Rogon)改为ChangePassword组件
    changego(event) {
      this.$router.push({ path: event });
      // this.$emit("onGoto", event);
    },
    /**
     * 检查cookie，判断是否已登陆
     */
    firstCheck() {
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
            console.log(this.loginstatus());
            if (this.loginstatus()) {
              this.AccessToken = one[1];
              //验证已登录后，由store中sessionId获取登录状态,将登陆状态放入store
              this.$router.push({
                path: "/LoggingStatus/PersonalCenter",
              });
            } else {
              console.log("Rogon登录为空");
            }
          } else {
            console.log("sessionId为空");
          }
        }
      }
      //cookie无sessionId
      if (!key) {
        console.log("Rogon无登录记录！cookie无sessionId");
      }
    },
    /**
     * 验证已登录后，由store中sessionId获取登录状态,将登陆状态放入store
     */
    loginstatus() {
      let that = this;
      let session = this.$store.getters.getsessionId;
      let val = true;
      axios
        .get("/user/loginstatus", {
          params: {},
          headers: {
            Authorization: session,
          },
        })
        .then((Response) => {
          if (Response.data.json.code == 200) {
            console.log("登录为" + Response.data.json.user.username);
            //将登陆状态放入store
            that.$store.commit("setuser", Response.data.json.user);
            val = true;
          } else {
            console.log(
              Response.data.json.code + "..." + Response.data.json.exception
            );
            val = false;
          }
        })
        .catch((error) => {
          console.log(error);
        });
      return val;
    },
  },
  created() {
    /**
     * 检查cookie，判断是否已登陆
     */
    this.firstCheck();
  },
  watch: {
    $route: function (to, from) {
      this.fromRoute = from.path;
      console.log(this.fromRoute.split("/")[1]);
    },
  },
  mounted() {
    document.title = "登录 - CodeSharingCommunity";
    this.getcodeforlogin();
  },
};
</script>
<style scoped>
#rogon {
  display: flex;
  /* overflow: scroll; */
  min-height: calc(100vh - 112px);
  /* background-color: saddlebrown; */
  background-image: url("../assets/rbk.jpg");
  background-size: cover;
  backface-visibility: visible;
  background-repeat: no-repeat;
}
#UpList {
  width: 300px;
  height: 390px;
  margin: auto;
  background-color: cadetblue;
}

ul {
  list-style: none;
  padding: 0 10%;
  height: 220px;
  margin: 40px 0;
}
li {
  display: block;
  margin: 15px 0;
  color: white;
}
input {
  width: 90%;
  height: 28px;
  padding: 0 10px;
  border: 0;
  outline: none;
}
#imgCode {
  height: 28px;
  width: 30%;
  float: right;
  cursor: pointer;
}
#code {
  display: inline;
  width: 58%;
  float: left;
}
button {
  margin: 15px 0;
  background-color: transparent;
  border: 0;
  font-size: 20px;
  color: white;
  cursor: pointer;
  outline: 0;
}
button:hover {
  color: black;
}
a {
  text-decoration: none;
  color: white;
  font-size: 10px;
  line-height: 18px;
  cursor: pointer;
}
a:hover {
  color: black;
}
</style>