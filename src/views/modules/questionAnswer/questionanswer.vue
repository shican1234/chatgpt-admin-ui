<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-questionAnswer__questionanswer}">
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
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" @sort-change="dataListSortChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userId" label="用户id" header-align="center" align="center"></el-table-column>
        <el-table-column prop="nickName" label="用户昵称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="question" label="问题" :show-overflow-tooltip="true" header-align="center" align="center"></el-table-column>
        <el-table-column prop="answer" label="AI的回答" :show-overflow-tooltip="true" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="产生时间" sortable="custom" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
              <el-button v-if="$hasPermission('questionAnswer:questionanswer:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/questionAnswer/questionanswer/page',
        getDataListIsPage: true,
        exportURL: '/questionAnswer/questionanswer/export',
        deleteURL: '/questionAnswer/questionanswer',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  }
}
</script>
