<template>
    <div class="index" ref="indexContent">
        <div class="banner swiper-container" >
            <ul class="banner_list swiper-wrapper">
                <li class="swiper-slide">
                    <img src="../../static/img/banner_1.jpg" alt="">
                </li>
                <li class="swiper-slide">
                    <img src="../../static/img/banner_2.jpg" alt="">
                </li>
            </ul>
            <div class="swiper-pagination"></div>
        </div>
        <div class="nav_wrap">
            <div class="nav_content">
                <ul class="nav_list">
                    <li :class="{active:this_Index == index}" 
                        v-for="key,index in navList" 
                        @click="changeNav(index,key)" :key="index">
                        {{key}}
                    </li>
                </ul>
            </div>
        </div>
        <ul class="index_movie_list">
            <router-link tag="li" :to="{path:'/movieDetails',query:{id:item.id}}" 
            v-for="item,index in message.addList" :key="index">
                <div class="movie_images">
                    <img :src="getImages(item.images.small)"/>
                </div>
                <div class="movie_message">
                    <h2 class="movie_name">{{item.title}}</h2>
                    <div class="type">
                        <span v-for="genre in item.genres">{{genre}}</span>
                    </div>
                </div> 
                <div class="grade">
                    {{item.rating.average | filterGrade}}
                </div>
            </router-link>
        </ul>
    </div>
</template>
<script>
    //swiper插件
    import Swiper from 'swiper'
    import 'swiper/dist/css/swiper.css';
    export default {
        name:'index',
        data() {
            return {
                this_Index: 0 ,
                navList:['动作','喜剧','剧情','爱情','科幻','动画','悬疑','惊悚','恐怖','犯罪','同性','音乐','歌舞','传记','历史','战争','西部','奇幻','冒险','灾难','武侠','情色'],
                activeMovieType:'动作',
                activeMovieStart:0,
                cacheList:[],
                message:{}
            }
        },
        watch: {
            activeMovieType(newValue,old){
                this.message = {
                    MovieStart:this.activeMovieStart,
                    url: `/search?tag=${newValue}`,
                    addList:this.message.addList,
                    localName:'indexData'
                }
                this.scroll(this.message)
            }
        },
        mounted() {
            this.message = {
                MovieStart:this.activeMovieStart,
                url: `/search?tag=${this.activeMovieType}`,
                addList:this.message.addList,
                localName:'indexData'
            }
            let data = this.localGet('indexData')
            if (data == false) {
                this.typeApi()
            }else{
                this.message.addList = data
            }
            //Swiper实列
            new Swiper('.swiper-container',{
                autoplay:{
                    disableOnInteraction:false
                },
                loop:true,
                pagination: {
                    el: '.swiper-pagination',
                },
            })
            this.scroll(this.message)
        },
        updated () {

        },
        beforeDestroy() {
           
        },
        methods:{
            changeNav(index,key){
                this.this_Index = index
                this.activeMovieType = key
                this.typeApi()
            },
            getImages( _url ){  //请求图片的地址进行缓存
                if( _url !== undefined ){
                    let _u = _url.substring( 7 );
                    return 'https://images.weserv.nl/?url=' + _u;
                }
            },
            typeApi(){
                let url = `/search?tag=${this.activeMovieType}&start=${this.activeMovieStart}&count=10`
                this.Api(url).then(data => {
                    this.message.addList = data.subjects
                    this.localSet('indexData',this.message.addList)
                }).catch( err => {
                    
                })
            },
        },
    }
