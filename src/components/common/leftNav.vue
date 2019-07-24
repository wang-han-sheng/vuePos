<template>
    <div class="left-nav">
        <ul>
            <li class="icon iconfont icon-fufei">
                <div>收银</div>
            </li>
            
            <li class="icon iconfont icon-dianpu">
                <div>店铺</div>
            </li>
            
            <li class="icon iconfont icon-fenlei">
                <div>商品</div>
            </li>
            
            <li class="icon iconfont icon-huiyuan">
                <div>会员</div>
            </li>
           
            <li class="icon iconfont icon-dianzan">
                <div>统计</div>
            </li>
            
        </ul>
    </div>
</template>
<script>
export default {
    name:'leftNav',
    data(){
        return {

        }
    }
}
</script>
<style>
.left-nav{
    color:#fff;
    float: left;
    height: 100%;
    width: 5%;
    background-color: #1d8ce0;
    font-size: 10px;
}
.iconfont{
    font-size: 18px;
}
.left-nav ul{
    margin: 0;
    padding: 0;
}
.left-nav ul li {
   list-style: none;
   text-align: center;
   border-bottom:1px solid #20a0ff;
   padding:10px;
}
</style>


