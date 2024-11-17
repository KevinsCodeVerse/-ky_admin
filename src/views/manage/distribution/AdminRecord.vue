PR<template>
  <div id="user_promoter_aduit_list">
    <!-- -----------------------------------筛选------------------------------------ -->
    <el-form :inline="true" size="medium">
      <!-- <el-form-item>
                <el-select placeholder="类型" v-model="screenData.type" clearable>
                    <el-option value="" label="全部类型"></el-option>
                    <el-option value="1" label="集体"></el-option>
                    <el-option value="2" label="市级代理"></el-option>
                    <el-option value="3" label="平台"></el-option>
                </el-select>
            </el-form-item> -->
      <!-- <el-form-item>
                <el-select placeholder="状态" v-model="screenData.status" clearable>
                    <el-option value="" label="全部状态"></el-option>
                    <el-option value="1" label="审核通过"></el-option>
                    <el-option value="0" label="待审批"></el-option>
                    <el-option value="-1" label="不通关"></el-option>
                    <el-option value="2" label="普通用户"></el-option>
                </el-select>
            </el-form-item> -->

      <!-- <el-form-item>
                <el-input placeholder="手机号搜索" clearable v-model="screenData.mobile"></el-input>
            </el-form-item> -->
      <el-form-item>
        <el-select v-model="screenData.status" placeholder="类型" @change="search">
          <el-option value="" label="全部"></el-option>
          <el-option value="1" label="已发放"></el-option>
          <el-option value="2" label="支出"></el-option>
          <el-option value="3" label="冻结中"></el-option>
          <el-option value="4" label="已退款"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input placeholder="订单号搜索" clearable v-model="screenData.orderId"></el-input>
      </el-form-item>
      <!-- <el-form-item>
                <el-input placeholder="用户名称/真实姓名" clearable v-model="screenData.realName"></el-input>
            </el-form-item> -->
      <el-form-item>
        <el-date-picker v-model="selectData" type="daterange" range-separator="-" start-placeholder="开始日期" end-placeholder="结束日期" clearable @change="search"> </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="el-icon-search" @click="search">搜索</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="warning" @click="reset">重置</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="success" @click="exportEx">导出</el-button>
      </el-form-item>
      <!-- <el-form-item> -->
      <!-- <el-button plain type="warning" @click="typefiltrate(2)">间推</el-button> -->
      <!-- <el-button plain type="primary" @click="typefiltrate(1)">直推</el-button> -->
      <!-- </el-form-item> -->
    </el-form>

    <!-- -----------------------------------列表------------------------------------ -->
    <el-table :data="list" stripe v-loading="loading" style="width: 100%">
      <el-table-column align="center" width="180" prop="orderId" label="订单号"></el-table-column>
      <el-table-column align="center" width="140px" prop="orderMoney" label="订单金额">
        <template slot-scope="scope">
          <span class="warning">￥{{ scope.row.orderMoney }}</span>
        </template>
      </el-table-column>

      <el-table-column align="center" prop="nick" label="分配对象">
        <template slot-scope="scope">
          <!-- <el-image fit="cover" style=" height: 60px" :src="scope.row.avatar" :preview-src-list="[scope.row.avatar]"></el-image> -->
          <p>{{ scope.row.name }}</p>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="queryType" label="分配类型">
        <template slot-scope="scope">
          <span v-if="scope.row.type == 3">平台</span>
          <span v-else-if="scope.row.type == 2">城市代理</span>
          <span v-else-if="scope.row.type == 1">集体</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="" label="分配内容">
        <template slot-scope="scope"> {{ scope.row.remark }} </template>
      </el-table-column>

      <el-table-column align="center" prop="amount" label="分配金额">
        <template slot-scope="scope">
          <!-- <span class="error">￥{{ scope.row.money }}</span> -->
          <span v-if="scope.row.status == 1" class="success"> +{{ $common.toThousands(scope.row.money, true) }}</span>
          <span v-if="scope.row.status == 2" class="error"> -{{ $common.toThousands(scope.row.money, true) }}</span>
          <span v-if="scope.row.status == 3" class="lanse">{{ $common.toThousands(scope.row.money, true) }}</span>
          <span v-if="scope.row.status == 4" class="info"> {{ $common.toThousands(scope.row.money, true) }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="status" label="状态" align="center">
        <template slot-scope="scope">
          <span v-if="scope.row.status == 1" class="success">已发放</span>
          <span v-if="scope.row.status == 2" class="error">支出</span>
          <span v-if="scope.row.status == 3" class="lanse">冻结中</span>
          <span v-if="scope.row.status == 4" class="info">已退款</span>
        </template>
      </el-table-column>
      <!-- <el-table-column align="center" prop="isInside" width="80" label="类型">
                <template slot-scope="scope">
                    <el-tag type="primary" v-if="scope.row.type == 1">直推</el-tag>
                    <el-tag type="warning" v-else>间推</el-tag>
                </template>
            </el-table-column> -->
      <el-table-column label="时间" align="center">
        <template slot-scope="scope">
          <!-- {{ $moment(scope.row.createTime).format('Y-MM-DD HH:mm:ss') }} -->
          <timer :date="scope.row.createTime" format="Y-MM-DD HH:mm:ss" />
        </template>
      </el-table-column>

      <!-- <el-table-column align="center" label="操作" width="150px" fixed="right">
                <template slot-scope="scope">
                    <el-button class="mybtn" type="primary" size="mini" @click="biaoshi(scope.row)">标识修改</el-button>
                    <el-button v-if="scope.row.status != 2" class="mybtn" type="warning" size="mini" @click="call(scope.row)">取消资格</el-button>
                    <el-button v-if="scope.row.status == 2" class="mybtn" type="success" size="mini" @click="regain(scope.row)">恢复资格</el-button>
                </template>
            </el-table-column> -->
    </el-table>

    <!-- 分页功能 -->
    <el-pagination class="margin_top20 align-right" background @current-change="pageChange" :current-page.sync="screenData.pageNo" :page-size="40" layout="total, prev, pager, next, jumper" :total="itemTotal"> </el-pagination>
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
        pageSize: 40,
        isInside: '',
        type: '',
        orderId: '',
        realName: ''
      },
      itemTotal: 0,
    };
  },

  watch: {},
  methods: {
    // 导出
    exportEx() {
      this.$request.post({
        url: 'admin/adFlow/adminFlowExport',
        params: this.screenData,
        success: res => {
          let a = document.createElement('a');
          a.setAttribute('href', this.$envConfig.serviceUrl + res);
          a.click(); //下载 
          URL.revokeObjectURL(a.href); // 释放URL 对象
          document.body.removeChild(a); // 删除 a 标签
          this.$message.success('操作成功');
        },
        error: () => {},
        finally: () => {}
      });
    },
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
        url: '/admin/adFlow/adminFlow',
        params: this.screenData,
        success: res => {
          console.log(res.list);
          this.list = res.list;
          this.itemTotal = res.total;
        },
        finally: () => {
          this.loading = false;
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
        pageSize: 40,
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
        this.screenData.endTime = this.$moment(this.selectData[1])
          .add(1, 'days')
          .format('Y-MM-DD HH:mm:ss');
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
        finally: () => {}
      });
    }
  },
  created() {
    this.getList(1);
  }
};
</script>

<style scoped>
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
.lanse {
  color: #409eff;
}
</style>
