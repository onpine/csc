<template>
  <div id="content">
    <div id="wenzhang_type">
      <!-- <h2>文章类型</h2>
      <ul v-for="(type, index1) in type_list" :key="index1">
        <li>
          <a href="#" @click="check(type.key)">{{type.type}}</a>
        </li>
      </ul>-->
    </div>
    <div id="tuijian_wenzhang">
      <h2>推荐文章</h2>
      <ol>
        <li v-for="(item, index2) in list" :key="index2">
          <div class="otherwise">
            <span id="auther">来自：{{item.user.username}}</span>
            <span id="time">{{item.web.subTime}}</span>
          </div>
          <div class="wname">
            <img :src="'http://121.199.27.93/img/'+item.user.imgpath" alt="头像" />
            <!-- <a @click="changego(href+item.web.id)"> -->
            <a :href="href+'/BlogArticle/'+item.web.id" target="_blank">
              <!-- <a href="href+item.web.id"> -->
              <b>{{item.web.type}}</b>
              {{item.web.title}}
            </a>
          </div>
          <div class="otherwise">
            <!-- <span class="biao">{{ item.type }}</span> -->
            <span>收藏:{{ item.collection }}</span>
            <span>阅读:{{ item.visit }}</span>
            <span>点赞:{{ item.thumbs }}</span>
          </div>
        </li>
        <div class="refresh">
          <input type="button" @click="refresh()" value="刷新" />
        </div>
        <li v-for="(item3, index3) in oldlist" :key="'old'+index3">
          <div class="otherwise">
            <span id="auther">来自：{{item3.user.username}}</span>
            <span id="time">{{item3.web.subTime}}</span>
          </div>
          <div class="wname">
            <img :src="'http://121.199.27.93/img/'+item3.user.imgpath" alt="头像" />
            <!-- <a @click="changego(href+item.web.id)"> -->
            <a :href="href+'/BlogArticle/'+item3.web.id" target="_blank">
              <b>{{item3.web.type}}</b>
              {{item3.web.title}}
            </a>
          </div>
          <div class="otherwise">
            <!-- <span class="biao">{{ item.type }}</span> -->
            <span>收藏:{{ item3.collection }}</span>
            <span>阅读:{{ item3.visit }}</span>
            <span>点赞:{{ item3.thumbs }}</span>
          </div>
        </li>
      </ol>
    </div>
    <div id="tuijian_yonghu">
      <!-- <h2>推荐用户</h2>
      <ul v-for="(user, index3) in users_list" :key="index3">
        <li>
          <a :href="user.href">{{user.name}}</a>
        </li>
      </ul>-->
    </div>
  </div>
