<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-gptKey__gptkey}">
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
        <el-form-item>
          <el-button v-if="$hasPermission('gptKey:gptkey:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('gptKey:gptkey:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="apiKey" label="GPT的KEY" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="状态" header-align="center" align="center">
          <template slot-scope="scope">
              <el-tag v-if="scope.row.status == 0" type="success">正常</el-tag>
              <el-tag v-if="scope.row.status == 1" type="danger">关闭</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="remarks" label="备注" header-align="center" align="center"></el-table-column>
        <el-table-column label="余额" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <!-- <el-button  type="primary" size="small" v-loading.fullscreen.lock="fullscreenLoading" @click="queryGrants(scope.row.id)">点击查询</el-button> -->
            <el-button
              type="primary"
              @click="queryGrants(scope.row.id)"
              v-loading.fullscreen.lock="fullscreenLoading">
              点击查询
            </el-button>
          </template>
        </el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('gptKey:gptkey:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('gptKey:gptkey:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './gptkey-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      fullscreenLoading: false,
      mixinViewModuleOptions: {
        getDataListURL: '/gptKey/gptkey/page',
        getDataListIsPage: true,
        exportURL: '/gptKey/gptkey/export',
        deleteURL: '/gptKey/gptkey',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  methods: {
    queryGrants (value) {
        this.fullscreenLoading = true;
        this.$http.get(`/gptKey/gptkey/queryBalance/`+value).then(({ data: res }) => {
        this.fullscreenLoading = false;
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        console.log(res)
        var con =  "用户名:"+res.data.accountName+
        "</br>账户总余额:"+res.data.hardLimitUsd+"美元"+
        "</br>已使用的金额:"+res.data.totalUsage+"美元"+
        "</br>大约剩余金额:"+res.data.balance+"美元";
        this.$alert(con, '余额信息', {
          dangerouslyUseHTMLString: true
        });
      }).catch(() => {})
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
