<template>
  <div>
    <!-- v-pre 跳过vue解析直接显示 -->
    <h3 v-pre>今日热点</h3>
    <div>
      <!-- ！！问题—当启动自动播放时，因轮播导致的高度变化会触发scroll事件且会使列表抖动 -->
      <!-- 修复问题1  不自动播放滚动 -->
      <!-- 问题2 路由切换，再切回来时，轮播图自动切回第一页但没有刷新CurrentIndex -->
      <!-- 修复问题2 监听路由，返回当前页重置SwipeCurrentIndex -->
      <keep-alive>
        <van-swipe :loop="true" :height="height" @change="change">
          <!-- i 记录当前页数 -->
          <van-swipe-item v-for="i in swipeCount" :key="i">
            <van-grid :column-num="3">
              <!-- 使用 i 截取 0-5 的数据 -->
              <van-grid-item
                v-for="item in fire.slice((i - 1) * 6, i * 6)"
                :key="item.count"
              >
                <h4>{{ item.name }}</h4>
                <h6 :class="item.amount > 0 ? 'red' : 'green'">
                  {{ item.current }}
                </h6>
                <h6 :class="item.amount > 0 ? 'red' : 'green'">
                  {{ item.amount }}%
                </h6>
              </van-grid-item>
            </van-grid>
          </van-swipe-item>
        </van-swipe>
      </keep-alive>
    </div>
  </div>
</template>
<script>
import { getFire } from "@/api/index";
// import { log } from "console";

export default {
  data() {
    return {
      fire: [],
      swipeCurrent: 0,
    };
  },
  methods: {
    //记录当前滑块索引值
    change(index) {
      this.swipeCurrent = index;
    },
  },
  computed: {
    //计算滑块数量
    swipeCount() {
      return Math.ceil(this.fire.length / 6);
    },

    // 以滑块内容的行数设置其高度
    height() {
      if (
        this.fire.length % 6 < 4 &&
        this.swipeCurrent == this.swipeCount - 1
      ) {
        return 343 / 4;
      } else {
        return 343 / 2;
      }
    },
  },

  watch: {
    $route(to) {
      if (to.name == "home") {
        this.swipeCurrent = 0;
      }
    },
  },
  mounted() {
    // getStock().then((res) => console.log(res));
    //请求文章数据
    getFire().then((res) => {
      this.fire = res.data.list;
    });
  },
};
</script>
<style lang="less" scoped>
//证券指数
div {
  h3 {
    //今日热点
    text-align: center;
    color: rgb(248, 110, 110);
    background-color: #f9f9fb;
    margin: 0;
    padding: 1rem;
  }
  h4 {
    //股票名称
    margin: 0;
    margin-bottom: 0.3rem;
  }
  h6 {
    margin: 0;
  }
  .red {
    color: red;
  }
  .green {
    color: rgb(72, 166, 72);
  }
}

//滑块指示器大小
// .van-swipe__indicator {
//   width: 0.6rem;
//   height: 0.1rem;
// }
</style>
