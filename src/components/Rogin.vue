<template>
  <div id="rogin">
    <div id="mycontent">
      <h1 style="font-size:3em">PDSU·S607·SCS</h1>
      <h1>新冠病毒肺炎疫情期间共同居家办公，学习</h1>
    </div>
    <div id="InList">
      <div>
        <ul>
          <li>
            <b>学号：</b>
            <input type="text" name="uid" id="uid" placeholder="学号/工号" v-model="user.uid" />
          </li>
          <li>
            <b>密码：</b>
            <input
              type="password"
              name="password"
              id="password"
              placeholder="密码"
              v-model="user.password"
            />
          </li>
          <!-- <li>
          <b>确认密码：</b>
          <input type="password" name="repassword" id="repassword" placeholder="确认密码" />
          </li>-->

          <li>
            <b>姓名：</b>
            <input
              type="text"
              name="username"
              id="username"
              placeholder="姓名"
              v-model="user.username"
            />
          </li>
          <li>
            <b>院系：</b>
            <input type="text" name="college" id="college" placeholder="院系" v-model="user.college" />
          </li>
          <li>
            <b>班级/办公室：</b>
            <input
              type="text"
              name="clazz"
              id="clazz"
              placeholder="选填(班级/办公室)"
              v-model="user.clazz"
            />
          </li>
          <!-- <li>
            <b>年龄：</b>
            <input type="text" name="age" id="age" placeholder="年龄" v-model="user.age" />
          </li>-->
          <li>
            <b>邮箱：</b>
            <div style="width:224px;margin:auto;height: 35px;">
              <input
                class="in_email"
                type="email"
                name="email"
                id="email"
                placeholder="邮箱"
                v-model="email"
              />
              <input class="enter_email" type="button" value="获取验证码" @click="getcodeforapply()" />
            </div>
          </li>
          <li>
            <b>邮箱验证码：</b>
            <input type="text" name="code" id="code" placeholder="邮箱验证码" v-model="code" />
          </li>
          <!-- <li>
            <b>性别：</b>
            <input type="radio" name="sex" id="man" checked="checked" />
            <strong>男</strong>
            <input type="radio" name="sex" id="women" />
            <strong>女</strong>
          </li>-->

          <li>
            <input type="checkbox" name="check" id="check" placeholder="同意用户协议" />
            <strong>
              同意我们的
              <a @click="changego('/HelloWorld/UserAgreement')">用户协议</a>
            </strong>
          </li>
          <li>
            <button type="submit" @click="inpush()">
              <strong>注册提交</strong>
            </button>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import qs from "qs";
export default {
  name: "Rogin",
  data() {
    return {
      user: {
        id: "",
        uid: "",
        password: "",
        username: "",
        collage: "",
        clazz: "",
        time: "",
        accountStatus: "",
        imgPath: "",
      },
      token: "",
      email: "",
      code: "",
      user_serve_url: "./#",
    };
  },
  methods: {
    //获取邮箱验证码 name email
    getcodeforapply() {
      console.log(this.user.username + "..." + this.email);
      axios
        .get("/user/getcodeforapply", {
          params: {
            email: this.email,
            name: this.user.username,
          },
          headers: {
            "X-Requested-With": "XMLHttpRequest",
          },
        })
        .then((Response) => {
          // console.log(Response.data.json);
          this.token = Response.data.json.token;
          document.cookie = "token=" + this.token;
          console.log(this.user.token);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    /**
     * 注册提交，检查请求验证
     */
    inpush() {
      console.log(this.user);
      // this.$router.push({ path: "/HelloWorld/Rogon" });
      if (document.getElementById("check").checked == false) {
        alert("选择是否同意用户协议");
        return;
      }
      axios
        .post(
          "/user/applynumber",
          qs.stringify({
            uid: this.user.uid,
            password: this.user.password,
            username: this.user.username,
            college: this.user.college,
            clazz: this.user.clazz,
            email: this.email,
            token: this.token,
            code: this.code,
          })
        )
        .then((Request) => {
          console.log(Request);
          alert(Request.data.json.code + "  " + Request.data.json.exception);
          if (Request.data.json.code == 200) {
            console.log(Request.data.json.msg);
            alert(Request.data.json.msg);
            this.$router.push({ path: "/HelloWorld/Rogon" });
          } else {
            console.log(
              Request.data.json.code + "..." + Request.data.json.exception
            );
            alert(Request.data.json.code + "..." + Request.data.json.exception);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //父组件动态引用组件，本Rogon改为UserAgreement组件
    changego(event) {
      this.$router.push({ path: event });
      // this.$emit("go", event);
    },
    //注册成功，路由跳转
    // goto(event) {
    //   this.$emit("goto", event);
    // }
  },
  mounted() {
    document.title = "注册 - CodeSharingCommunity";
  },
};
</script>
<style scoped>
#rogin {
  display: flex;
  /* overflow: scroll; */
  min-height: calc(100vh - 112px);
  /* background-color: saddlebrown; */
  background-image: url("../assets/img-dc.jpg");
  background-size: cover;
  backface-visibility: visible;
  background-repeat: no-repeat;
}
#mycontent {
  width: 500px;
  /* height: 400px; */
  /* background-color: cornflowerblue; */
  color: white;
  margin: auto;
  font-family: "newfont";
}
#InList {
  width: 350px;
  margin: auto;
}
ul {
  width: 350px;
  height: 532px;
  list-style: none;
  padding: 0;
}
li {
  width: 100%;
  float: left;
  margin: 10px 0;
  /* line-height: 39px; */
  min-height: 30px;
}
.in_email {
  float: left;
  width: 120px;
  border: 0;
  outline: 0;
  display: inline-block;
  border-radius: 5px 0 0 5px;
}
.enter_email {
  float: left;
  border-radius: 0px 5px 5px 0px;
  width: 80px;
  cursor: pointer;
  height: 35px;
  display: inline-block;
  outline: 0;
  border: 0;
}
li > b {
  display: none;
}
strong {
  color: white;
}
input {
  height: 35px;
  padding: 0 10px;
  width: 200px;

  border-radius: 5px;
  outline: 0;
  opacity: 0.9;
  border: 0;
}
input:focus {
  border-color: darkslategray;
  /* box-shadow: 0; */
}
input[type="checkbox"] {
  height: 12.8px;
  width: 12.8px;
}
input[type="radio"] {
  height: 12.8px;
  width: 12.8px;
  /* width: 10%; */
  outline: 0;
}
a {
  text-decoration: none;
  color: beige;
  cursor: pointer;
}
button {
  background-color: transparent;
  padding: auto 0;
  border: 0;
  font-size: 20px;
  color: black;
  cursor: pointer;
  outline: 0;
}
button strong:hover {
  color: black;
}
</style>