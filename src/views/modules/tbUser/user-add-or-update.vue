<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="手机号" prop="mobile">
          <el-input v-model="dataForm.mobile" placeholder="手机号" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="普通用户标识" prop="wxOpenid">
          <el-input v-model="dataForm.wxOpenid" placeholder="普通用户的标识，对当前开发者帐号唯一" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="用户统一标识" prop="wxUnionid">
          <el-input v-model="dataForm.wxUnionid" placeholder="用户统一标识。针对一个微信开放平台帐号下的应用，同一用户的unionid是唯一的" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="创建时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="创建时间" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
         
          <el-form-item label="最后登录时间" prop="lastLogin">
          <el-input v-model="dataForm.lastLogin" placeholder="最后登录时间" :disabled="!!dataForm.id"></el-input>
      </el-form-item>

      <el-form-item label="账号状态" prop="status" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">正常</el-radio>
          <el-radio :label="1">封禁</el-radio>
        </el-radio-group>
      </el-form-item>

          <el-form-item label="封禁原因" prop="lockReason">
          <el-input v-model="dataForm.lockReason" placeholder="封禁原因"></el-input>
      </el-form-item>
          <el-form-item label="昵称" prop="nickName">
          <el-input v-model="dataForm.nickName" placeholder="昵称" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="头像地址" prop="avatar">
          <el-input v-model="dataForm.avatar" placeholder="头像地址" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="ip" prop="ip">
          <el-input v-model="dataForm.ip" placeholder="ip" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          
          <el-form-item label="账号" prop="username">
          <el-input v-model="dataForm.username" placeholder="账号(可以为空但是不能重复)" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
import debounce from 'lodash/debounce'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        mobile: '',
        wxOpenid: '',
        wxUnionid: '',
        createTime: '',
        vipDate: '',
        lastLogin: '',
        status: '',
        lockReason: '',
        nickName: '',
        avatar: '',
        ip: '',
        channel: '',
        username: '',
        password: '',
        pid: '',
        msgCount: '',
        money: '',
        leveloneProportion: '',
        secondaryProportion: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        vipDate: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        msgCount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        leveloneProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        secondaryProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.getInfo()
        }
      })
    },
    // 获取信息
    getInfo () {
      this.$http.get(`sys/tbUser/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/sys/tbUser/', this.dataForm).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.visible = false
              this.$emit('refreshDataList')
            }
          })
        }).catch(() => {})
      })
    }, 1000, { 'leading': true, 'trailing': false })
  }
}
</script>
