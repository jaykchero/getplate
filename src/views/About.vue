<template>
  <div class="home">


    <img alt="Vue logo" src="../assets/logo.png">
    <van-row gutter="20">
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <van-cell v-for="item in list" :key="item" :title="item" />
      </van-list>
    </van-row>
  </div>
</template>

<script>
  import axios from 'axios'


  export default {
    data() {
      return {
        list: [],
        loading: false,
        finished: false,
        active: 2,
      };
    },
    mounted () {
      this.loading=true;
      axios.get("http://localhost:10000/plate/list").then((res)=>{
        this.list=res.data.data.treeMap;
        this.loading=false;

      })
    },

    methods: {

    },
  };

</script>
