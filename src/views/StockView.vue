<template>
  <div>
    <div class="top"></div>
    <van-tabs v-model="active" :swipeable="true" animated>
      <van-tab v-for="(title, index) in titles" :title="title" :key="title">
        <van-list v-for="(stock, i) in stocks[index]" :key="stock.code">
          <van-cell :title="`${i + 1} ${stock.name}`">
            <Icon
              icon="material-symbols:arrow-downward"
              :color="stock.chg > 0 ? 'red' : 'green'"
              :rotate="stock.chg > 0 ? '2' : '0'"
            />

            <span> {{ stock.value }}</span>
          </van-cell>
        </van-list>
      </van-tab>
    </van-tabs>
  </div>
</template>
<script>
// import { constants } from "buffer";
import { Icon } from "@iconify/vue2";
import { getStockList } from "../api/index";
export default {
  data() {
    return {
      active: 0,
      titles: ["全球", "沪深", "港股", "美股"],
      stocks: [],
      // type: { 0: 20, 1: 22, 2: 23, 4: 21 },
    };
  },
  components: { Icon },
  created() {
    //获取股票列表
    assignStocks(this);

    async function assignStocks(_this) {
      for (let index = 0; index < 4; index++) {
        await getStockList(index).then(({ data }) => {
          _this.stocks.push([...data.items]);
        });
      }
    }
  },
};
// computed:{
//   active(){

//   }
// }
</script>
<style lang="less" scoped>
.van-cell {
  font-size: 15px;
  padding: 10px 30px;
  // border: 0.2px solid black;
}
</style>
