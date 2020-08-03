<template>
  <div id="my_fans">
    <ul>
      <li>
        <div class="img">
          <img src="../assets/item-5.jpg" alt="img" />
          <b>onpine</b>
          <input type="button" value="关注" />
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
      userList: {},
    };
  },
  methods: {
    /**
     * getoneselffans获取自己的粉丝
     */
    getoneselffans() {
      let that = this;
      Axios.get("/user/getoneselffans", {
        params: {
          p: 1,
        },
        headers: { Authorization: this.$store.getters.getsessionId },
      })
        .then((Response) => {
          console.log(Response.data.json);
          switch (Response.data.json.code) {
            case 200:
              that.userList = { ...{}, ...Response.data.json.userList };
              break;

            default:
              console.log("getoneselffans" + Response.data.json.exception);
              break;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    document.title = "我的粉丝 - CodeSharingCommunity";
    this.getoneselffans();
  },
};
</script>

<style scoped>
#my_fans {
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
  height: 30px;
}
.img {
  display: flex !important;
  width: 100px;
  height: 30px;
}
.img img {
  height: 30px;
  margin: 0 10px;
  border-radius: 50%;
}
.img b {
  margin: 0;
  line-height: 30px;
  height: 30px;
  margin: 0 10px;
}
.img input[type="button"] {
  display: inline-block;
  border: 0;
  /* width: 100px; */
  padding: 0 10px;
  line-height: 30px;
  outline: 0;
  margin: 0 10px;
  cursor: pointer;
}
</style>