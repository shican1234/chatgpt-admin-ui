<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-demo__user}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="用户id" header-align="center" align="center"></el-table-column>
        <el-table-column prop="mobile" label="手机号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="nickName" label="昵称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="avatar" label="头像" header-align="center" align="center">
          <template slot-scope="scope">
          <img :src="scope.row.avatar" width="40" height="40" alt=""/>
        </template>
        </el-table-column>
        <el-table-column prop="wxOpenid" label="Openid" header-align="center" align="center"></el-table-column>
        <el-table-column prop="wxUnionid" label="Unionid" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="lastLogin" label="最后登录时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="状态" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status == 0" type="success">正常</el-tag>
            <el-tag v-if="scope.row.status == 1" type="danger">封禁</el-tag>
        </template>
      </el-table-column>
        
        <el-table-column prop="ip" label="ip" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('demo:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        :current-page="page"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="limit"
        :total="total"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="pageSizeChangeHandle"
        @current-change="pageCurrentChangeHandle">
      </el-pagination>
      <!-- 弹窗, 新增 / 修改 -->
      <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './user-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/sys/tbUser/page',
        getDataListIsPage: true,
        exportURL: '/sys/tbUser/export',
        deleteURL: '/sys/tbUser',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
      
    }
  },
  components: {
    AddOrUpdate
  }
  
}
</script>
