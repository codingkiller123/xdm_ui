<template>
  <div class="app-container">
    <div  class="el-tabs el-tabs--top">
      <div class="el-tabs__header is-top">
        <div class="el-tabs__nav-wrap is-top">
          <div class="el-tabs__nav-scroll">
            <div role="tablist" class="el-tabs__nav is-top" style="transform: translateX(0px);">
              <div class="el-tabs__active-bar is-top" :style="transform"></div>
              <div id="tab-first" aria-controls="pane-first" v-on:click="paneSecond(0)" role="tab" aria-selected="true" tabindex="0" :class="transClass">
                基本信息
              </div>
              <div id="tab-second" aria-controls="pane-second" v-on:click="paneSecond(1)" role="tab" tabindex="-1" :class="transClassSecond">
                已订购套餐
              </div>
              <div id="tab-third" aria-controls="pane-third" v-on:click="paneSecond(2)" role="tab" tabindex="-1" :class="transClassThird">
                网络信息
              </div>
              <div id="tab-fourth" aria-controls="pane-fourth" v-on:click="paneSecond(3)" role="tab" tabindex="-1" :class="transClassFourth">
                变更记录查询
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
      <div class="el-tabs__content">
        <div  role="tabpanel" id="pane-first" aria-labelledby="tab-first" v-show="firstShow" class="el-tab-pane">
          <div  class="el-row" style="margin-left: -16px; margin-right: -16px;">
            <div  class="" style="padding-left: 16px; padding-right: 16px;">
              <div  class="el-card box-card is-always-shadow" style="height: 500px;">
                <div class="el-card__header">
                  <div  class="clearfix">
                    <span >基本信息</span>
                  </div>
                </div>
                <div class="el-card__body">
                  <div   class="el-row">
                    <el-form ref="form" :model="form" label-width="100px" size="mini">
                      <div class="spanCol el-col el-col-24">
                        iccid:{{ iotCardDetail.cardInfo.iccid }}
                      </div>
                      <div   class="spanCol el-col el-col-24">
                        msisdn:{{ iotCardDetail.cardInfo.msisdn }}
                      </div>
                      <div   class="spanCol el-col el-col-24">
                        移动用户识别码:{{ iotCardDetail.cardInfo.imsi }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        终端设备识别码:{{ iotCardDetail.cardInfo.imei }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        流量总量(单位:KB):{{ iotCardDetail.cardInfo.flowTotal }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        流量使用量(单位:KB):{{ iotCardDetail.cardInfo.flowUsed }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        流量剩余量(单位:KB):{{ iotCardDetail.cardInfo.flowLeft }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        激活时间:{{ iotCardDetail.cardInfo.activationTime }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        激活时间:{{ iotCardDetail.cardInfo.overTime }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        本月流量使用量(单位:KB):{{ iotCardDetail.cardInfo.currentMonthUsed }}
                      </div>
                      <div class="spanCol el-col el-col-24">
                        卡状态:{{ cardStatusFormat(iotCardDetail.cardInfo.status) }}
                      </div>
                    </el-form>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div  role="tabpanel" id="pane-second" aria-labelledby="tab-second" v-show="secondShow" class="el-tab-pane">
          <div  class="el-row" style="margin-left: -16px; margin-right: -16px;">
            <div  class="" style="padding-left: 16px; padding-right: 16px;">
              <div  class="el-card box-card is-always-shadow" style="height: 500px;">
                <div class="el-card__header">
                  <div  class="clearfix">
                    <span >套餐基本信息</span>
                  </div>
                </div>
                <div class="el-card__body">
                  <div   class="el-row">
                      <div v-for="cardOrder in iotCardDetail.cardInfo.cardOrderedPackageVOList" >
                        <div class="spanCol el-col el-col-24">
                          套餐名称:{{cardOrder.itemName}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          生效时间:{{cardOrder.itemEffectiveTime}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          失效时间:{{cardOrder.itemOverTime}}
                        </div>
                      </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div  role="tabpanel" id="pane-third" aria-labelledby="tab-third" v-show="thirdShow" class="el-tab-pane">
          <div  class="el-row" style="margin-left: -16px; margin-right: -16px;">
            <div  class="" style="padding-left: 16px; padding-right: 16px;">
              <div  class="el-card box-card is-always-shadow" style="height: 500px;">
                <div class="el-card__header">
                  <div  class="clearfix">
                    <span >网络基本信息</span>
                  </div>
                </div>
                <div class="el-card__body">
                  <div class="el-row">
                      <div v-for="online in iotCardDetail.onlineStateInfoList" >
                        <div class="spanCol el-col el-col-24">
                          apnId:{{online.apnId}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          在线状态:{{online.apnId}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          ip:{{online.ip}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          会话创建时间:{{online.createDate}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          接入方式:{{ratStatusFormat(online.rat)}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          IPV6地址前缀:{{online.ipv6Prefix}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          IPv6地址接口:{{online.ipv6}}
                        </div>
                      </div>
                      <div v-for="apn in iotCardDetail.apnListList" >
                         <div class="spanCol el-col el-col-24">
                          APN信息:{{apn.apnName}}
                         </div>
                         <div class="spanCol el-col el-col-24">
                          状态:{{apnStatusFormat(apn.status)}}
                         </div>
                      </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div  role="tabpanel" id="pane-fourth" aria-labelledby="tab-fourth" v-show="fourthShow" class="el-tab-pane">
          <div  class="el-row" style="margin-left: -16px; margin-right: -16px;">
            <div  class="" style="padding-left: 16px; padding-right: 16px;">
              <div  class="el-card box-card is-always-shadow" style="height: 500px;">
                <div class="el-card__header">
                  <div  class="clearfix">
                    <span >单卡状态变更历史查询</span>
                  </div>
                </div>
                <div class="el-card__body">
                  <div   class="el-row">
                    <el-form ref="form"  label-width="100px" size="mini">
                      <div v-for="history in iotCardDetail.stateChangeHistoryList" >
                        <div class="spanCol el-col el-col-24">
                          源状态:{{descStatusFormat(history.descStatus)}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          目标状态:{{descStatusFormat(history.targetStatus)}}
                        </div>
                        <div class="spanCol el-col el-col-24">
                          变更时间:{{history.changeDate}}
                        </div>
                      </div>
                    </el-form>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import { optionList } from "@/api/system/dept";
import { getIotCardType } from "@/api/commanage/iotcard";
import Treeselect from "@riophae/vue-treeselect";
import "@riophae/vue-treeselect/dist/vue-treeselect.css";
import Editor from '@/components/Editor';

export default {
  name: "Notice",
  components: {
    Editor,
    Treeselect
  },
  data() {
    return {
      // 遮罩层
      loading: true,
      transform:"width: 56px; transform: translateX(0px);",
      transClass:"el-tabs__item is-top is-active",
      transClassSecond:"el-tabs__item is-top",
      transClassThird:"el-tabs__item is-top",
      transClassFourth:"el-tabs__item is-top",
      firstShow:true,
      secondShow:false,
      thirdShow:false,
      fourthShow:false,
      statusOptions:[],
      // 卡详情
      iotCardDetail:undefined,
    };
  },
  created() {
    const iccId = this.$route.params && this.$route.params.dictId;
    this.getCardDetail(iccId);
    this.getDicts("iot_card_desc_status").then(response => {
      this.statusOptions = response.data;
    });
  },
  methods: {
    /** 查询卡列表 */
    getCardDetail(iccId) {
      this.loading = true;
      getIotCardType(iccId).then(response => {
        this.iotCardDetail = response.data;
        this.loading = false;
      });
    },
    // 卡状态字典翻译
    cardStatusFormat(row) {
      var map = ["其他","待激活","沉默期","测试期","正常","停机","已销号"];
      return map[row];
    },
    descStatusFormat(row) {
      return this.selectDictLabel(this.statusOptions, row);
    },
    ratStatusFormat(row) {
      var map = ["","3G","2G","","","","4G","","NB"];
      return map[row];
    },
    apnStatusFormat(row) {
      var map = ["离线","在线"];
      return map[row];
    },
    paneSecond(type){
      //点击之后显示
      var siteBar = "el-tabs__item is-top is-active";
      var noSiteBar = "el-tabs__item is-top";
      if(type == 0){
        this.firstShow = true;
        this.secondShow = false;
        this.thirdShow = false;
        this.fourthShow = false;
        this.transform = "width: 56px; transform: translateX(0px);";
        this.transClass = siteBar;
        this.transClassSecond = noSiteBar;
        this.transClassThird = noSiteBar;
        this.transClassFourth = noSiteBar;
      }else if(type == 1){
        this.firstShow = false;
        this.secondShow = true;
        this.thirdShow = false;
        this.fourthShow = false;
        this.transform = "width: 73px; transform: translateX(96px);";
        this.transClass = noSiteBar;
        this.transClassSecond = siteBar;
        this.transClassThird = noSiteBar;
        this.transClassFourth = noSiteBar;
      }else if(type == 2){
        this.firstShow = false;
        this.secondShow = false;
        this.thirdShow = true;
        this.fourthShow = false;
        this.transform = "width: 70px; transform: translateX(200px);";
        this.transClass = noSiteBar;
        this.transClassSecond = noSiteBar;
        this.transClassThird = siteBar;
        this.transClassFourth = noSiteBar;
      }else if(type == 3){
        this.firstShow = false;
        this.secondShow = false;
        this.thirdShow = false;
        this.fourthShow = true;
        this.transform = "width: 90px; transform: translateX(300px);";
        this.transClass = noSiteBar;
        this.transClassSecond = noSiteBar;
        this.transClassThird = noSiteBar;
        this.transClassFourth = siteBar;
      }
    }
  }
};
</script>
