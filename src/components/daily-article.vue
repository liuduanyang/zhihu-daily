<template>
  <div>
    <div class="daily-article-title">{{data.title}}</div>
    <div class="daily-article-content" v-html="data.body"></div>
  </div>
</template>

<script>
  import $ from '../libs/util'
  export default {
    props:['id'],
    data(){
      return{
        data:{}  // 为null 则会报错
      }
    },
    methods:{
      getArticle(){
        $.ajax.get('news/'+this.id).then(res=>{
          // 将文章中图片的地址替换为代理的地址
          res.body=res.body.replace(/src="http/g,`src="${$.imgPath}http`);
          res.body=res.body.replace(/src="https/g,`src="${$.imgPath}https`);
          this.data=res;
          // 返回文章顶部
          window.scrollTo(0,0);
        })
      }
    },
    watch:{
      id(val){
          this.getArticle();
      }
    }
  }
</script>

<style>
.daily-article-title{
  font-size: 28px;
  font-weight: bold;
  color:#222;
  padding:10px 0;
}
</style>
