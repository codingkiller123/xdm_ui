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
                      ICCID:{{ form.iccid }}
                    </div>
                    <div   class="spanCol el-col el-col-24">
                      MSISDN:{{ form.msisdn }}
                    </div>
                    <div   class="spanCol el-col el-col-24">
                      运营商类型:{{ form.operatorType }}
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
import { listIotCard } from "@/api/commanage/iotcard";
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
    this.getList();
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
    getList() {
      this.loading = true;
      listIotCard(this.queryParams).then(response => {
        this.iotCardList = response.rows;
        this.total = response.total;
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
    },
    /** 详细按钮操作 */
    handleView(row) {
      this.open2 = true;
      this.form = row;
    },
    // 取消按钮
    cancel() {
      this.open = false;
      this.reset();
    },
    // 表单重置
    reset() {
      this.form = {
        noticeId: undefined,
        noticeTitle: undefined,
        noticeType: undefined,
        noticeContent: undefined,
        status: "0"
      };
      this.resetForm("form");
    },
    /** 搜索按钮操作 */
    handleQuery() {
      this.queryParams.pageNum = 1;
      this.getList();
    },
    /** 重置按钮操作 */
    resetQuery() {
      this.resetForm("queryForm");
      this.handleQuery();
    },
    // 多选框选中数据
    handleSelectionChange(selection) {
      this.ids = selection.map(item => item.noticeId)
      this.single = selection.length!=1
      this.multiple = !selection.length
    },
    /** 新增按钮操作 */
    handleAdd() {
      this.reset();
      this.open = true;
      this.title = "添加公告";
    },
    /** 修改按钮操作 */
    handleUpdate(row) {
      this.reset();
      const noticeId = row.noticeId || this.ids
      getNotice(noticeId).then(response => {
        this.form = response.data;
        this.open = true;
        this.title = "修改公告";
      });
    },
    /** 提交按钮 */
    submitForm: function() {
      this.$refs["form"].validate(valid => {
        if (valid) {
          if (this.form.noticeId != undefined) {
            updateNotice(this.form).then(response => {
              this.msgSuccess("修改成功");
              this.open = false;
              this.getList();
            });
          } else {
            addNotice(this.form).then(response => {
              this.msgSuccess("新增成功");
              this.open = false;
              this.getList();
            });
          }
        }
      });
    },
    /** 删除按钮操作 */
    handleDelete(row) {
      const noticeIds = row.noticeId || this.ids
      this.$confirm('是否确认删除公告编号为"' + noticeIds + '"的数据项?', "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(function() {
        return delNotice(noticeIds);
      }).then(() => {
        this.getList();
        this.msgSuccess("删除成功");
      }).catch(() => {});
    }
  }
};
</script>
