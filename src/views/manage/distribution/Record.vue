<template>
  <div id="user_promoter_aduit_list">
    <!-- -----------------------------------筛选------------------------------------ -->
    <main-head>
      <el-form :inline="true" size="medium">
        <!-- <el-form-item>
            <el-select placeholder="分销员类型" v-model="screenData.isInside" clearable>
                <el-option value="" label="全部分销员"></el-option>
                <el-option value="1" label="内部人员"></el-option>
                <el-option value="0" label="普通人员"></el-option>
            </el-select>
        </el-form-item>-->
        <!-- <el-form-item>
            <el-select placeholder="状态" v-model="screenData.status" clearable @change="search">
                <el-option v-for="(item,index) in statusList" :key="index" :value="item.value" :label="item.label"></el-option>
            </el-select>
        </el-form-item> -->

        <el-form-item>
          <el-input placeholder="用户昵称/真实姓名" @keyup.enter.native="search" clearable v-model="screenData.keyWord"></el-input>
        </el-form-item>
        <!-- <el-form-item>
          <el-input placeholder="订单号搜索" @keyup.enter.native="search" clearable v-model="screenData.orderId"></el-input>
        </el-form-item> -->
        <el-form-item>
          <el-select placeholder="分销等级" v-model="screenData.adGrade" clearable @change="search">
            <el-option value="0" label="普通会员"></el-option>
            <el-option value="1" label="黄金会员"></el-option>
            <el-option value="2" label="合伙人"></el-option>
            <el-option value="3" label="至尊合伙人"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-date-picker v-model="selectData" type="daterange" range-separator="-" start-placeholder="开始日期" end-placeholder="结束日期"
                          clearable @change="search">
          </el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" icon="el-icon-search" @click="search">搜索</el-button>
        </el-form-item>
        <!--        <el-form-item>-->
        <!--          <el-button type="warning" @click="reset">重置</el-button>-->
        <!--        </el-form-item>-->
        <!--        <el-form-item>-->
        <!--          <el-button plain type="primary" @click="typefiltrate(1)">直推</el-button>-->
        <!--          <el-button plain type="warning" @click="typefiltrate(2)">间推</el-button>-->
        <!--        </el-form-item>-->
      </el-form>
    </main-head>
    <main-content>
      <!-- -----------------------------------列表------------------------------------ -->
      <el-table :data="list" stripe v-loading="loading" style="width: 100%" height="570">
        <!-- <el-table-column align="center" width="180" prop="orderId" label="订单号"></el-table-column> -->
        <el-table-column align="center" width="140px" prop="orderAmount" label="订单金额">
          <template slot-scope="scope">
            <span class="warning">￥{{ scope.row.orderAmount }}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" width="180px" prop="nick" label="分配对象">
          <template slot-scope="scope">
            <el-image fit="cover" style="width: 60px; height: 60px" :src="scope.row.avatar"
                      :preview-src-list="[scope.row.avatar]"></el-image>
            <p>{{ scope.row.nick }}</p>
          </template>
        </el-table-column>
        <el-table-column align="center" prop="adGrade" label="分销等级" width="120px">
          <template slot-scope="scope">
            <el-popover placement="top-start" :title="isgradeText(scope.row.adGrade)" width="300" trigger="hover" content="">
              <div class="dicc">
                <p>直接推荐用户下单可获订单总额{{ (scope.row.adGrade==0? inviteConfig.userDirect:
                  scope.row.adGrade==1?inviteConfig.goldDirect:
                  scope.row.adGrade==2?inviteConfig.partnerDirect:inviteConfig.superPartnerDirect)*100}}%佣金</p>
                <p>简介推荐用户下单可获订单总额{{ (scope.row.adGrade==0? inviteConfig.userIndirect:
                  scope.row.adGrade==1?inviteConfig.goldIndirect:
                  scope.row.adGrade==2?inviteConfig.partnerIndirect:inviteConfig.superPartnerIndirect)*100}}%佣金</p>
                <div v-if="scope.row.adGrade < 3">
                  <p style="margin: 10px 0px; font-size: 16px; color: #000">升级条件</p>
                  <p>累计{{ scope.row.adGrade==0? inviteConfig.goldPerson:
                  scope.row.adGrade==1?inviteConfig.partnerPerson:inviteConfig.superPartnerPerson}}直推业绩</p>
                  <p>累计{{ scope.row.adGrade==0? inviteConfig.goldTeam:
                  scope.row.adGrade==1?inviteConfig.partnerTeam:inviteConfig.superPartnerTeam}}间推业绩</p>
                </div>
                <p v-else style="margin: 10px 0px; font-size: 16px; color: #000">已是最高等级</p>
              </div>

              <div slot="reference" :class="isgrade(scope.row.adGrade)">{{ isgradeText(scope.row.adGrade) }}</div>
            </el-popover>
          </template>
        </el-table-column>
        <el-table-column align="center" prop="isInside" width="80" label="类型">
          <template slot-scope="scope">
            <el-tag type="primary" v-if="scope.row.type == 1">直推</el-tag>
            <el-tag type="warning" v-else>间推</el-tag>
          </template>
        </el-table-column>
        <el-table-column align="center" prop="isInside" width="80" label="状态">
          <template slot-scope="scope">
            <div v-for="(item,index) in statusList" :key="index" >
              <el-tag type="primary" v-if="item.value == scope.row.status">{{item.label}}</el-tag>
            </div>
          </template>
        </el-table-column>

        <el-table-column align="center" prop="secAmount" label="佣金">
          <template slot-scope="scope">
            <span class="error">￥{{ scope.row.secAmount }}</span>
          </template>
        </el-table-column>

        <el-table-column align="center" prop="remark" label="备注">
          <template slot-scope="scope">
            {{ scope.row.remark === undefined ? "无" : scope.row.remark }}
          </template>
        </el-table-column>
        <el-table-column label="时间" align="center">
          <template slot-scope="scope">
            {{ $moment(scope.row.createTime).format('Y-MM-DD HH:mm:ss') }}
          </template>
        </el-table-column>
      </el-table>

    </main-content>

    <main-footer>
      <el-pagination
          class="align-center"
          background
          @current-change="pageChange"
          :current-page.sync="screenData.pageNo"
          :page-size="10"
          layout="total, prev, pager, next, jumper"
          :total="itemTotal"
      >
      </el-pagination>
    </main-footer>
    <!-- 分页功能 -->

    <el-dialog title="添加标识" :visible.sync="show" width="30%" @close="(show = false), (form = {})">
      <el-form :model="form" ref="form" :rules="rules" label-width="80px" :inline="false" size="normal">
        <el-form-item label="真实姓名">
          <el-input v-model="form.realName"></el-input>
        </el-form-item>
        <el-form-item label="内部人员">
          <el-radio v-model="form.isInside" :label="1">是</el-radio>
          <el-radio v-model="form.isInside" :label="0">否</el-radio>
        </el-form-item>
      </el-form>
      <span slot="footer">
                <el-button @click="show = false">取消</el-button>
                <el-button type="primary" @click="identification">确认</el-button>
            </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rules: {},
      selectData: '',
      show: false,
      form: {
        isInside: 0,
        realName: ''
      },
      list: [], //数据
      loading: false,
      screenData: {
        usType: '', //筛选参数
        status: '',
        pageNo: 1,
        pageSize: 10,
        isInside: '',
        type: '',
        orderId: '',
        realName: '',
        keyWord:'',
        adGrade:'',
      },
      itemTotal: 0,
      statusList:[
        {
          value: 1,
          label: '冻结中'
        },
        {
          value: 2,
          label: '已发放'
        },
        {
          value: 3,
          label: '已退款'
        }
      ],
      inviteConfig:{},
    };
  },

  watch: {},
  methods: {
    // 判断直推或间推
    typefiltrate(type) {
      if (type == 1) {
        this.screenData.type = 1;
      } else {
        this.screenData.type = 2;
      }

      this.getList(1);
    },
    // 判断等级
    isgrade(e) {
      switch (e) {
        case 0:
          return 'tag_grade_pt';
        case 1:
          return 'tag_grade_hj';
        case 2:
          return 'tag_grade_hh';
        case 3:
          return 'tag_grade_zz';
      }
    },
    isgradeText(e) {
      switch (e) {
        case 0:
          return '普通会员';
        case 1:
          return '黄金会员';
        case 2:
          return '合伙人';
        case 3:
          return '至尊合伙人';
      }
    },
    //--------------获取列表----------------------
    getList(pageNo) {
      this.loading = true;
      this.screenData.pageNo = pageNo || this.screenData.pageNo;
      this.$request.post({
        url: '/admin/userRoyalFlow/list',
        params: this.screenData,
        success: (res) => {
          this.list = res.list;
          this.itemTotal = res.total;
        },
        finally: () => {
          this.loading = false;
          setTimeout(() => {
            this.$store.commit('routes/SET_LOADING', false);
          }, 200)
        }
      });
    },
    // 重置
    reset() {
      this.screenData = {
        //筛选参数
        usType: '', //筛选参数
        status: '',
        pageNo: 1,
        pageSize: 10,
        isInside: '',
        type: '',
        orderId: '',
        realName: ''
      };
      this.getList();
    },
    // 搜索
    search() {
      this.screenData.pageNo = 1;
      this.screenData.isCount = true;
      if (this.selectData && this.selectData.length) {
        this.screenData.startTime = this.$moment(this.selectData[0]).format('Y-MM-DD HH:mm:ss');
        this.screenData.endTime = this.$moment(this.selectData[1]).add(1, 'days').format('Y-MM-DD HH:mm:ss');
      } else {
        this.screenData.startTime = '';
        this.screenData.endTime = '';
        this.selectData = [];
      }
      this.getList(1);
    },

    // 标识
    biaoshi(row) {
      this.form.id = row.id;
      this.show = true;
      this.form.isInside = row.isInside;
      this.form.realName = row.realName;
    },
    //页面改变
    pageChange(current) {
      if (current) {
        this.screenData.pageNo = current;
        this.getList(this.screenData.pageNo);
      }
    },
    //
    identification() {
      this.$request.post({
        url: '/tenant/tenantInvite/markInvite',
        params: {
          id: this.form.id,
          isInside: this.form.isInside,
          realName: this.form.realName
        },
        success: () => {
          this.getList();
          this.show = false;
        },
        finally: () => {
        }
      });
    },
    getInviteConfig() {
      this.$request.post({
        url: '/admin/adInviteConfig/show',
        success: (result) => {
          this.inviteConfig = result
        },
        finally: () => {

        }
      });
    }
  },
  created() {
    this.getInviteConfig();
    this.getList(1);
  }
};
</script>

