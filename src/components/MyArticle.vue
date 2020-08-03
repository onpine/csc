<template>
  <div class="my_article">
    <ul>
      <li v-for="(aitem, aindex) in list" :key="aindex">
        <h5 @click="resolve(aitem.web.id)">{{ aitem.web.title }}</h5>
        <a @click="deleteblob(aitem.web.id)" class="del">删除</a>
        <a @click="updatablob(aitem.web.id)" class="upd">编辑</a>
        <div class="otherwise">
          <span class="biao">{{ aitem.web.type }}</span>
          <span>阅读:{{ aitem.visit }}</span>
          <span>{{ aitem.web.subTime }}</span>
          <!-- <span>点赞:{{ item.thubms }}</span> -->
        </div>
      </li>
    </ul>
  </div>
</template>
<script>
import Axios from "axios";
import store from "../store";
import qs from "qs";
export default {
  data() {
    return {
      // arts: [
      //   {
      //     webid: 10,
      //     tit: "题目标题。。。。长一点",
      //     type: "原创",
      //     visit: 20,
      //     thubms: 30,
      //     collection: 25,
      //   },
      // ],
      // userList: [],
      blobList: [],
      list: [],
    };
  },
  methods: {
    /**
     * 删除指定文章
     * delete
     */
    deleteblob(webid) {
      let that = this;
      Axios.post(
        "/blob/delete",
        qs.stringify({
          webid: webid,
        }),
        { headers: { Authorization: this.$store.getters.getsessionId } }
      ).then((Response) => {
        console.log(Response.data.json);
        switch (Response.data.json.code) {
          case 200:
            alert("删除博客..." + Response.data.json.msg);
            that.getoneselfblobs();
            break;

          default:
            console.log("删除博客..." + Response.data.json.exception);
            alert("删除博客..." + Response.data.json.exception);
            break;
        }
      });
    },
    /**
     * 更新文章
     * updata
     */
    updatablob(webid) {
      let routeData = this.$router.resolve({
        path: "/LoggingStatus/BlogEditor/" + webid,
      });
      window.open(routeData.href, "_blank");
    },
    /**
     * 获取自己的博客
     * Authorization
     */
    getoneselfblobs() {
      let that = this;
      Axios.get("/user/getoneselfblobs", {
        params: {
          p: 1,
        },
        headers: { Authorization: this.$store.getters.getsessionId },
      })
        .then((Response) => {
          // console.log(Response.data.json.blobList);
          switch (Response.data.json.code) {
            case 200:
              {
                // that.blobList = [...[], ...Response.data.json.blobList];
                that.list = [...[], ...Response.data.json.blobList.list];
                for (const key in that.list) {
                  if (that.list[key].web.contype == "1") {
                    that.list[key].web.type = "原创";
                  } else {
                    that.list[key].web.type = "转载";
                  }
                }
              }
              break;

            default:
              console.log("我的博客..." + Response.data.json.exception);
              break;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //查看文章
    goArticle(webid) {
      console.log(webid);
      this.$router.push({ path: `/LoggingStatus/BlogArticle/${webid}` });
    },
    //新标签路由跳转
    resolve(webid) {
      let routeData = this.$router.resolve({
        path: `/LoggingStatus/BlogArticle/${webid}`,
      });
      window.open(routeData.href, "_blank");
    },
  },
  created() {
    this.getoneselfblobs();
  },
  mounted() {
    document.title = "我的文章-CodeSharingCommunity";
  },
};
</script>
<style scoped>
#my_article {
  height: auto;
  width: auto;
}
ul {
  list-style-type: none;
  width: 100%;
  margin: auto 0;
  padding: 0;
}
li {
  /* padding-top: 20px; */
  /* height: 100px; */
  background-color: white;
  margin: 20px 0;
  overflow: hidden;
}
li h5 {
  float: left;
  margin: 0;
  text-align: left;
  padding: 0 20px;
  line-height: 30px;
  font-size: 16px;
  cursor: pointer;
  display: inline-block;
}
li a {
  float: right;
  display: inline-block;
  width: 30px;
  line-height: 30px;
  font-size: 12px;
  cursor: pointer;
}
.del {
  color: crimson;
}
.upd {
  color: dodgerblue;
}
li h5:hover {
  color: gray;
}
.otherwise {
  display: flex;
  justify-content: space-between;
  /* background-color: rgb(230, 230, 230); */
  height: 30px;
  width: 80%;
  padding: 0 20px;
  border-radius: 5px;
}
/* .biao {
  background-color: rgb(231, 146, 146);
} */
.otherwise span {
  display: block;
  line-height: 30px;
  color: #9d9ea7;
  font-size: 12px;
}
</style>