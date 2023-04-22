<template>
  <div class="grpc-detail-container">
    <el-row>
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>{{ form.service_name }} 详情</span>
        </div>
        <div>
          <el-form ref="form" :model="form" label-width="130px">
            <el-form-item label="服务名称">
              <el-input v-model="form.service_name" disabled />
            </el-form-item>
            <el-form-item label="服务描述">
              <el-input v-model="form.service_desc" disabled />
            </el-form-item>
            <el-form-item label="端口">
              <el-input v-model="form.port" disabled />
            </el-form-item>
            <el-form-item label="metadata转换">
              <el-input v-model="form.header_transfor" type="textarea" :autosize="{ minRows: 2, maxRows: 20}" disabled />
            </el-form-item>
            <el-form-item label="开启验证">
              <el-switch
                v-model="form.open_auth"
                active-value="1"
                inactive-value="0"
                disabled
              />
            </el-form-item>
            <el-form-item label="IP白名单">
              <el-input v-model="form.white_list" type="textarea" :autosize="{ minRows: 2, maxRows: 20}" disabled />
            </el-form-item>
            <el-form-item label="IP黑名单">
              <el-input v-model="form.black_list" type="textarea" :autosize="{ minRows: 2, maxRows: 20}" disabled />
            </el-form-item>
            <el-form-item label="客户端限流">
              <el-input v-model="form.clientip_flow_limit" disabled />
            </el-form-item>
            <el-form-item label="服务端限流">
              <el-input v-model="form.service_flow_limit" disabled />
            </el-form-item>
            <el-form-item label="轮询方式">
              <el-radio v-model="form.round_type" disabled label="0">random</el-radio>
              <el-radio v-model="form.round_type" disabled label="1">round-robin</el-radio>
              <el-radio v-model="form.round_type" disabled label="2">weight_round-robin</el-radio>
              <el-radio v-model="form.round_type" disabled label="3">ip_hash</el-radio>
            </el-form-item>
            <el-form-item label="IP列表">
              <el-input v-model="form.ip_list" type="textarea" :autosize="{ minRows: 2, maxRows: 20}" disabled />
            </el-form-item>
            <el-form-item label="权重列表">
              <el-input v-model="form.weight_list" type="textarea" :autosize="{ minRows: 2, maxRows: 20}" disabled />
            </el-form-item>
          </el-form>
        </div>
      </el-card>
    </el-row>
  </div>
</template>
<script>
import { serviceDetail } from '@/api/service'

export default {
  data() {
    return {
      form: {
        id: '',
        service_name: '',
        service_desc: '',
        port: '',
        header_transfor: '',
        open_auth: '',
        black_list: '',
        white_list: '',
        clientip_flow_limit: '',
        service_flow_limit: '',
        round_type: '',
        ip_list: '',
        weight_list: ''
      }
    }
  },
  created() {
    const id = this.$route.params.id
    this.fetchData(id)
  },
  methods: {
    fetchData(id) {
      serviceDetail({ id }).then(response => {
        const formData = response.data
        this.form.id = formData.info.id
        this.form.service_name = formData.info.service_name
        this.form.service_desc = formData.info.service_desc
        this.form.port = formData.grpc_rule.port.toString()
        this.form.header_transfor = formData.grpc_rule.header_transfor.replace(/,/g, '\n')
        this.form.open_auth = formData.access_control.open_auth.toString()
        this.form.black_list = formData.access_control.black_list.replace(/,/g, '\n')
        this.form.white_list = formData.access_control.white_list.replace(/,/g, '\n')
        this.form.clientip_flow_limit = formData.access_control.clientip_flow_limit
        this.form.service_flow_limit = formData.access_control.service_flow_limit
        this.form.round_type = formData.load_balance.round_type.toString()
        this.form.ip_list = formData.load_balance.ip_list.replace(/,/g, '\n')
        this.form.weight_list = formData.load_balance.weight_list.replace(/,/g, '\n')
      })
    }
  }
}
</script>
<style scoped>
.grpc-detail-container {
  background-color: #f0f2f5;
  padding: 30px;
  min-height: calc(100vh - 84px);
}
</style>