<style scoped>
/* dialog样式覆盖 */
/deep/ .el-dialog__header {
  background-color: #F5F5F5;
  border-bottom: #EBEBEB solid 1px;
}

/* 表格头样式覆盖 */
/deep/ .el-table .el-table__header th {
  background-color: #D7D7D7 !important;
  color: #666666 !important;
}

/* 标签样式覆盖 */
/deep/ .el-tabs__item {
  color: #898989 !important;
}

.mybtn {
  margin: 3px 10px;
}

.avatar {
  border-radius: 50%;
  width: 70px;
  height: 70px;
}

.dicc p {
  color: #888;
}

.tag_grade_pt {
  padding: 4px 6px;
  background-color: rgba(0, 0, 0, 0.041);
  border-radius: 3px;
  color: rgba(48, 48, 48, 0.822);
  border: 1px solid rgba(0, 0, 0, 0.096);
}

.tag_grade_hj {
  padding: 4px 6px;
  background-color: rgba(204, 201, 0, 0.103);
  border-radius: 3px;
  color: rgb(170, 167, 0);
  border: 1px solid rgba(204, 201, 0, 0.144);
}

.tag_grade_hh {
  padding: 4px 6px;
  background-color: rgba(232, 82, 82, 0.103);
  border-radius: 3px;
  color: rgb(232, 82, 82);
  border: 1px solid rgba(232, 82, 82, 0.144);
}

.tag_grade_zz {
  padding: 4px 6px;
  background-color: rgba(189, 125, 225, 0.103);
  border-radius: 3px;
  color: rgb(177, 101, 221);
  border: 1px solid rgba(189, 125, 225, 0.144);
}
</style>
