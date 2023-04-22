<template>
  <div class="mixin-components-container">
    <el-row>
      <el-form ref="form" :model="form" label-width="130px">
        <el-card class="box-card" style="margin-bottom:20px;">
          <div slot="header" class="clearfix">
            <span>TCP服务详情</span>
          </div>
          <div>
            <el-form-item label="服务名称">
              <el-input v-model="form.service_name" disabled />
            </el-form-item>
            <el-form-item label="服务描述">
              <el-input v-model="form.service_desc" disabled />
            </el-form-item>
            <el-form-item label="端口">
              <el-input v-model="form.port" disabled />
            </el-form-item>
            <el-form-item label="开启验证">
              <el-switch v-model="form.open_auth" active-value="1" inactive-value="0" disabled />
            </el-form-item>
            <el-form-item label="IP白名单">
              <el-input
                v-model="form.white_list"
                disabled
                type="textarea"
                :autosize="{ minRows: 2, maxRows: 20}"
              />
            </el-form-item>

            <el-form-item label="IP黑名单">
              <el-input
                v-model="form.black_list"
                disabled
                type="textarea"
                :autosize="{ minRows: 2, maxRows: 20}"
              />
            </el-form-item>

            <el-form-item label="客户端限流">
              <el-input v-model="form.clientip_flow_limit" disabled />
            </el-form-item>

            <el-form-item label="服务端限流">
              <el-input v-model="form.service_flow_limit" disabled />
            </el-form-item>

            <el-form-item label="轮询方式">
              <el-radio v-model="form.round_type" label="0" disabled>random</el-radio>
              <el-radio v-model="form.round_type" label="1" disabled>round-robin</el-radio>
              <el-radio v-model="form.round_type" label="2" disabled>weight_round-robin</el-radio>
              <el-radio v-model="form.round_type" label="3" disabled>ip_hash</el-radio>
            </el-form-item>

            <el-form-item label="IP列表">
              <el-input
                v-model="form.ip_list"
                disabled
                type="textarea"
                :autosize="{ minRows: 2, maxRows: 20}"
              />
            </el-form-item>

            <el-form-item label="权重列表">
              <el-input
                v-model="form.weight_list"
                disabled
                type="textarea"
                :autosize="{ minRows: 2, maxRows: 20}"
              />
            </el-form-item>
          </div>
        </el-card>
      </el-form>
    </el-row>
  </div>
</template>
<script>
import { serviceDetail } from '@/api/service'
export default {
  components: {},
  data() {
    return {
      form: {
        id: '0',
        service_name: '',
        service_desc: '',
        port: '',
        rule_type: '0',
        rule: '',
        need_https: '0',
        need_strip_uri: '1',
        need_websocket: '1',
        url_rewrite: '',
        header_transfor: '',
        open_auth: '0',
        black_list: '',
        white_list: '',
        white_host_name: '',
        clientip_flow_limit: '',
        service_flow_limit: '',
        round_type: '2',
        ip_list: '',
        weight_list: '',
        forbid_list: '',
        upstream_connect_timeout: '',
        upstream_header_timeout: '',
        upstream_idle_timeout: '',
        upstream_max_idle: ''
      }
    }
  },
  created() {
    const id = this.$route.params && this.$route.params.id
    if (id > 0) {
      this.fetchData(id)
    }
    this.tempRoute = Object.assign({}, this.$route)
  },
  methods: {
    fetchData(id) {
      this.listLoading = true
      const query = {
        id: id
      }
      serviceDetail(query).then(response => {
        const formData = response.data
        this.form.id = formData.info.id
        this.form.service_name = formData.info.service_name
        this.form.service_desc = formData.info.service_desc

        this.form.port = formData.tcp_rule.port.toString()

        this.form.rule_type = formData.http_rule.rule_type.toString()
        this.form.rule = formData.http_rule.rule
        this.form.need_https = formData.http_rule.need_https.toString()
        this.form.need_strip_uri = formData.http_rule.need_strip_uri.toString()
        this.form.need_websocket = formData.http_rule.need_websocket.toString()
        this.form.url_rewrite = formData.http_rule.url_rewrite.replace(
          /,/g,
          '\n'
        )
        this.form.header_transfor = formData.http_rule.header_transfor.replace(
          /,/g,
          '\n'
        )

        this.form.open_auth = formData.access_control.open_auth.toString()
        this.form.black_list = formData.access_control.black_list.replace(
          /,/g,
          '\n'
        )
        this.form.white_list = formData.access_control.white_list.replace(
          /,/g,
          '\n'
        )
        this.form.white_host_name = formData.access_control.white_host_name.replace(
          /,/g,
          '\n'
        )
        this.form.clientip_flow_limit =
          formData.access_control.clientip_flow_limit
        this.form.service_flow_limit =
          formData.access_control.service_flow_limit

        this.form.round_type = formData.load_balance.round_type.toString()
        this.form.ip_list = formData.load_balance.ip_list.replace(/,/g, '\n')
        this.form.weight_list = formData.load_balance.weight_list.replace(
          /,/g,
          '\n'
        )
        this.form.forbid_list = formData.load_balance.forbid_list.replace(
          /,/g,
          '\n'
        )

        this.form.upstream_connect_timeout =
          formData.load_balance.upstream_connect_timeout
        this.form.upstream_header_timeout =
          formData.load_balance.upstream_header_timeout
        this.form.upstream_idle_timeout =
          formData.load_balance.upstream_idle_timeout
        this.form.upstream_max_idle = formData.load_balance.upstream_max_idle

        this.listLoading = false
      })
    }
  }
}
</script>

<style scoped>
.mixin-components-container {
  background-color: #f0f2f5;
  padding: 30px;
  min-height: calc(100vh - 84px);
}
.component-item {
  min-height: 100px;
}
.el-select .el-input {
  width: 130px;
}
.input-with-select .el-input-group__prepend {
  background-color: #fff;
}
</style>
