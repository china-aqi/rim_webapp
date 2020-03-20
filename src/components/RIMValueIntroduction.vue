<template>
  <el-card class="box-card introduction">
    <div slot="header" class="clearfix">
      <span>公司介绍</span>
    </div>
    <div class="infoContent" v-if="companyInfo != null">
      <ul>
        <li>
          <strong>市值：</strong>{{ companyInfo.market_value.toFixed(1) }}亿元
        </li>
        <li><strong>行业：</strong>{{ companyInfo.industry }}</li>
        <li v-if="this.companyInfo.main_business.length > 18">
          <strong>主营：</strong>{{ stringCut() }} ...
        </li>
        <li v-else><strong>主营：</strong>{{ stringCut() }}</li>
        <li><strong>注册：</strong>{{ this.companyInfo.registered_place }}</li>
        <li><strong>历史：</strong>{{ this.companyInfo.history }}</li>
      </ul>
    </div>
  </el-card>
</template>

<script>
export default {
  name: "RIMValueIntroduction",
  props: { companyInfo: Object },
  // data() {
  //   return {
  //     marketValue: this.companyInfo.market_value,
  //     industry: this.companyInfo.industry,
  //     mainBusiness: this.companyInfo.main_business,
  //     registeredPlace: this.companyInfo.registered_place,
  //     history: this.companyInfo.history
  //   };
  // },
  methods: {
    stringCut() {
      if (this.companyInfo.main_business.length > 18) {
        return this.companyInfo.main_business.slice(0, 17);
      } else return this.companyInfo.main_business;
    }
  }
};
</script>

<style>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.introduction {
  width: 180px;
  text-align: left;
}
.infoContent {
  font-size: small;
}
</style>
