<template>
    <div class="left-nav">
    <ul>
        <li>
        <i class="icon iconfont icon-goumai"></i>
        <div>收银</div>
        </li>

         <li>
        <i class="icon iconfont icon-dianpu"></i>
        <div>店铺</div>
        </li>

         <li>
        <i class="icon iconfont icon-hanbao"></i>
        <div>商品</div>
        </li>

         <li>
        <i class="icon iconfont icon-huiyuanqia"></i>
        <div>会员</div>
        </li>

         <li>
        <i class="icon iconfont icon-zhubaoshipin"></i>
        <div>统计</div>
        </li>
    </ul>
    </div>
</template>
<script>
 export default{
     name:'leftNav'
 }    
</script>
<style >
    .left-nav {
       color:#fff;
       font-size:10px;
       height:100%;
       background-color:#1D8ce0;
       width:5%;
       float:left;
        
    }
    .iconfont{
        font-size:24px;
    }
    .left-nav ul{
        margin: 0px;
        padding: 0px;
    }
    .left-nav ul li{
        list-style: none;
        text-align: center;
        padding: 10px;
        border-bottom: solid 1px #20a0ff;
    }
</style>