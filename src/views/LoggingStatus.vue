<template>
  <div id="loggingstatus">
    <LoggingHeader />
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
    <!-- <PersonalCenter /> -->
    <!-- <BlogArticle /> -->
    <Footer />
  </div>
</template>
<script>
import LoggingHeader from "../components/LoggingHeader";
import Content from "../components/Content.vue";
import Header from "../components/Header.vue";
import Footer from "../components/Footer";
import BlogEditor from "../components/BlogEditor";
import BlogArticle from "../components/BlogArticle";
import PersonalCenter from "../components/PersonalCenter";
import axios from "axios";
import store from "../store";

export default {
  name: "loggingstatus",
  components: {
    LoggingHeader,
    Content,
    Header,
    Footer,
    BlogEditor,
    BlogArticle,
    PersonalCenter
  },
  methods: {
    loggingCheck() {
      var kie = document.cookie.split("; ");
      var key = false;
      for (var item of kie) {
        var one = item.split("=");
        //将cookie里的sessionId数据set到store里
        if (one[0] == "sessionId") {
          key = true;
          //sessionId不为空,有登录记录
          if (one[1] != "") {
            this.$store.commit("setsessionId", one[1]);
            if (this.loginstatus()) {
              console.log("loggingstatus已登录..." + one[0] + "..." + one[1]);
              alert("loggingstatus已登录..." + one[0] + "..." + one[1]);
            } else {
              console.log("sessionId无效");
              // alert("loggingstatus未登录，空值");
              this.$router.push({ path: "/HelloWorld/Rogon" });
            }
            //验证已登录后，由store中sessionId获取登录状态,将登陆状态放入store

            return;
          } else {
            console.log("loggingstatus未登录，空值");
            alert("loggingstatus未登录，空值");
            this.$router.push({ path: "/HelloWorld/Rogon" });
          }
        }
      }
      //cookie无sessionId
      if (!key) {
        console.log("loggingstatus=无登录记录！cookie无sessionId");
        alert("loggingstatus=无登录记录！cookie无sessionId");
        this.$router.push({ path: "/HelloWorld/Rogon" });
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
            Authorization: session
          }
        })
        .then(Response => {
          if (Response.data.json.code == 200) {
            console.log("username=" + Response.data.json.user.username);
            //将登陆状态放入store
            that.$store.commit("setuser", Response.data.json.user);
            val = true;
          } else {
            alert(
              Response.data.json.code + "..." + Response.data.json.exception
            );
            this.$store.commit("setsessionId", "");
            document.cookie = "sessionId='',path=/";
            val = false;
          }
        })
        .catch(error => {
          console.log(error);
        });
      return val;
    }
  },
  created() {
    // this.loggingCheck();
  }
};
</script>