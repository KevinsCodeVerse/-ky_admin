<template>
  <div>
    <main-head :is-form-slot="false">

      <!--      <el-button type="primary" style="float: right; margin-right: 20px" size="small" @click="btn">保存</el-button>-->
      <!--      <el-button type="warning" style="float: right; margin-right: 20px" size="small" @click="getList">重置</el-button>-->
      <!--      <el-button type="success" style="float: right" size="small" @click="updata">修改</el-button>-->

    </main-head>

    <main-content>
        <el-card>
          <div slot="header" class="clearfix">
            <span style="font-weight: bold">设置推广商家入驻佣金</span>
          </div>
          <div style="font-size: 13px;padding: 20px">
            <div>
              <div style="display: flex;">
                <div style="display: flex;flex-direction: column;gap: 50px;margin-right: 50px;">
                  <div style="font-weight: bold">
                    推广等级
                  </div>
                  <div style="font-weight: bold">
                    普通推广员
                  </div>
                  <div style="font-weight: bold">
                    黄金推广员
                  </div>
                  <div style="font-weight: bold">
                    合伙人
                  </div>
                  <div style="font-weight: bold">
                    至尊合伙人
                  </div>
                </div>
                <div style="display: flex;flex-direction: column;gap: 30px;margin-left: 125px">
                  <div style="font-weight: bold">
                    直推佣金比例(0.01-1)
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.userDirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.goldDirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.partnerDirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.superPartnerDirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                </div>
                <div style="display: flex;flex-direction: column;gap: 30px;margin-left: 50px">
                  <div style="font-weight: bold">
                    间推佣金比例(0.01-1)
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.userIndirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.goldIndirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.partnerIndirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                  <div style="font-weight: bold">
                    <el-input-number :disabled="isUpdata" v-model="list.superPartnerIndirect" :precision="2" :step="0.01" :max="1"
                                     :min="0.00"></el-input-number>
                  </div>
                </div>

              </div>
              <el-divider></el-divider>
            </div>

          </div>
          <div style="display: flex">
            <el-button type="success" style="float: right; margin-right: 20px;width: 100px" size="small" @click="setConfig">保存</el-button>
          </div>

        </el-card>


        <el-card style="margin-top: 20px">
          <div slot="header" class="clearfix">
            <span style="font-weight: bold">分销等级</span>
          </div>
          <div style="font-size: 13px;padding: 20px">
            <div style="display: flex;">
              <div style="display: flex;flex-direction: column;gap: 50px;">
                <div style="font-weight: bold">
                  推广等级
                </div>
                <div style="font-weight: bold">
                  普通推广员
                </div>
                <div style="font-weight: bold">
                  黄金推广员
                </div>
                <div style="font-weight: bold">
                  合伙人
                </div>
                <div style="font-weight: bold">
                  至尊合伙人
                </div>
                <div style="font-weight: bold">
                  <el-button type="success" style="float: right; margin-right: 20px;width: 100px" size="small" @click="setConfig">保存</el-button>
                </div>
              </div>
              <div style="display: flex;flex-direction: column;gap: 30px;margin-left: 125px">
                <div style="font-weight: bold">
                  升级所需个人业绩（直推）
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="true" v-model="defaultValue" width="200px"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.goldPerson" width="200px"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.partnerPerson" width="200px"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.superPartnerPerson" width="200px"></el-input>
                </div>
              </div>
              <div style="display: flex;flex-direction: column;gap: 30px;margin-left: 50px">
                <div style="font-weight: bold">
                  升级所需团队业绩（间推）
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="true" v-model="defaultValue" width="200px"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.goldTeam"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.partnerTeam"></el-input>
                </div>
                <div style="font-weight: bold">
                  <el-input :disabled="isUpdata" v-model="list.superPartnerTeam"></el-input>
                </div>
              </div>
            </div>
          </div>

        </el-card>
<!--      </view>-->

    </main-content>

  </div>
</template>

<script>
export default {
  data() {
    return {
      vipProList: [],
      vipList: [],
      defaultValue: "无",
      list: {},
      rules: {},
      isUpdata: false
    };
  },

  watch: {},

  mounted() {
    this.getList();
  },

  methods: {
    // 修改
    updata() {
      this.isUpdata = false;
    },
    // 保存
    setConfig() {
      this.$confirm('保存为最新配置, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'success'
      })
          .then(() => {
            this.$request.post({
              url: '/admin/adInviteConfig/setConfig',
              params: this.list,
              success: (result) => {
                this.$message.success('修改成功');
              },
              finally: () => {
              }
            });
          })
          .catch((err) => {
            return err;
          });
    },
    // 请求 list
    getList() {
      this.$request.post({
        url: '/admin/adInviteConfig/show',
        success: (result) => {
          this.list = result || {};
          console.log(result);
        },
        finally: () => {

        }
      });
    }
  }
};
</script>

<style></style>

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
</style>
