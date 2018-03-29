<template>
  <div class="pos">
      <el-row>
          <el-col :span="7" class="pos-order" id="order-list">
              <el-tabs>
                  <el-tab-pane label="点餐">
                      <el-table border :data="tableData">
                      <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                      <el-table-column prop="count" label="数量"></el-table-column>
                      <el-table-column prop="goodsPrice" label="金额"></el-table-column>
                      <el-table-column  label="操作" fixed="right" width="100">
                          <template slot-scope="scope">
                              <el-button type="text" size="small" @click="delSingleGood(scope.row)">删除</el-button>
                              <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                          </template>
                      </el-table-column>
                      </el-table>
                      <div class="totalDiv">
                          <span>总数量：{{totalCount}}</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span>总金额：{{totalMoney}}元</span>  
                      </div>
                      <div class="div-btn">
                          <el-button type="warning">挂单</el-button>
                          <el-button type="danger" @click="delAllGoods">删除</el-button>
                          <el-button type="success">结账</el-button>
                      </div>
                  </el-tab-pane>
                  <el-tab-pane label="挂单">
                      挂单
                  </el-tab-pane>
                  <el-tab-pane label="外卖">
                      外卖
                  </el-tab-pane>
              </el-tabs>
          </el-col>
          <el-col :span="17">
              <div class="ofen-goods">
                  <div class="title">常用商品</div>
                  <div class="ofen-goods-list">
                    <ul>
                        <li v-for="oftenGood in oftenGoods" :key="oftenGood.goodsId" @click="addOrderList(oftenGood)">
                            <span>{{oftenGood.goodsName}}</span>
                            <span class="o-price">￥{{oftenGood.price}}元</span>
                        </li>
                    </ul>
                  </div>
              </div>
              <div class="goods-type">
                <el-tabs>
                    <el-tab-pane label="汉堡">
                           <div>
                               <ul class="cookList">
                                   <li v-for="goods in type0Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                                       <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                       <span class="foodName">{{goods.goodsName}}</span>
                                       <span class="foodPrice">￥{{goods.price}}元</span>
                                   </li>
                               </ul>
                           </div>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <div>
                                <ul class='cookList'>
                                    <li v-for="goods in type1Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <div>
                               <ul class="cookList">
                                   <li v-for="goods in type2Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                                       <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                       <span class="foodName">{{goods.goodsName}}</span>
                                       <span class="foodPrice">￥{{goods.price}}元</span>
                                   </li>
                               </ul>
                           </div>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                           <div>
                               <ul class="cookList">
                                   <li v-for="goods in type3Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                                       <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                       <span class="foodName">{{goods.goodsName}}</span>
                                       <span class="foodPrice">￥{{goods.price}}元</span>
                                   </li>
                               </ul>
                           </div>
                        </el-tab-pane>
                </el-tabs>
             </div>
          </el-col>
      </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      totalMoney: 0
    };
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        console.log(response);
        this.oftenGoods = response.data;
      })
      .catch(error => {
        console.log(error);
        alert("网路错误");
      });
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(response => {
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        console.log(error);
        alert("网路错误");
      });
  },
  methods: {
    addOrderList(goods) {
      this.totalCount = 0;
      this.totalMoney = 0;
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      if (isHave) {
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          goodsPrice: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      this.reCaculate();
    },
    delSingleGood(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.reCaculate();
    },
    reCaculate() {
      this.totalCount = 0;
      this.totalMoney = 0;
      this.tableData.forEach(element => {
        this.totalCount += element.count;
        this.totalMoney += element.count * element.goodsPrice;
      });
    },
    delAllGoods(){
        this.tableData=[];
        this.totalCount=0;
        this.totalMoney=0;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 20px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.ofen-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #ffffff;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auto;
  overflow: hidden;
  background-color: #ffffff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
  padding-top: 10px;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.totalDiv {
  border-bottom: #e5e9f2 1px solid;
  margin-top: 10px;
  line-height: 40px;
  height: 40px;
}
</style>