</template>
<script>
import Axios from "axios";
import store from "../store";
export default {
  data() {
    return {
      type_list: [
        { type: "java", key: "java" },
        { type: "html", key: "html" },
        { type: "前端", key: "ui" },
        { type: "后端", key: "houduan" },
        { type: "全栈", key: "quanzhan" },
        { type: "Vue", key: "vue" },
      ],
      users_list: [
        { name: "Erika Selin", href: "./#" },
        { name: "Erika Selin", href: "./#" },
        { name: "Erika Selin", href: "./#" },
        { name: "Erika Selin", href: "./#" },
        { name: "Erika Selin", href: "./#" },
      ],
      list: [],
      oldlist: [],
      blobList: [],
      href: "/#/HelloWorld",
      pageNum: 1,
      pageSize: 10,
    };
  },
  methods: {
    /**
     * 获取首页数据，http://121.199.27.93/blob/getwebindex
     * json{"code" : "200",
     * "msg" : "success",
     * "webList" : "网页信息集合",
     * "userList", "用户信息集合",
     * "visitList", "访问量集合",
     * "thumbsList" : "点赞量集合",
     * "collectionList" : "收藏量集合"}
     */
    getwebindex() {
      let that = this;
      Axios.get("/blob/getwebindex", {
        params: {},
        headers: {
          "X-Requested-With": "XMLHttpRequest",
        },
      })
        .then((Response) => {
          // console.log(Response.data.json);
          if (Response.data.json.code == 200) {
            // console.log(
            //   Response.data.json.code + "..." + Response.data.json.msg
            // );
            that.blobList = {
              ...{},
              ...Response.data.json.blobList,
            };
            that.pageNum = that.blobList.pageNum;
            that.pageSize = that.blobList.pageSize;
            // console.log(that.pageNum + "..." + that.pageSize);
            that.list = [...[], ...that.blobList.list];
            that.oldlist = [];
            for (const iter of that.list) {
              if (iter.web.contype == 1) {
                iter.web.type = "原创";
              } else {
                iter.web.type = "转载";
              }
            }
            // console.log(that.list);
            // console.log(that.oldlist);
          } else {
            console.log(
              Response.data.json.code + "..." + Response.data.json.exception
            );
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //路由跳转
    changego(event) {
      this.$router.push({ path: event });
    },
    //新标签路由跳转
    resolve(event) {
      let routeData = this.$router.resolve({
        path: event,
      });
      window.open(routeData.href, "_blank");
    },
    //刷新推荐文章
    refresh() {
      let that = this;
      if (this.pageNum >= this.pageSize) return;
      Axios.get("/blob/getwebindex", {
        params: {
          p: that.pageNum + 1,
        },
        headers: {
          "X-Requested-With": "XMLHttpRequest",
        },
      })
        .then((Response) => {
          // console.log(Response.data.json);
          if (Response.data.json.code == 200) {
            // console.log(
            //   Response.data.json.code + "..." + Response.data.json.msg
            // );
            that.blobList = {
              ...{},
              ...Response.data.json.blobList,
            };
            that.pageNum = that.pageNum + 1;
            that.pageSize = that.blobList.pageSize;
            // console.log(that.pageNum + "..." + that.pageSize);
            that.oldlist = [...that.list, ...that.oldlist];
            that.list = [...that.blobList.list.reverse(), ...[]];
            for (const iter of that.list) {
              if (iter.web.contype == 1) {
                iter.web.type = "原创";
              } else {
                iter.web.type = "转载";
              }
            }
            // console.log(that.list);
            // console.log(that.oldlist);
            document.body.scrollIntoView({
              behavior: "smooth",
              block: "start",
            });
          } else {
            alert(
              Response.data.json.code + "..." + Response.data.json.exception
            );
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    document.title = "CodeSharingCommunity";
    if (this.$store.getters.getsessionId != "") {
      this.href = "/#/LoggingStatus";
    }
    this.getwebindex();
  },
};
</script>

<style scoped>
#content {
  display: flex;
  /* overflow: scroll; */
  min-height: calc(100vh - 112px);
}
#wenzhang_type {
  position: relative;
  width: 20%;

  background-color: #eeeeee;
  margin: 0;
  padding: 0;
  box-shadow: 0 0 10px 0 #333333;
}
#tuijian_wenzhang {
  position: relative;
  width: 60%;
  background-color: #eeeeee;
  /* background-color: white; */
  margin: 0;
  padding: 0;
}
#tuijian_yonghu {
  width: 20%;
  background-color: #eeeeee;
  margin: 0;
  padding: 0;
  box-shadow: 0 0 10px 0 #333333;
}
ul li {
  line-height: 35px;
}
ul li a:hover {
  color: steelblue;
}
a {
  text-align: left;
  text-decoration: none;
  color: black;
}
#wenzhang_type ul {
  padding: 0 20%;
  margin: auto 10%;
  font-weight: 600;
}
#tuijian_wenzhang ol {
  list-style-type: none;
  padding: 0 10%;
}
#tuijian_wenzhang li {
  margin: 10px 0;
  padding: 20px 5%;
  border-radius: 10px;

  background-color: white;
}

#tuijian_wenzhang li:hover {
  /* border-left: orangered solid 5px; */
  background-color: #eeeeee;
  box-shadow: 0 0 5px 0 white;
}
#tuijian_wenzhang div {
  /* height: 20px; */
  margin: 0;
}
.wname {
  text-align: left;
  /* margin: 10px 0; */
  padding: 10px 5%;
  height: 30px;
}
.wname a {
  /* display: block; */
  cursor: pointer;
  line-height: 30px;
  height: 30px;
  padding: 0 10px;
  font-size: 20px;
  font-weight: 600px;
}
.wname b {
  line-height: 20px;
  padding: 5px 5px;
  height: 30px;
  font-size: 15px;
  font-weight: 600;
  color: #333333;
  background-color: rgb(255, 181, 181);
  border-radius: 5px;
}
.wname img {
  width: 30px;
  height: 30px;
  float: left;
  border-radius: 15px;
}
.note {
  display: flex;
  justify-content: space-between;
}
.note span {
  margin: 10px;
}
.otherwise {
  display: flex;
  justify-content: space-between;
  /* background-color: rgb(230, 230, 230); */
  /* line-height: 30px; */
  width: 90%;
  padding: 0 5%;
  border-radius: 5px;
}
/* .biao {
  background-color: rgb(231, 146, 146);
} */
.otherwise span {
  display: block;
  /* line-height: 30px;
  height: 30px;
  */
  color: #9d9ea7;
  font-size: 12px;
}
h2 {
  text-align: center;
}
.refresh {
  width: 100%;
  height: 55px;
  margin: 0;
}
.refresh input[type="button"] {
  padding: 0;
  width: 80%;
  height: 45px;
  background-color: white;
  outline: none;
  border: 0;
  border-radius: 10px;
  cursor: pointer;
  margin: 0 10%;
}
.refresh input[type="button"]:hover {
  background-color: #eeeeee;
  box-shadow: 0 0 5px 0 white;
}
#tuijian_wenzhang span {
  line-height: 20px;
  font-size: 12px;
  font-weight: 500;
}
#tuijian_yonghu ul {
  list-style-type: none;
  padding: 0;
  font-weight: 600;
}
</style>