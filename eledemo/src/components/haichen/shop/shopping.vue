<template>
  <div class="shopping">
    <!-- 
          购物车
    -->
    <div class="car">
      <div class="car_bottom">
        <div
          @click="show_car"
          id="patterning"
          ref="show_car"
          :class="isActive? 'animated bounceIn 1000ms':''"
        >
          <span v-if="this.index_car>0" class="globule">{{this.index_car}}</span>
          购物
        </div>
        <section class="fee">
          <p>
            ¥
            <span>{{calculate?calculate.all_money:0.00}}</span>
          </p>
          <p v-if="pei">配送费¥{{pei}}</p>
        </section>
        <section class="gotopay">
          <span v-if="least">还查¥{{least&&calculate.all_money>0?calculate.all_money-least:least}}元</span>
        </section>
        <section
          @click="submit()"
          v-if="(least&&calculate.all_money>least)||calculate.all_money==least"
          class="gotopay2"
        >
          <span>去结算</span>
        </section>
        <div v-if="Object.keys(foods).length>0&&car" class="caozuo">
          <header>
            <div>购物车</div>
            <div @click.stop="clear()">清空</div>
          </header>
          <ul :key="number" v-for="(val,number) in foods">
            <li v-if="val&&val.number>0">
              <section>
                <h3>{{val.specfoods.name}}</h3>
                <h6>{{val.specfoods.specs_name}}</h6>
              </section>
              <section>
                <p>¥{{val.specfoods.price}}</p>
                <div>
                  <div @click="decrease(val.specfoods.food_id,val.specfoods)" class="decreas22">-</div>
                  <div class="number22">{{val.number}}</div>
                  <div @click="add1(val.specfoods.food_id,val.specfoods)" class="add22">+</div>
                </div>
              </section>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <!-- 
           购物车
    -->
    <div ref="left" v-if="restaurant_data" class="left">
      <ul>
        <li
          ref="left_son"
          :class="title_index==index?'title_name':''"
          @click.stop="scroll(index)"
          :key="index"
          v-for="(value,index) in restaurant_data"
        >
          <img v-if="value.icon_url" :src="'https://fuss10.elemecdn.com/'+value.icon_url+'.jpeg'">
          <span>{{value.name}}</span>
        </li>
      </ul>
    </div>
    <div ref="right" v-if="restaurant_data" class="right">
      <ul @mousewheel="imgScroll">
        <li ref="right_son" :key="index" v-for="(value,index) in restaurant_data">
          <header>
            <section>
              <strong>{{value.name}}</strong>
              <span>{{value.description}}</span>
              <span @click.stop="apprear_prompt">...</span>
              <div v-if="aprear_prompt1" class="alter">{{value.description}}</div>
            </section>
          </header>
          <section class="content" :key="index2" v-for="(value2,index2) in value.foods">
            <div class="menu_link">
              <section>
                <img :src="'https://elm.cangdu.org/img/'+value2.image_path">
              </section>
              <section>
                <h3>{{value2.name}}</h3>
                <p>{{value2.description}}</p>
                <p>{{value2.tips}}</p>
                <p></p>
              </section>
            </div>
            <footer>
              <section>
                <span>¥</span>
                <span>{{value2.specfoods[0].price}}</span>
              </section>
              <section class="one" v-if="value2.specfoods.length==1">
                <div
                  v-if="foods[value2.specfoods[0].food_id]&&foods[value2.specfoods[0].food_id].number>0"
                  class="one_child1"
                  @click="decrease(value2.specfoods[0].food_id,value2.specfoods)"
                >-</div>
                <div
                  v-if="foods[value2.specfoods[0].food_id]&&foods[value2.specfoods[0].food_id].number>0"
                >{{foods[value2.specfoods[0].food_id].number}}</div>
                <div
                  class="one_child3"
                  @click="add($event, value2.specfoods[0].food_id,value2.specfoods)"
                >+</div>
              </section>
              <section class="lot" v-if="value2.specfoods.length>1">
                <div
                  v-if="specfoods[value2.name]&&specfoods[value2.name].number>0"
                  class="decrease2"
                >-</div>
                <div>{{specfoods[value2.name]&&specfoods[value2.name].number>0?specfoods[value2.name].number:''}}</div>
                <div class="seleted" @click="select(1,value2.specfoods)">选规格</div>
                <!-- 
      规格
                -->
                <div v-if="specs_list&&specs_list[0].name==value2.name" class="specs_list">
                  <header>
                    <span></span>
                    <h3>{{value2.name}}</h3>
                    <i @click.stop="disappear(1)" class="el-icon-close"></i>
                  </header>
                  <section>
                    <h4>规格</h4>
                    <ul>
                      <li
                        :class="number==index3?'check':'no_check'"
                        v-on:click="select_spect(index3)"
                        :key="index3"
                        v-for="(value3,index3) in value2.specfoods"
                      >{{value3.specs_name}}</li>
                    </ul>
                  </section>
                  <footer v-if="specs_list">
                    <div>
                      <span>¥</span>
                      <span v-if="specs_list[number]">{{specs_list[number].price}}</span>
                    </div>
                    <div :disabled="specs_list[number]" @click="join()">加入购物车</div>
                  </footer>
                </div>

                <!-- 
                  规格
                -->
              </section>
            </footer>
          </section>
        </li>
      </ul>
    </div>
    <div ref="ball" class="ball"></div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      restaurant_id: null,
      restaurant_data: null,
      specs_list: null,
      number: 0,
      foods: {},
      specfoods: {},
      index_car: 0,
      calculate: {
        all_money: 0
      },
      pei: null,
      least: null,
      aprear_prompt1: false,
      car: false,
      title_index: 0,
      isActive: false
    };
  },
  methods: {
    imgScroll() {
      let clientHeight = this.$refs.left.clientHeight;
      let ping =
        (this.$refs.left.scrollHeight - clientHeight + 50) /
        (this.$refs.left_son.length - 1);

      for (let t = 0; t < this.$refs.right_son.length; t++) {
        if (t != 0) {
          if (
            this.$refs.right_son[t - 1].offsetTop - 164 <
              this.$refs.right.scrollTop &&
            this.$refs.right.scrollTop < this.$refs.right_son[t].offsetTop - 164
          ) {
            this.$refs.left.scrollTop = ping * (t - 1);
            // console.log(this.$refs.left.scrollTop,'truescroll')
            this.title_index = t - 1;
            // break
          }
        } else {
        }
      }
    },
    scroll(index) {
      this.$refs.right.scrollTop =
        this.$refs.right.children[0].children[index].offsetTop - 192;
      this.title_index = index;
    },
    show_car() {
      this.car = !this.car;
    },
    clear() {
      this.specs_list = null;

      this.number = 0;

      this.foods = {};

      this.specfoods = {};

      this.index_car = 0;

      this.calculate = {
        all_money: 0
      };
    },
    apprear_prompt() {
      console.log("是");
      this.aprear_prompt1 = true;
      let _this = this;
      setTimeout(function() {
        _this.aprear_prompt1 = false;
      }, 1000);
    },
    foodDetail() {
      this.$router.push({
        name: "foodDetail",
        params: {}
      });
    },
    submit() {
      this.$store.state.cart_datas = localStorage.setItem(
        "car_data",
        JSON.stringify(this.foods)
      );
      this.$router.push({
        name: "confirmOrder",
        params: {
          foods: this.foods
        }
      });
    },
    calculate_money() {
      let all_money = 0;
      if (Object.keys(this.foods).length > 0) {
        for (let key in this.foods) {
          all_money =
            this.foods[key].number * this.foods[key].specfoods.price +
            all_money;
        }
      }
      this.calculate.all_money = all_money;
    },
    join() {
      if (
        this.specs_list[this.number] &&
        this.specs_list[this.number].food_id &&
        this.specs_list[this.number].name
      ) {
        let id = this.specs_list[this.number].food_id;
        let name = this.specs_list[this.number].name;
        this.index_car++;
        if (!this.foods[id]) {
          this.$set(this.foods, id, {
            number: 1,
            specfoods: this.specs_list[this.number]
          });
          this.calculate_money();
        } else {
          this.foods[id].number++;

          this.calculate_money();
        }
        this.specs_list = null;
        this.number = 0;
        if (this.specfoods[name]) {
          this.specfoods[name].number++;

          this.calculate_money();
        } else {
          this.$set(this.specfoods, name, {
            number: 1
          });

          this.calculate_money();
        }
      }
    },
    decrease(id, specfoods) {
      if (this.foods[id].number == 0) {
        delete this.foods[id];
      } else {
        this.foods[id].number--;
        if (this.index_car == 0) {
          this.index_car == 0;
        } else {
          this.index_car--;
        }
      }
      if (this.specfoods[specfoods.name]) {
        if (this.specfoods[specfoods.name].number == 0) {
          delete this.specfoods[specfoods.name];
        } else {
          this.specfoods[specfoods.name].number--;
        }
      }
      this.calculate_money();
    },
    add(event, id, specfoods) {
      let ball = this.$refs.ball.cloneNode(true);

      let height = document.documentElement.clientHeight;
      let y = event.pageY;

      let newHeight = height - y;
      ball.style.left = event.pageX - 30 + "px";
      ball.style.bottom = newHeight + "px";
      ball.style.transition =
        "left .5s cubic-bezier(1,1,1,1),bottom  .5s cubic-bezier(.99,0,1,.83)";
      event.target.appendChild(ball);
      // ball.style
      setTimeout(() => {
        ball.style.left = "40px";
        ball.style.bottom = "40px";
      }, 10);
      ball.style.display = "block";
      ball.addEventListener("transitionend", () => {
        ball.remove();
        this.isActive = true;
        this.$refs.show_car.addEventListener("animationend", () => {
          this.isActive = false;
        });
      });
      this.add1(id, specfoods);
    },
    select_spect(index) {
      this.number = index;
    },
    select(id, specfoods) {
      console.log(specfoods, id);
      if (!this.specs_list) {
        this.specs_list = specfoods;
      }
      console.log(this.specs_list, "搜索");

      this.$emit("call_back");
    },
    add1(id, specfoods) {
      this.index_car++;
      let specfoods1 = specfoods[0];
      if (!this.foods[id]) {
        this.$set(this.foods, id, {
          number: 1,
          specfoods: specfoods1
        });
      } else {
        this.foods[id].number++;
      }
      if (this.specfoods[specfoods.name]) {
        this.specfoods[specfoods.name].number++;
      }
      this.calculate_money();
    },

    disappear(id) {
      this.specs_list = null;
      this.index = 0;
      this.number2 = 0;
      this.$forceUpdate();
      this.$emit("call_back");
    }
  },
  beforeDestroy() {
    if (this.$store.state.foods[this.restaurant_id]) {
      this.$store.state.foods[this.restaurant_id] = {
        restaurant_id: this.restaurant_id,
        restaurant_data: this.restaurant_data,
        specs_list: this.specs_list,
        number: this.number,
        foods: this.foods,
        specfoods: this.specfoods,
        index_car: this.index_car,
        calculate: this.calculate,
        pei: this.pei,
        least: this.least,
        aprear_prompt1: this.aprear_prompt1,
        car: this.car,
        title_index: this.title_index
      };
    } else {
      this.$store.state.foods[this.restaurant_id] = {
        restaurant_id: this.restaurant_id,
        restaurant_data: this.restaurant_data,
        specs_list: this.specs_list,
        number: this.number,
        foods: this.foods,
        specfoods: this.specfoods,
        index_car: this.index_car,
        calculate: this.calculate,
        pei: this.pei,
        least: this.least,
        aprear_prompt1: this.aprear_prompt1,
        car: this.car,
        title_index: this.title_index
      };
    }
  },
  created() {
    if (this.$store.state.foods[this.$route.query.id]) {
      this.restaurant_id = this.$store.state.foods[
        this.$route.query.id
      ].restaurant_id;
      this.restaurant_data = this.$store.state.foods[
        this.$route.query.id
      ].restaurant_data;
      this.specs_list = this.$store.state.foods[
        this.$route.query.id
      ].specs_list;
      this.number = this.$store.state.foods[this.$route.query.id].number;
      this.foods = this.$store.state.foods[this.$route.query.id].foods;
      this.specfoods = this.$store.state.foods[this.$route.query.id].specfoods;
      this.index_car = this.$store.state.foods[this.$route.query.id].index_car;
      this.calculate = this.$store.state.foods[this.$route.query.id].calculate;
      this.pei = this.$store.state.foods[this.$route.query.id].pei;
      this.least = this.$store.state.foods[this.$route.query.id].least;
      this.aprear_prompt1 = this.$store.state.foods[
        this.$route.query.id
      ].aprear_prompt1;
      this.car = this.$store.state.foods[this.$route.query.id].car;
      this.title_index = this.$store.state.foods[
        this.$route.query.id
      ].title_index;
      console.log(this.index_car, this.$store.state.foods);
    } else {
      this.restaurant_id = this.$route.query.id;
      this.$store.state.restaurant_id = this.$route.query.id;
      this.$http({
        method: "get",
        url:
          "  https://elm.cangdu.org/shopping/v2/menu?restaurant_id=" +
          this.restaurant_id
      }).then(res => {
        this.restaurant_data = res.data;
      });

      this.$http({
        url:
          " https://elm.cangdu.org/shopping/restaurant/" + this.restaurant_id,
        method: "get"
      }).then(res => {
        this.pei = res.data.float_delivery_fee;
        this.least = res.data.float_minimum_order_amount;
      });
    }
  },
  mounted() {}
};
</script>
<style scoped>
.ball {
  width: 0.2rem;
  height: 0.2rem;
  background: #3190e8;
  border-radius: 50%;
  position: fixed;
  bottom: 0.3rem;
  left: 0.4rem;
  z-index: 1111;
  display: none;
}
.title_name {
  background: white;
  box-sizing: border-box;
  border-left: 0.03rem solid blue;
}
.caozuo li > section:nth-child(1) {
  flex: 4;
  overflow: hidden;
}
.caozuo li > section:nth-child(2) > p {
  display: flex;
  align-items: center;
  color: orange;
  font-size: 0.18rem;
  font-weight: 700;
  margin-right: 0.3rem;
}
.add22 {
  width: 0.2rem;
  height: 0.2rem;
  box-sizing: border-box;
  border: 2px solid #3190e8;
  color: white;
  border-radius: 50%;
  background: #3190e8;
  display: flex;
  justify-content: center;
  align-content: center;
}
.number22 {
  display: flex;
  justify-content: center;
  align-content: center;
  margin-right: 0.1rem;
}
.decreas22 {
  width: 0.2rem;
  height: 0.2rem;
  box-sizing: border-box;
  border: 2px solid #3190e8;
  color: #3190e8;
  border-radius: 50%;
  background: white;
  display: flex;
  justify-content: center;
  align-content: center;
  margin-right: 0.1rem;
}
.caozuo li > section:nth-child(2) > div {
  display: flex;
  align-items: center;
}
.caozuo li > section:nth-child(2) {
  flex: 3;
  overflow: hidden;
  display: flex;
  justify-content: space-between;
}
.caozuo li {
  width: 100%;
  height: 0.7rem;
  background: white;
  box-sizing: border-box;
  padding: 0.1rem 0.1rem;
  display: flex;
}
.caozuo > header {
  width: 100%;
  height: 0.4rem;
  background: #eceff1;
  box-sizing: border-box;
  padding: 0.1rem 0.1rem;
  display: flex;
  justify-content: space-between;
}
.caozuo {
  position: absolute;
  bottom: 0.5rem;
  left: 0;
  right: 0;
}
.check {
  border: 1px solid #3190e8;
  color: #3190e8;
}
.specs_list > footer > div:nth-child(2) {
  color: white;
  background: #3190e8;
  box-sizing: border-box;
  padding: 0.05rem 0.05rem;
  border-radius: 0.02rem;
  font-size: 0.12rem;
  font-weight: 1000;
}
.specs_list > footer > div:nth-child(1) {
  color: orange;
  font-size: 0.16rem;
  font-weight: 1000;
}
.specs_list > footer {
  padding: 0.2rem 0.2rem;
  box-sizing: border-box;
  background: #f9f9f9;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.specs_list > section > ul > li {
  box-sizing: border-box;
  padding: 0.06rem 0.08rem;
  font-size: 0.15rem;
  margin-right: 0.1rem;
  border: 1px solid #ddd;
}
.specs_list > section > ul {
  display: flex;
  flex-wrap: wrap;
  box-sizing: border-box;
  padding: 0.05rem 0 0.05rem 0;
}
.specs_list > section {
  box-sizing: border-box;
  padding: 0.1rem 0.2rem;
  margin-bottom: 0.2rem;
}
.el-icon-close {
  margin-top: 0.06rem;
  transform: scale(2);
}
.specs_list > header {
  box-sizing: border-box;
  padding: 0.1rem 0.2rem;
  display: flex;
  font-size: 0.15rem;
  justify-content: space-between;
}
.specs_list {
  width: 3rem;
  position: fixed;
  top: 50%;
  left: 50%;
  z-index: 500;
  margin-left: -1.5rem;
  margin-top: -1rem;
  background: white;
}
.gotopay2 {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 1.2rem;
  background: #4cd964;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.15rem;
  color: white;
}
.gotopay {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 1.2rem;
  background: #535356;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.15rem;
  color: white;
}
.fee > p:nth-child(1) {
  font-size: 0.2rem;
  font-weight: 800;
}
.fee {
  color: white;
  position: absolute;
  bottom: 0.03rem;
  left: 1rem;
}
.globule {
  background: red;
  width: 0.2rem;
  height: 0.2rem;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  left: 0.4rem;
  bottom: 0.3rem;
}
#patterning {
  position: absolute;
  left: 0.2rem;
  bottom: 0.1rem;
  width: 0.6rem;
  height: 0.6rem;
  background: #3190e8;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  border: 0.04rem solid #444;
  box-sizing: border-box;
  border-radius: 100%;
  z-index: 800;
}
.car_bottom {
  height: 0.5rem;
}
.car {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  background: #3d3d3f;
  z-index: 200;
}
.left::-webkit-scrollbar {
  width: 0 !important;
}
.right::-webkit-scrollbar {
  width: 0 !important;
}
.lot div:nth-child(2) {
  margin: 0 0.1rem 0 0.1rem;
}
.seleted {
  width: 0.6rem;
  padding: 0.05rem 0.05rem;
  box-sizing: border-box;
  background: #3190e8;
  border-radius: 0.1rem;
  display: flex;
  justify-content: center;
  align-content: center;
  color: white;
  font-size: 0.15rem;
}
.decrease2 {
  font-size: 0.15rem;
  display: flex;
  justify-content: center;
  align-content: center;
  width: 0.2rem;
  height: 0.2rem;
  box-sizing: border-box;
  border: 2px solid gray;
  border-radius: 50%;
  color: gray;
}
.lot {
  display: flex;
  align-items: center;
}
.one div:nth-child(2) {
  margin: 0 0.1rem 0 0.1rem;
}
.one_child3 {
  width: 0.2rem;
  height: 0.2rem;
  box-sizing: border-box;
  border-radius: 50%;
  background: #3190e8;
  display: flex;
  justify-content: center;
  align-content: center;
  color: white;
  font-size: 0.15rem;
}
.one_child1 {
  font-size: 0.15rem;
  display: flex;
  justify-content: center;
  align-content: center;
  width: 0.2rem;
  height: 0.2rem;
  box-sizing: border-box;
  border-radius: 50%;
  background: #3190e8;
  color: white;
}
.one {
  display: flex;
}
footer > section:nth-child(1) {
  color: orange;
  font-size: 0.16rem;
}
footer {
  padding: 0.1rem 0 0.1rem 0.6rem;
  box-sizing: border-box;
  display: flex;
  justify-content: space-between;
}
.menu_link > section:nth-child(2) h3 {
  font-weight: 1000;
  font-size: 0.2rem;
}
.menu_link > section:nth-child(2) {
  flex: 1;
  overflow: hidden;
}
.menu_link > section:nth-child(1) {
  width: 20%;
  margin-right: 0.1rem;
}
.menu_link > section:nth-child(1) > img {
  width: 100%;
}
.menu_link {
  width: 100%;
  display: flex;
}
.content {
  padding: 0.1rem 0.1rem;
  box-sizing: border-box;
  background: white;
  border-bottom: 0.01rem solid #ededed;
}
.alter {
  width: 40%;
  height: 0.4rem;
  line-height: 0.4rem;
  border-radius: 0.1rem;
  text-align: center;
  z-index: 1;
  position: absolute;
  right: 0;
  color: white;
  background: rgba(0, 0, 0, 0.8);
}
.right header section > span:nth-child(3) {
  float: right;
}
.right header section strong {
  width: 100%;
  font-size: 0.2rem;
  margin-right: 0.1rem;
  font-weight: 1000;
}
.right header {
  padding: 0.1rem 0.1rem;
  box-sizing: border-box;
  position: relative;
}
.left li {
  width: 0.9rem;
  height: 0.3rem;
  box-sizing: border-box;
  padding: 0.25rem 0.08rem;
  border-bottom: 0.01rem solid #ededed;
  overflow: hidden;
  overflow: hidden; /*自动隐藏文字*/
  text-overflow: ellipsis; /*文字隐藏后添加省略号*/
  white-space: nowrap; /*强制不换行*/
}
.left img {
  width: 20%;
  margin-right: 0.05rem;
}
.left span {
  font-weight: 1000;
}
.left {
  overflow-x: hidden;
  overflow-y: scroll;
}
.right {
  flex: 1;
  overflow-x: hidden;
  overflow-y: scroll;
}

.shopping {
  flex: 1;
  display: flex;
  box-sizing: border-box;
  padding-bottom: 0.5rem;
}
</style>
