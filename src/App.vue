<template>
  <div id="app">
    <div class="daily">
      <div class="daily-menu">
        <div class="daily-menu-item" :class="{on:type==='recommend'}" @click="handleToRecommend()">每日推荐</div>
        <div class="daily-menu-item" :class="{on:type==='daily'}" @click="showThemes=!showThemes">主题日报</div>
        <ul v-show="showThemes">
          <li v-for="item of themes">
            <a :class="{on:item.id===themeId&&type==='daily'}" @click="handleTotheme(item.id)">
              {{item.name}}
            </a>
          </li>
        </ul>
      </div>

      <div class="daily-list">
        <template v-if="type==='recommend'">
          <!-- 因为Item是组件，绑定原生事件时要带事件修饰符 .native 否则会认为监听的是来自Item组件的自定义事件click -->
          <Item v-for="item of recommentList" :data="item" :key="item.id" :id="articleId" @click.native="handleClick(item.id)"></Item>
        </template>
        <template v-if="type==='daily'">
          <Item v-for="item of list" :data="item" :key="item.id" :id="articleId" @click.native="handleClick(item.id)"></Item>
        </template>
      </div>

      <daily-article :id="articleId" class="daily-article"></daily-article>
    </div>
  </div>
</template>

<script>
import $ from './libs/util';
import Item from './components/item'
import dailyArticle from './components/daily-article'
export default {
  components:{
    Item,
    dailyArticle
  },
  data(){
    return {
      themes:[],
      showThemes:false,
      type:'recommend',
      list:[],
      themeId:0,
      recommentList:[],
      articleId:0
    }
  },
  methods:{
    getThemes(){
      $.ajax.get('themes').then(res=>{
        this.themes=res.others;
      })
    },
    handleTotheme(id){
      this.type='daily';
      this.themeId=id;
      this.list=[];
      $.ajax.get(`theme/${id}`).then(res=>{
        this.list=res.stories;
      })
    },
    handleToRecommend(){
      this.type='recommend';
      this.recommentList=[];
      this.getToRecommend();
    },
    getToRecommend(){
      $.ajax.get('news/latest').then(res=>{
        res.stories.forEach(item=>{
          this.recommentList.push(item);
        });
      });
    },
    handleClick(id){
      this.articleId=id;
    }
},
  mounted(){
    this.getThemes();
    this.getToRecommend();
  }
}
</script>

<style>
  html,body{
    margin:0;
    padding:0;
    height:100%;
    color:#657180;
    font-size: 16px;
  }
  .daily-menu{
    width:150px;
    position: fixed;
    top:0;
    left:0;
    bottom:0;
    overflow: hidden;
    background-color: #f5f7f9;
  }
  .daily-menu-item{
    font-size: 18px;
    text-align:center;
    margin:5px 0;
    padding:10px 0;
    cursor: pointer;
    border-right: 2px solid transparent;
    transition: all .3s ease-in-out;
  }
  .daily-menu-item:hover{
    background-color: #e3e8ee;
  }
  .daily-menu-item.on{
    border-right: 2px solid #3399ff;    
  }
  .daily-list{
    width:300px;
    position: fixed;
    top:0;
    bottom:0;
    left:150px;
    overflow: auto;
    border-right:1px solid #d7dde4;
  }
  .daily-item{
    display: block;
    color:inherit;
    text-decoration: none;
    padding:16px;
    overflow: hidden;
    cursor: pointer;
    transition:all .3s ease-in-out;
  }
  .daily-item:hover{
    background-color: #e3e8ee;
  }
  .daily-article{
    margin-left: 450px;
    padding:20px;
  }
  .daily-menu ul{
    list-style:none;
  }
  .daily-menu ul li a{
    display:block;
    color:inherit;
    text-decoration: none;
    padding:5px 0;
    margin:5px 0;
    cursor: pointer;
  }
  .daily-menu ul li a:hover,.daily-menu ul li a.on{
    color:#3399ff;
  }
</style>
