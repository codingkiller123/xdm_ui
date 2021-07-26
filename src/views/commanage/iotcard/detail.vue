<template>
  <div class="app-container">
    <div  class="el-tabs el-tabs--top">
      <div class="el-tabs__header is-top">
        <div class="el-tabs__nav-wrap is-top">
          <div class="el-tabs__nav-scroll">
            <div role="tablist" class="el-tabs__nav is-top" style="transform: translateX(0px);">
              <div class="el-tabs__active-bar is-top" style="width: 56px; transform: translateX(0px);"></div>
              <div id="tab-first" aria-controls="pane-first" role="tab" aria-selected="true" tabindex="0" class="el-tabs__item is-top is-active">
                基本信息
              </div>
              <div id="tab-second" aria-controls="pane-second" role="tab" tabindex="-1" class="el-tabs__item is-top">
                已订购套餐
              </div>
              <div id="tab-third" aria-controls="pane-third" role="tab" tabindex="-1" class="el-tabs__item is-top">
                充值记录
              </div>
              <div id="tab-fourth" aria-controls="pane-fourth" role="tab" tabindex="-1" class="el-tabs__item is-top">
                流量使用趋势
              </div>
              <div id="tab-message" aria-controls="pane-message" role="tab" tabindex="-1" class="el-tabs__item is-top">
                短信记录
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
      <div class="el-tabs__content">
        <div  role="tabpanel" id="pane-first" aria-labelledby="tab-first" class="el-tab-pane">
          <div  class="el-row" style="margin-left: -16px; margin-right: -16px;">
            <div  class="el-col el-col-24 el-col-xs-24 el-col-sm-24 el-col-lg-8" style="padding-left: 16px; padding-right: 16px;">
              <div  class="el-card box-card is-always-shadow" style="height: 500px;">
                <div class="el-card__header">
                  <div  class="clearfix">
                    <span >基本信息</span>
                  </div>
                </div>
                <div class="el-card__body">
                  <div   class="el-row">
                    <el-form ref="form" :model="form" label-width="100px" size="mini">
                    <div   class="spanCol el-col el-col-24">
                      ICCID:{{ iotCardDetail.iccid }}
                    </div>
                    <div   class="spanCol el-col el-col-24">
                      MSISDN:{{ iotCardDetail.msisdn }}
                    </div>
                    <div   class="spanCol el-col el-col-24">
                      运营商类型:{{ iotCardDetail.operatorType }}
                    </div>
                    </el-form>>
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
      // 选中数组
      ids: [],
      // 非单个禁用
      single: true,
      // 非多个禁用
      multiple: true,
      // 显示搜索条件
      showSearch: true,
      // 部门树选项
      deptOptions: [],
      // 总条数
      total: 0,
      // 卡详情
      iotCardDetail:undefined,
      // 公告表格数据
      iotCardList: [],
      // 弹出层标题
      title: "",
      open2:false,
      // 是否显示弹出层
      open: false,
      // 类型数据字典
      statusOptions: [],
      // 状态数据字典
      typeOptions: [],
      // 查询参数
      queryParams: {
        id: 317,
        pageNum: 1,
        pageSize: 10,
        noticeTitle: undefined,
        createBy: undefined,
        status: undefined
      },
      // 表单参数
      form: {},
      // 表单校验
      rules: {
        noticeTitle: [
          { tradeId: true, message: "客户类型不能为空", trigger: "change" }
        ]
      }
    };
  },
  created() {
    const iccId = this.$route.params && this.$route.params.iccid;
    this.getCardDetail(iccId);
    /** 获取公司客户列表 */
    optionList().then(response => {
      this.deptOptions = this.handleTree(response.data, "deptId");
    });
    /** 获取原始卡状态字典 */
    this.getDicts("com_iotcard_status").then(response => {
      this.statusOptions = response.data;
    });
    this.getDicts("sys_notice_type").then(response => {
      this.typeOptions = response.data;
    });
  },
  methods: {
    /** 查询卡列表 */
    getCardDetail(iccId) {
      this.loading = true;
      getIotCardType(this.queryParams).then(response => {
        this.iotCardDetail = response.data;
        this.loading = false;
      });
    },
    /** 转换部门数据结构 */
    normalizer(node) {
      if (node.children && !node.children.length) {
        delete node.children;
      }
      return {
        id: node.deptId,
        label: node.deptName,
        children: node.children
      };
    },
    // 卡状态字典翻译
    statusFormat(row, column) {
      return this.selectDictLabel(this.statusOptions, row.status);
    },
    // 公告状态字典翻译
    typeFormat(row, column) {
      return this.selectDictLabel(this.typeOptions, row.noticeType);
    }
  }
};
</script>
