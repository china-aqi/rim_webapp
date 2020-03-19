<template>
  <div>
    <el-card class="box-card" id="RIMValue">
      <div slot="header" class="clearfix">
        <span>{{ name }}: {{ code }}</span>
      </div>
      <div>
        <el-row :gutter="20">
          <el-col :span="11">
            <div class="grid-content bg-purple">
              <div class="grid-content bg-purple-light">
                <RIMValueResult :result="getRimValue()"></RIMValueResult>
              </div>
              <el-row :gutter="10">
                <el-col :span="12">
                  <div class="grid-content bg-purple-dark">
                    <RIMValueTable
                      :tbl-data="get2018Indicator()"
                    ></RIMValueTable>
                  </div>
                </el-col>
                <el-col :span="12">
                  <div class="grid-content bg-purple-dark">
                    <RIMValueTable :tbl-data="getForecast()"></RIMValueTable>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="5">
            <div class="grid-content bg-purple"><RIMValueIntroduction></RIMValueIntroduction></div>
          </el-col>
          <el-col :span="8">
            <div class="grid-content bg-purple" v-if="rData != null">
              <RIMValueSlider
                :config-info="get_configT1_T2()"
                :caption-fn="
                  v =>
                    '盈利成长期持续' +
                    v +
                    '年， ROE均值回归期持续' +
                    (rData['t1_t2'] - v) +
                    '年'
                "
                @func="getValueT1T2FormSon"
              ></RIMValueSlider>
              <RIMValueSlider
                :config-info="get_configR()"
                :caption-fn="v => '投入资本回报率' + (v * 100).toFixed(1) + '%'"
                @func="getValueRFormSon"
              ></RIMValueSlider>
              <RIMValueSlider
                :config-info="get_configG1()"
                :caption-fn="v => 'EPS增长期的增长率' + (v * 100).toFixed(1) + '%'"
                @func="getValueG1FormSon"
              ></RIMValueSlider>
              <RIMValueSlider
                :config-info="get_configG2()"
                :caption-fn="v => '持续经营期剩余收益增长率' + (v * 100).toFixed(1) + '%'"
                @func="getValueG2FormSon"
              ></RIMValueSlider>
            </div>
          </el-col>
        </el-row>
      </div>
    </el-card>
  </div>
</template>