</script>
<style scoped>
    .banner_list li{
        height: 3rem;
        background-color: #ccc;
    }
    .nav_wrap{
        position: relative;
        width: 6.9rem;
        height: 1rem;
        margin: 0 auto;
    }
    .nav_content{
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        overflow: hidden;
        height: 1rem;
    }
    .nav_list{
        overflow: hidden;
        overflow-x: auto;
        display: flex;
        height: 100%;
        align-items: center;
    }
    .nav_list li{
        color: #999;
        flex: 0 0 22%;
        text-align: center;
        line-height: 1rem;
        font-size: .28rem;
    }
    .nav_list li.active{
        position: relative;
        color: #259faf;
    }
    .nav_list li.active::after{
        content: '';
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        height: .08rem;
        background-color: #259faf;
    }
    .index_movie_list{
        padding-left: .3rem;
        padding-right: .07px;
        padding-bottom: .98rem;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
    }
    .index_movie_list li{
        position: relative;
        width: 3.33rem;
        box-shadow: 0px 0px 5px rgba(63, 114, 129, 0.5);
        border-radius: 4px;
        margin-right: .23rem;
        margin-top: .23rem;
        overflow: hidden;
    }
    .index_movie_list li .grade{
        position: absolute;
        top: 0;
        right: 0;
        width: .6rem;
        height: .5rem;
        background-color: rgba(63, 114, 129, 0.9);
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: .3rem;
        font-weight: 200;
        color: #FFF;
        border-bottom-right-radius: 4px;
    }
    .movie_images{
        width: 3.33rem;
        height: 3.5rem;
    }
    .movie_images img{
        width: 100%;
        height: 100%;
    }
    .movie_name{
        font-weight: normal;
        font-size: .28rem;
        color: #259faf;
        margin-bottom: .15rem;
    }
    .type{
        line-height: 100%;
    }
    .movie_message{
        padding: .2rem;
    }
    .movie_message .type{
        display: flex;
        align-items: center;
        flex-wrap: wrap;
    }
    .movie_message span{
        font-size: .24rem;
        color: #999;
        border: 1px solid #259faf;
        padding:3px 8px;
        margin-right: 5px;
        border-radius: 20px;
    }
    
</style>
<style>
    .swiper-pagination .swiper-pagination-bullet{
        position: relative;
        background:rgba(63, 114, 129, 0.6);
        border:1px solid #FFF;
        opacity: 1;
    }
    .swiper-pagination .swiper-pagination-bullet-active{
        border-radius: 6px;
        width:.5rem;
        animation:myfirst .3s;
        -moz-animation:myfirst .3s; /* Firefox */
        -webkit-animation:myfirst .3s; /* Safari and Chrome */
        -o-animation:myfirst .3s; /* Opera */
    }
    .swiper-pagination .swiper-pagination-bullet-active:after{
        position: absolute;
        content:'';
        background:#FFF;
        left: .05rem;
        right: .05rem;
        top:1px;
        height: 6px;
        border-radius: 6px;
    }
    @keyframes myfirst
    {
        0%   {width:.1rem;opacity: .2;}
        25%  {width:.2rem;opacity: .4;}
        50%  {width:.3rem;opacity: .6;}
        75%  {width:.4rem;opacity: .8;}
        100% {width:.5rem;opacity: 1;}
    }
    @-moz-keyframes myfirst
    {
        0%   {width:.1rem;opacity: .2;}
        25%  {width:.2rem;opacity: .4;}
        50%  {width:.3rem;opacity: .6;}
        75%  {width:.4rem;opacity: .8;}
        100% {width:.5rem;opacity: 1;}
    }
    @-webkit-keyframes myfirst
    {
        0%   {width:.1rem;opacity: .2;}
        25%  {width:.2rem;opacity: .4;}
        50%  {width:.3rem;opacity: .6;}
        75%  {width:.4rem;opacity: .8;}
        100% {width:.5rem;opacity: 1;}
    }
    @-o-keyframes myfirst
    {
        0%   {width:.1rem;opacity: .2;}
        25%  {width:.2rem;opacity: .4;}
        50%  {width:.3rem;opacity: .6;}
        75%  {width:.4rem;opacity: .8;}
        100% {width:.5rem;opacity: 1;}
    }
</style>

