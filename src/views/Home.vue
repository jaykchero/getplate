<template>
  <div class="home">
    <van-count-down  @finish="finish" ref="countDown" millisecond :time="time" format="HH:mm:ss:SS" />
    <van-row>

    <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <van-cell @click="onclick1(item)"  v-for="item in list" :key="item" :title="item" />
      </van-list>
    </van-pull-refresh>
    </van-row>
  </div>
</template>

<script>
  import axios from 'axios'
  import { Dialog } from 'vant';
  import { Toast } from 'vant';


  export default {

    components: {
      [Dialog.Component.name]: Dialog.Component,
    },

    data() {
      return {
        list: [],
        time: 1 * 30 * 1000,
        finished: false,
        loading: false,
        refreshing: false,
      };
    },
    created () {
      this.loading=true;
      axios.get("http://localhost:10000/plate/list").then((res)=>{
        this.list=res.data.data.treeMap;
        this.loading=false;
      })
    },


    methods: {

      onLoad() {
        setTimeout(() => {
          if (this.refreshing) {
            this.list = [];
            this.refreshing = false;
          }

          this.loading=true;
          this.$refs.countDown.reset();
          axios.get("http://localhost:10000/plate/list").then((res)=>{
            this.list=res.data.data.treeMap;
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
        Toast('倒计时结束');
      },

      onclick1(event){
        Dialog.confirm({
          title:'确认选择车牌?',
          message:event,
        })
          .then(() => {
            // on confirm
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
  /*.van-list{*/
  /*  display: flex;*/
  /*  justify-content: space-between;*/
  /*}*/

</style>
