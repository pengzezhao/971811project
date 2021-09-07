<template>
    <div class="home-container">
      <van-nav-bar id="bar" title="泽朝头条" fixed/>
<!--      <p>{{artlist}}</p>-->
      <van-pull-refresh v-model="isLoading" @refresh="onRefresh" :disabled="finished">
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <ArticleInfo v-for="item in articlelist" :key="item.id"
                     :title="item.title"
                     :author="item.aut_name"
                     :cmt-count="item.comm_count"
                     :time="item.pubdate"
                     :cover="item.cover"
        ></ArticleInfo>
        </van-list>
      </van-pull-refresh>

    </div>
</template>

<script>
import { getArticleAPI } from '../../api/articleAPI'
import ArticleInfo from '../../components/Article/ArticleInfo'
export default {
  name: 'Home',
  components: { ArticleInfo },
  data () {
    return {
      page: 1,
      limit: 10,
      articlelist:[],
      loading:true,
      finished:false,
      isLoading:false

    }
  },
  created () {
    this.initArticleList()
  },
  methods: {
    async initArticleList (isRefresh) {
      const { data: res } = await getArticleAPI(this.page, this.limit)
      console.log(res)
      if (isRefresh){
        this.articlelist=[...res,...this.articlelist]
        this.isLoading=false
      }else{
        this.articlelist=[...this.articlelist,...res]
        this.loading=false

      }
      // this.articlelist=[...this.articlelist,...res]
      // this.loading=false
      if (res.length===0){
        this.finished=true
      }
    },
    onLoad(){
      console.log('触发了事件！')
      this.page++
      this.initArticleList()
    },
    onRefresh(){
      console.log('出发了下拉刷新！')
      this.page++
      this.initArticleList(true)

    }
  }
}
</script>

<style lang="less" scoped>
  .home-container{
    padding:46px 0 50px 0;

    .van-nav-bar{
      background-color: #007bff;
    }
    /deep/ .van-nav-bar__title{
      color: white;
    }
    /*#bar{*/
    /*  color: white;*/
    /*}*/
  }

</style>
