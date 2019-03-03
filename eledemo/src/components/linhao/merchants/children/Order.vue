<template>
  <div>
    <!-- 头部 -->
    <ul class="header">
      <a @click="back_3()">
        <i class="header_home el-icon-arrow-left backButton"></i>
      </a>
      <span class="position_city">订单列表</span>
      <!-- <router-link to="/home">
        <li class="header_login">切换城市</li>
      </router-link>-->
    </ul>
    <!-- 中間部分 -->
    <ul class="cont">
      <li v-for="(isint ,index) in inmit" :key="index">
        <div class="cont-img">
          <img :src=" '//elm.cangdu.org/img/'+ isint.restaurant_image_url" alt>
        </div>
        <router-link :to="{name:'indentode'}">
          <div class="cont-sp" @click="ko()">
            <span>{{isint.restaurant_name}}</span>
            <p>{{isint.formatted_created_at}}</p>
          </div>
          <p class="cont-p">等待支付</p>
          <div class="sold1"></div>
          <div class="cont-bot">
            <p>ds</p>
            <p>￥{{isint.total_amount}}</p>
          </div>
        </router-link>
        <div class="sold2"></div>

        <span class="cont-span" @click="one()">去支付(還剩00分00秒)</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userId: "",
      inmit: []
    };
  },
  methods: {
    back_3() {
      this.$router.go(-1);
    }
  },
  created() {
    if (localStorage.getItem("user_data")) {
      let get_user = localStorage.getItem("user_data");
      this.user_name = JSON.parse(get_user);
      // console.log(this.user_name);
      this.userId = this.user_name.user_id;
    }
    if (this.userId) {
      this.$http({
        method: "get",
        url:
          "https://elm.cangdu.org/bos/v2/users/" +
          this.userId +
          "/orders?offset=0&limit=20"
      }).then(res => {
        console.log(res);
        if (res.data.length > 0) {
          this.inmit = res.data;
        }
      });
    }
  }
};
</script>


<style scoped>
/* 头部样式 */
.header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 0.45rem;
  background: #3190e8;
  padding: 0 0.1rem;
  font-size: 0.16rem;
  z-index: 111;
}
/* .header .header_login {
  font-size: 0.13rem;
} */
.header_home,
.header_login {
  line-height: 0.45rem;
  color: #fff;
}
.header_home {
  float: left;
  font-size: 0.25rem;
}
.header_login {
  float: right;
}
.position_city {
  position: absolute;
  left: 50%;
  margin-left: -25%;
  width: 50%;
  height: 0.45rem;
  color: #fff;
  line-height: 0.45rem;
  text-align: center;
  font-weight: bolder;
}

.cont {
  margin-top: 0.5rem;
  background-color: white;
  height: 1.6rem;
}

.cont-img {
  margin-top: 0.1rem;
  float: left;
  padding-left: 0.1rem;
}

.cont img {
  width: 0.5rem;
}

.cont-sp {
  float: left;
  margin-top: 0.2rem;
}

.cont-sp span {
  font-size: 0.2rem;
  color: black;
}

.cont-sp p {
  font-size: 0.1rem;
  color: #aaa;
  /* margin-top: 0.05rem; */
}

.cont-p {
  float: right;
  padding: 0.2rem;
  color: black;
}

.cont-bot p:nth-child(1) {
  margin-left: 0.6rem;
  color: black;
}

.cont-bot p:nth-child(2) {
  margin-right: 0.2rem;
  color: orange;
}

.cont-bot {
  padding-top: 0.1rem;
  display: flex;
  justify-content: space-between;
}

.cont-span {
  float: right;
  margin-top: 0.15rem;
  margin-right: 0.1rem;
  border: 0.01rem solid orange;
  color: orange;
  height: 0.2rem;
  line-height: 0.2rem;
}
</style>
