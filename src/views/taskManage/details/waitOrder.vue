<!--待接订单-->
<template>
  <div id="bBindShop">
      <div class="businessManage-title"></div>
      <div class="businessManage-table">

      <el-table border ref="multipleTable" size="mini" :data="businessDetails" tooltip-effect="dark" style="width: 100%">
       
        <el-table-column  align="center" prop="id" label="订单ID"></el-table-column>
        <el-table-column  align="center" prop="taskId" label="任务ID"></el-table-column>
        <el-table-column  align="center" label="商家ID">
           <template slot-scope="scope">
              <span class="">{{scope.row.taskVM.businesId}}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" prop="bussinessCapital" label="商家支付本金"></el-table-column>
       <!--  <el-table-column align="center" prop="businessPayments" label="商家支付金额"></el-table-column> -->
        <el-table-column align="center" prop="bussinessCommission" label="商家支付佣金"></el-table-column>
        <el-table-column align="center" prop="" label="商品价格"></el-table-column>
        <el-table-column align="center" prop="orderTime" label="创建时间" width="150">
          <template slot-scope="scope">
              <div>{{scope.row.taskVM?scope.row.taskVM.createTime:'' | dateParse}}</div>
          </template>
        </el-table-column>
        <!-- <el-table-column align="center" prop="scalpuser.username" label="接单账号"></el-table-column> -->
        <el-table-column align="center" prop="status" label="订单状态" width="95">
          <template slot-scope="scope">
              <div><span :style="'display:inline-block;position:relative;top:-2px;right:5px;height:5px;width:5px;border-radius:50%;background:'+(scope.row.status=='已完成'?'green':'red')"></span>{{scope.row.status}}</div>
          </template>
        </el-table-column>
      <!--   <el-table-column align="center" prop="dianfu" label="垫付资金"></el-table-column>
        <el-table-column align="center" prop="payments" label="用户付款"></el-table-column>
        <el-table-column align="center" prop="refund" label="商家返款"></el-table-column>
        <el-table-column align="center" prop="taobaoorderid" label="淘宝订单号" width="180"></el-table-column>
       <el-table-column  align="center" label="商品链接" width="80">
          <template slot-scope="scope">
            <span class="linkSpan linkClass" :data-clipboard-text="scope.row.taskVM?scope.row.taskVM.commodityLink:''" :title="scope.row.taskVM?scope.row.taskVM.commodityLink:''" @click="toCopy">复制链接</span>
          </template>
        </el-table-column> -->
      </el-table>
    </div>
    <div class="recharge-page" style="margin-top:20px;">
       <el-pagination background @current-change="handleCurrentChange" :page-size="pagesize" layout="total, prev, pager, next" :total="total"></el-pagination>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import config from '@/config.js'
import Clipboard from 'clipboard';

import service from '@/utils/request'
export default {
  name: 'businessDetails',
  data(){
    return {
       pagesize:10,
       page:0,
      total:10,
      businessDetails:[]
    }
  },
  created(){
    this.loadVoucher()
  },
  computed: {
   ...mapGetters(['businessData','businessDetailsId'])

  },
  watch:{
    page:function(now,old) {
      this.loadVoucher();
    }
  },
  methods: {
    /* 分页页数更改之后 */
    handleCurrentChange(val) {
       this.page = val -1
    },
    loadVoucher(){
      let obj={
        params:{
          status:'待接单',
          businesId:this.businessDetailsId,
          page:this.page,
          pageSize: this.pagesize
        }
      }
      /* 通过商家id获取商家基本信息 */
   service.get('/order/query', obj).then((data) => {
    // console.log('=====',data)
      if(data.data.status==200){
        this.businessDetails = data.data.data.list;
        this.total = data.data.data.total
      }else{
        config.errorTitle(this,data.status,'获取信息失败');
      }
    }).catch(() => {
      this.$notify({title:'失败',type: 'error', message: '获取信息失败!'});
    })

    },

     /* 复制链接 */
    toCopy(){
      const shopClipboard = new Clipboard('.linkClass');
      let vm = this;
      shopClipboard.on('success', function() {
        vm.$notify({title:'成功',type: 'success', message:'复制成功!'});
        shopClipboard.destroy();
      });
      shopClipboard.on('error', function() {
        vm.$notify({title:'失败',type: 'error', message:'复制失败!'});
        shopClipboard.destroy();
      });
    },
    /* 返回 */
    toBack(){
      this.$router.push(this.$route.query.path);
    },
  },
}
</script>
<style lang="scss" scoped>

  #bBindShop{
    .businessManage-title{
      margin-top:10px;
      line-height: 40px;
      border-bottom:3px solid rgb(232, 232, 232);
    }
     .businessManage-table{
      margin-top:10px;
      border-top:1px solid #ebeef5;
      .iconDiv:first-child{
        margin-right: 10px;
      }
    }
    font-size:14px;
    .bBindShop-title{
      overflow: hidden;
      &>div:first-child{
        float: left;
      }
      &>div:last-child{
        float: right;
      }
    }
    .infoContent{
      .baseInfo,.accountInfo{
        border-bottom:1px solid rgb(215, 215, 215);
        padding: 10px;
      }
      .infoTitle{
        line-height: 20px;
        padding:10px 0;
      }
    }
  }
</style>
