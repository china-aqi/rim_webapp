<template>
  <div class="searchbar">
    <el-autocomplete
      class="inline-input"
      v-model="state1"
      :fetch-suggestions="querySearch"
      placeholder="请输公司名称/公司代码/拼音简称"
      :trigger-on-focus="false"
      @select="handleSelect"
      style="width: 400px"
      ><i
            class="el-icon-search el-input__icon"
            slot="suffix"
>
    </i></el-autocomplete>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  data() {
    return {
      securities: [],
      info: [],
      state1: ""
    };
  },

  methods: {
    querySearch(queryString, cb) {
      let securities = this.securities;
      let results = queryString
        ? securities.filter(this.createFilter(queryString))
        : securities;
      // 调用 callback 返回建议列表的数据
      cb(results);
    },
    createFilter(queryString) {
      return securities => {
        return (
          securities.value.toLowerCase().indexOf(queryString.toLowerCase()) >= 0
        );
      };
    },
    handle_securities_4_combobox(response) {
      // this.securities = [{"value": response.data["hello world"][0][1] + "(" + response.data["hello world"][0][0] + ")"}];
      for (let i in response.data["hello world"]) {
        this.securities.unshift({
          value:
            response.data["hello world"][i][1] +
            "(" +
            response.data["hello world"][i][0] +
            "," +
            response.data["hello world"][i][2] +
            ")"
        });
      }
      this.info = response.data["hello world"];
    },
    handleSelect(item) {
      console.log(item);
      this.$router.push({
        path: "/RIMValue/",
        query: {
          code: this.state1.replace(/[^0-9]/gi, ""),
          name: this.state1.slice(0, this.state1.indexOf("("))
        }
      });
    },
    jump() {
      this.$router.push("/RIMValue/" + this.state1.replace(/[^0-9]/gi, ""));
    }
  },
  mounted() {
    axios
      .get("http://106.15.137.244:80/securities")
      .then(this.handle_securities_4_combobox);
  }
};
</script>

<style>
.searchbar {
  margin-top: 10%;
}
</style>
