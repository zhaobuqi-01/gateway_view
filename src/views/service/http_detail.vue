<template>
  <div class="mixin-components-container">
    <el-row>
      <el-card class="box-card">
        <div slot="header" class="clearfix" />
        <div slot="header" class="clearfix">
          <span>{{ form.service_name }} 详情</span>
        </div>
        <el-form ref="form" :model="form" label-width="140px">
          <el-form-item label="服务名称">
            <el-input v-model="form.service_name" readonly />
          </el-form-item>
          <el-form-item label="服务描述">
            <el-input v-model="form.service_desc" readonly />
          </el-form-item>
          <el-form-item label="接入类型">
            <el-input v-model="form.rule" readonly class="input-with-select">
              <el-select slot="prepend" v-model="form.rule_type" style="width:80px" readonly>
                <el-option label="路径" :value="0" />
                <el-option label="域名" :value="1" />
              </el-select>
            </el-input>
          </el-form-item>
          <el-form-item label="支持https">
            <el-switch v-model="form.need_https" :active-value="1" :inactive-value="0" disabled />
            <span style="color:#606266;font-weight: 700;">&nbsp;&nbsp;&nbsp;支持strip_uri&nbsp;&nbsp;</span>
            <el-switch
              v-model="form.need_strip_uri"
              :active-value="1"
              :inactive-value="0"
              disabled
            />
            <span style="color:#606266;font-weight: 700;">&nbsp;&nbsp;&nbsp;支持websocket&nbsp;&nbsp;</span>
            <el-switch
              v-model="form.need_websocket"
              :active-value="1"
              :inactive-value="0"
              disabled
            />
          </el-form-item>
          <el-form-item label="URL重写">
            <el-input v-model="form.url_rewrite" type="textarea" autosize readonly />
          </el-form-item>
          <el-form-item label="Header转换">
            <el-input v-model="form.header_transfor" type="textarea" autosize readonly />
          </el-form-item>
          <el-form-item label="开启验证">
            <el-switch v-model="form.open_auth" :active-value="1" :inactive-value="0" disabled />
          </el-form-item>
          <el-form-item label="IP白名单">
            <el-input v-model="form.white_list" type="textarea" autosize readonly />
          </el-form-item>
          <el-form-item label="IP黑名单">
            <el-input v-model="form.black_list" type="textarea" autosize readonly />
          </el-form-item>
          <el-form-item label="客户端限流">
            <el-input v-model="form.clientip_flow_limit" readonly />
          </el-form-item>
          <el-form-item label="服务端限流">
            <el-input v-model="form.service_flow_limit" readonly />
          </el-form-item>
          <el-form-item label="轮询方式">
            <el-radio-group v-model="form.round_type" disabled>
              <el-radio :label="0">random</el-radio>
              <el-radio :label="1">round-robin</el-radio>
              <el-radio :label="2">weight_round-robin</el-radio>
              <el-radio :label="3">ip_hash</el-radio>
            </el-radio-group>
          </el-form-item>

          <el-form-item label="IP列表">
            <el-input v-model="form.ip_list" type="textarea" autosize readonly />
          </el-form-item>

          <el-form-item label="权重列表">
            <el-input v-model="form.weight_list" type="textarea" autosize readonly />
          </el-form-item>

          <el-form-item label="建立连接超时">
            <el-input v-model="form.upstream_connect_timeout" readonly />
          </el-form-item>

          <el-form-item label="获取header超时">
            <el-input v-model="form.upstream_header_timeout" readonly />
          </el-form-item>

          <el-form-item label="链接最大空闲时间">
            <el-input v-model="form.upstream_idle_timeout" readonly />
          </el-form-item>

          <el-form-item label="最大空闲链接数">
            <el-input v-model="form.upstream_max_idle" readonly />
          </el-form-item>
        </el-form>
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
        service_name: '',
        service_desc: '',
        rule_type: 0,
        rule: '',
        need_https: 0,
        need_websocket: 0,
        need_strip_uri: 1,
        url_rewrite: '',
        header_transfor: '',
        round_type: 2,
        ip_list: '',
        weight_list: '',
        upstream_connect_timeout: '',
        upstream_header_timeout: '',
        upstream_idle_timeout: '',
        upstream_max_idle: '',
        open_auth: 0,
        black_list: '',
        white_list: '',
        clientip_flow_limit: '',
        service_flow_limit: ''
      }
    }
  },
  created() {
    const id = this.$route.params.id
    this.fetchData(id)
  },
  methods: {
    fetchData(id) {
      const query = { id: id }
      serviceDetail(query).then(response => {
        this.form.id = response.data.info.id
        this.form.load_type = response.data.info.load_type
        this.form.service_name = response.data.info.service_name
        this.form.service_desc = response.data.info.service_desc
        this.form.rule_type = response.data.http_rule.rule_type
        this.form.rule = response.data.http_rule.rule
        this.form.need_https = response.data.http_rule.need_https
        this.form.need_websocket = response.data.http_rule.need_websocket
        this.form.need_strip_uri = response.data.http_rule.need_strip_uri
        this.form.url_rewrite = response.data.http_rule.url_rewrite.replace(
          /,/g,
          '\n'
        )
        this.form.header_transfor = response.data.http_rule.header_transfor.replace(
          /,/g,
          '\n'
        )
        this.form.round_type = response.data.load_balance.round_type
        this.form.round_type = response.data.load_balance.round_type
        this.form.ip_list = response.data.load_balance.ip_list.replace(
          /,/g,
          '\n'
        )
        this.form.weight_list = response.data.load_balance.weight_list.replace(
          /,/g,
          '\n'
        )
        this.form.upstream_connect_timeout =
          response.data.load_balance.upstream_connect_timeout
        this.form.upstream_header_timeout =
          response.data.load_balance.upstream_header_timeout
        this.form.upstream_idle_timeout =
          response.data.load_balance.upstream_idle_timeout
        this.form.upstream_max_idle =
          response.data.load_balance.upstream_max_idle
        this.form.upstream_max_idle =
          response.data.load_balance.upstream_max_idle
        this.form.open_auth = response.data.access.open_auth
        this.form.black_list = response.data.access.black_list.replace(
          /,/g,
          '\n'
        )
        this.form.white_list = response.data.access.white_list.replace(
          /,/g,
          '\n'
        )
        this.form.clientip_flow_limit = response.data.access.clientip_flow_limit
        this.form.service_flow_limit = response.data.access.service_flow_limit
      })
    }
  }
}
</script>

<style scoped>
.mixin-components-container {
  margin: 20px;
}

.input-with-select {
  width: 100%;
}

.el-form-item__label {
  padding-top: 8px;
}
</style>