<script>
import axios from "axios";
import RIMValueResult from "../components/RIMValueResult";
import RIMValueTable from "../components/RIMValueTable";
import RIMValueSlider from "../components/RIMValueSlider";
import RIMValueIntroduction from "../components/RIMValueIntroduction";
export default {
  name: "Detail",
  components: {RIMValueIntroduction, RIMValueTable, RIMValueResult, RIMValueSlider },
  data() {
    return {
      rData: null,
      code: this.$route.query.code,
      name: this.$route.query.name,
      RValue: 0.1,
      G1Value: 0.1,
      G2Value: 0.02,
      T1Value: 7,
      T2Value: 5
    };
  },
  methods: {
    get2018Indicator() {
      if (this.rData != null) {
        return [
          {
            des: "BPS" + this.rData["last_bps"][0],
            value: this.rData["last_bps"][1].toFixed(2)
          },
          {
            des: "EPS" + this.rData["last_eps"][0],
            value: this.rData["last_eps"][1].toFixed(2)
          }
        ];
      }
    },

    getForecast() {
      if (this.rData != null) {
        return [
          {
            des: this.rData["analysis_eps"][0][0] + "EPS",
            value: this.rData["analysis_eps"][0][1].toFixed(2)
          },
          {
            des: this.rData["analysis_eps"][1][0] + "EPS",
            value: this.rData["analysis_eps"][1][1].toFixed(2)
          },
          {
            des: this.rData["analysis_eps"][2][0] + "EPS",
            value: this.rData["analysis_eps"][2][1].toFixed(2)
          }
        ];
      }
    },

    get_configT1_T2() {
      if (this.rData != null) {
        return {
          name: "增长期/回落期",
          min: this.rData["t1_range"][0],
          max: this.rData["t1_t2"],
          step: 1,
          defaultValue: this.rData["t1_range"][1]
        };
      }
    },

    get_configR() {
      if (this.rData != null) {
        return {
          name: "r",
          min: this.rData["capital_return_rate_range"][0],
          max: this.rData["capital_return_rate_range"][1],
          step: 0.005,
          defaultValue: this.rData["capital_return_rate_default"]
        };
      }
    },

    get_configG1() {
      if (this.rData != null) {
        return {
          name: "g1",
          min: this.rData["g1_range"][0],
          max: this.rData["g1_range"][1],
          step: 0.01,
          defaultValue: this.rData["g1_default"]
        };
      }
    },

    get_configG2() {
      if (this.rData != null) {
        return {
          name: "g2",
          min: this.rData["g2_range"][0],
          max: this.rData["g2_range"][1],
          step: 0.005,
          defaultValue: this.rData["g2_default"]
        };
      }
    },

    getValueT1T2FormSon(data) {
      this.T1Value = data;
      this.T2Value = this.rData["t1_t2"] - this.T1Value;
    },
    getValueRFormSon(data) {
      this.RValue = data;
    },
    getValueG1FormSon(data) {
      this.G1Value = data;
    },
    getValueG2FormSon(data) {
      this.G2Value = data;
    },

    getRimValue() {
      if (this.rData != null) {
        let rimParameter2018 = {
          bps2018: this.rData["last_bps"][1],
          predicationEPS: this.rData["analysis_eps"],
          t1: this.T1Value,
          g1: this.G1Value,
          industry_roe: this.rData["industry_roe"],
          r: this.RValue,
          g2: this.G2Value
        };
        let result = [{ year: 2018, bps: this.rData["last_bps"][1] }];
        return this.calcRI2018(rimParameter2018, 2019, result)
          .slice(1)
          .reduce((x, y) => x + y.discounted_ri, 0)
          .toFixed(2);
      }
    },
    calcRI2018(p, year, result) {
      let eps = null;
      let last_result = result.slice(-1)[0];
      if (2019 <= year && year <= 2021) {
        eps = p.predicationEPS[year - 2019][1];
      } else if (year <= 2018 + this.T1Value) {
        eps = last_result.eps * (1 + p.g1);
      } else if (year <= 2030) {
        let last_roe = last_result.eps / last_result.bps;
        let delta_roe = (last_roe - p.industry_roe) / (2030 + 1 - year);
        eps = (last_roe - delta_roe) * last_result.bps;
      }

      if (year <= 2030) {
        let bps = last_result.bps + eps;
        let ri = eps - last_result.bps * p.r;
        result.push({
          year: year,
          eps: eps,
          bps: bps,
          ri: ri,
          discounted_ri: ri / Math.pow(1 + p.r, year - 2019)
        });
        return this.calcRI2018(p, year + 1, result);
      } else {
        let cv = (last_result.ri * (1 + p.g2)) / (p.r - p.g2);
        result.push({
          year: 2031,
          eps: 0,
          bps: 0,
          ri: cv,
          discounted_ri: cv / Math.pow(1 + p.r, year - 2019)
        });
        return result;
      }
    }
  },
  mounted() {
    axios
      .get("http://192.168.0.7:8001/v1.0/rim-proposal", {
        params: {
          code: this.$route.query.code
        }
      })
      .then(response => (this.rData = response.data));
  }
};
</script>

<style>
#RIMValue {
  width: 1200px;
  margin: auto;
}

.el-row {
  margin-bottom: 5px;
}
.el-col {
  border-radius: 4px;
}

.bg-purple-dark {
}

.bg-purple {
}

.bg-purple-light {
}

.grid-content {
  border-radius: 4px;
  min-height: 36px;
}

.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
</style>
