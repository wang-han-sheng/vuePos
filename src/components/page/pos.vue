
<template>
  <div class="pos">
    <el-row class="mainPos">
      <el-col :span="10" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="orderData" style="width:100%" border  stripe>
              <el-table-column label="商品名称" prop="goodsName" width="150"></el-table-column>
              <el-table-column label="数量" prop="count"></el-table-column>
              <el-table-column label="金额" prop="price"></el-table-column>
              <el-table-column label="操作" fixed="right">
                <template slot-scope="scope">
                  <el-button @click='addOrderList(scope.row)' type="text" size="small">增加</el-button>
                  <el-button @click='reduceOrderList(scope.row)' type="text" size="small">减少</el-button>
                  <el-button type="text" size="small" @click='handleDelSingle(scope.row)'>删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="total">
              <span><small>总数：</small>{{totalCount}}</span>
              <span><small>总价：</small>{{totalMoney}}</span>
            </div>
            <div class="order-btn">
              <el-button type="warning" round>挂单</el-button>
              <el-button type="danger" @click='handleDelAll' round>清空全部</el-button>
              <el-button type="success" @click='handleSuccess' round>结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="14">
        <div>
          <div class="title">常用商品</div>
          <div class="ofen-goods">
            <ul>
              <li v-for="item in ofenUseData" :key="item.goodsId" @click='addOrderList(item)'>
                <span>{{item.goodsName}}</span>
                <span class="price">¥{{item.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡" class="type">
                <ul class="cookList">
                  <li v-for="item in cookList1" :key="item.goodsId" @click='addOrderList(item)'>
                    <div class="clear">
                      <div class="foodImg" style="float:left">
                        <img :src ="item.goodsImg" width="100%">
                      </div>
                      <div style="float:left">
                        <div class="foodName">{{item.goodsName}}</div>
                        <div class="foodPrice">¥{{item.price}}</div>
                      </div>
                    </div>
                  </li>
                </ul>
            </el-tab-pane>
            <el-tab-pane label="小食" class="type">
              <ul class="cookList">
                  <li v-for="item in cookList2" :key="item.goodsId" @click='addOrderList(item)'>
                    <div class="clear">
                      <div class="foodImg" style="float:left">
                        <img :src ="item.goodsImg" width="100%">
                      </div>
                      <div style="float:left">
                        <div class="foodName">{{item.goodsName}}</div>
                        <div class="foodPrice">¥{{item.price}}</div>
                      </div>
                    </div>
                  </li>
                </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料" class="type">
              <ul class="cookList">
                  <li v-for="item in cookList3" :key="item.goodsId" @click='addOrderList(item)'>
                    <div class="clear">
                      <div class="foodImg" style="float:left">
                        <img :src ="item.goodsImg" width="100%">
                      </div>
                      <div style="float:left">
                        <div class="foodName">{{item.goodsName}}</div>
                        <div class="foodPrice">¥{{item.price}}</div>
                      </div>
                    </div>
                  </li>
                </ul>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: "pos",
  mounted: function() {
    let clientHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = clientHeight + "px";
  },
  data() {
    return {
      orderData: [],
      ofenUseData: [],
      cookList1: [],
      cookList2: [],
      cookList3: [],
      totalMoney:0,
      totalCount:0,
    };
  },
  created:function(){
    //获取常用商品
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
     .then(response => this.ofenUseData = response.data)
     .catch(error => {this.$message('网络问题，请重试')})
    
    //获取汉堡、小食、饮料 分类信息
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
      .then(response => {
        this.cookList1 = response.data[0];
        this.cookList2 = response.data[1];
        this.cookList3 = response.data[2];
      });
  },
  methods: {
    // 添加商品
    addOrderList(item){
      this.totalMoney = 0;
      this.totalCount = 0
      let isHave = false;
      for(let i=0;i<this.orderData.length;i++){
        if(this.orderData[i].goodsId === item.goodsId){
          isHave = true;
        }; 
      }
      if(isHave){
        var arr = this.orderData.filter(o => o.goodsId === item.goodsId);
        arr[0].count++;
      }else{
        let newGood = {goodsId:item.goodsId,goodsName:item.goodsName,price:item.price,count:1}
        this.orderData.push(newGood)
      }
      this.getTotalOrder();
    },
    // 减少商品
    reduceOrderList(item){
        var arr = this.orderData.filter(o => o.goodsId === item.goodsId);
        arr[0].count--;
        if(arr[0].count<1){
           arr[0].count = 1
          this.$message({
             type:'warning',
            message:'商品数量至少为1'
            })
        }
        this.getTotalOrder()
    },
     //单个删除
    handleDelSingle(item){
      this.orderData = this.orderData.filter(o=>o.goodsId !== item.goodsId)
      this.getTotalOrder();
    },
    //删除全部
    handleDelAll(){
      if(this.orderData.length){
        this.totalMoney = 0;
        this.totalCount = 0;
        this.orderData = [];
      }else{
        this.$message('请先添加商品！')
      }
    },
    //正常情况，此处应调用后台接口，将总价传给后台，方便记录！
    handleSuccess(){
      if(this.orderData.length){
        this.$message('商品总价为'+this.totalMoney+'元,感谢您的光临！')
      }else{
        this.$message('请先添加商品！')
      }
    },
    //获取总价 总数量
    getTotalOrder(){
      this.totalMoney = 0;
      this.totalCount = 0;
      if(this.orderData){
        this.orderData.forEach((element)=>{
          this.totalCount += element.count;
          this.totalMoney += element.count *element.price;
        })
      }else{
        this.$message('请先填入商品！')
      }
    },
    
    
  }
};
</script>
<style scoped>
.order-btn {
  margin-top: 20px;
}
.mainPos {
  margin-left: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.total{
  background-color: #fff;
  border-bottom: 1px solid #d3dce6;
  padding: 10px;
  font-size: 18px;
  text-align: right;
}
.ofen-goods ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
  cursor: pointer;
}
.price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
  margin:10px;
}
.cookList li {
  cursor: pointer;
  list-style: none;
  width: 50%;
  min-width: 260px;
  float: left;
}
.cookList li>div {
  border: 1px solid #e5e9f2;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  margin: 2px;
}
.foodImg {
  width: 100px;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
</style>

