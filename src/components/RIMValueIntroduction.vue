<template>
  <el-card class="box-card introduction">
    <div slot="header" class="clearfix">
      <span>公司介绍</span>
    </div>
    <div>
      <ul>
        <li>市值：{{}}</li>
        <li>行业：{{}}</li>
        <li>主营：{{}}</li>
        <li>注册：{{}}</li>
        <li>历史：{{}}</li>
      </ul>
    </div>
  </el-card>
</template>

<script>
export default {
  name: "RIMValueIntroduction"
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
}
</style>
