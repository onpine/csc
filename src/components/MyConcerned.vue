<template>
  <div id="my_concerned">
    <ul>
      <li>
        <div class="img">
          <img src="../assets/fang03.jpg" alt="img" />
          <b>onpine</b>
          <input type="button" value="取消" />
        </div>
      </li>
      <li>
        <div class="img">
          <img src="../assets/fang03.jpg" alt="img" />
          <b>onpine</b>
          <input type="button" value="取消" />
        </div>
      </li>
      <li>
        <div class="img">
          <img src="../assets/fang03.jpg" alt="img" />
          <b>onpine</b>
          <input type="button" value="取消" />
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {},
  mounted() {
    document.title = "我的关注 - CodeSharingCommunity";
  },
};
</script>

<style scoped>
#my_concerned {
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