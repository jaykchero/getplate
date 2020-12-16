<template>
  <div class="home">
    <van-nav-bar
      title="50选1系统"
      left-text="返回"
      right-text="测试"
      left-arrow
      @click-left="onClickLeft"
      @click-right="onClickRight"
    />

    <!-- 下滑进入 -->
    <transition name="van-slide-down">
      <div style="margin-bottom: 20px;margin-top: 20px" >下拉刷新车牌</div>
    </transition>


    <div style="display: flex;justify-content: space-around">

      <van-popover
        v-model="showPopover"
        theme="dark"
        trigger="click"
        @select="selectTime"
        :actions="actions"
      >
        <template #reference>
          <van-button style="margin-top: 13px;" size="small" type="info">
            选择倒计时
          </van-button>
        </template>
      </van-popover>

      <div style="display: flex;justify-content: center">
        <p style="display: inline">倒计时：</p>
        <van-count-down :auto-start="autoStart" @finish="finish" millisecond ref="countDown" :time="time" format="HH:mm:ss:SS" />
      </div>

    </div>


    <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
        <van-grid
          :finished="finished"
          finished-text="没有更多了"
          @load="onLoad"
          :column-num="3" >
          <van-grid-item @click="onclick1(item)" v-for="item in list" :key="item" icon="photo-o">
            {{item}}
          </van-grid-item>
        </van-grid>
    </van-pull-refresh>
  </div>
</template>

<script>
  import axios from 'axios'
  import { Dialog, Toast } from 'vant'
  import { DropdownMenu, DropdownItem } from 'vant';


  export default {

    components: {
      [Dialog.Component.name]: Dialog.Component,
    },

    data() {
      return {
        showPopover: false,
        actions: [
          { text: '倒计时3分钟',time: 3 * 60 * 1000 },
          { text: '倒计时1分钟',time: 1 * 60 * 1000 },
          { text: '倒计时30秒',time: 30* 1000 }],

        list: [],
        autoStart:true,
        // time: 3 * 60 * 1000,
        time:3 * 60 * 1000,
        loading: false,
        finished: false,
        refreshing: false,
      };
    },
    created () {
      this.loading=true;
      axios.get("http://localhost:10000/plate/list").then((res)=>{
        this.list=res.data.data.treeMap;
        this.$refs.countDown.reset();
        this.loading=false;
        console.log(this.list)
      })
    },


    methods: {

      selectTime(val){
        this.time=val.time;
        console.log(this.time)
        this.$refs.countDown.reset();

      },

      onClickLeft() {
        Toast('返回');
      },
      onClickRight() {
        Toast('按钮');
      },

      onLoad() {
        setTimeout(() => {
          if (this.refreshing) {
            this.list = [];
            this.refreshing = false;
          }

          this.loading=true;
          this.$refs.countDown.reset();
          axios.get("http://49.234.74.115:10000/plate/list").then((res)=>{
            this.list=res.data.data.treeMap;
            console.log(this.list)
            this.loading=false;
          })
        }, 1000);
      },


      onRefresh() {
        // 清空列表数据
        this.finished = false;
        Toast('刷新成功');
        // 重新加载数据
        // 将 loading 设置为 true，表示处于加载状态
        this.loading = true;
        this.onLoad();
      },

      finish() {
        let id= Math.ceil(Math.random()*50);
        console.log("id",id)
        console.log()

        Dialog.confirm({
          title:'选择超时，系统已自动为您选择此车牌：',
          message:this.list[id],
        })
          .then(() => {

        })
          .catch(() => {
            // on cancel
          });

      },

      onclick1(event){
        Dialog.confirm({
          title:'确认选择车牌?',
          message:event,
        })
          .then((res) => {
            this.$refs.countDown.reset();
          })
          .catch(() => {
            // on cancel
          });

      },


      // onLoad() {
      //   // 异步更新数据
      //   // setTimeout 仅做示例，真实场景中一般为 ajax 请求
      //   setTimeout(() => {
      //     this.time="",
      //     this.loading=true;
      //     axios.get("http://localhost:10000/plate/list").then((res)=>{
      //       this.list=res.data.data.treeMap;
      //       this.time=1 * 60 * 1000,
      //       this.loading=false;
      //     })
      //   }, 1000*60);
      // },
    },
  };
</script>
<style>
  .van-dialog__message{
    font-size: 20px !important;
    color:#42b983 !important;
    /*background-image: linear-gradient(to right, #ff6034, #3c5c78);*/
  }
  .van-count-down{
    line-height: 55px !important;
  }

  .van-nav-bar{
    background:#42b983!important;


  }

  .van-nav-bar__text,.van-nav-bar__title,.van-nav-bar .van-icon{
    color: snow !important;
  }


  .van-grid-item{
    position: relative;
    -webkit-box-flex: 1;

    -webkit-flex-basis: 33%;
    flex-basis: 33%;
    box-sizing: border-box !important;
    border-radius: 8px;
    padding: 0 6px 6px 0;
    -moz-border-radius:8px; /* 老的 Firefox */
  }

  .van-pull-refresh{
    padding-left: 10px;
    padding-right: 10px;
  }
  .home{
    background: #f7f8fa;
  }

</style>
