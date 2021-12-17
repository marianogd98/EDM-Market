<template>
  <div class="app-container">
    <div class="filter-container">
      <el-checkbox-group v-model="checkboxVal">
        <el-checkbox label="sigo">
          Sigo
        </el-checkbox>
        <el-checkbox label="rattan">
          Rattan
        </el-checkbox>
        <el-checkbox label="nova">
          Nova
        </el-checkbox>
        <el-checkbox label="sun">
          Sun Market
        </el-checkbox>
      </el-checkbox-group>
    </div>

    <el-table :key="key" :data="tableData" border fit highlight-current-row style="width: 100%">
      <el-table-column prop="name" label="PrecioRio" width="180" />
      <el-table-column v-for="fruit in formThead" :key="fruit" :label="fruit">
        <template slot-scope="scope">
          {{ scope.row[fruit] }}
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
const defaultFormThead = ['sigo', 'rattan', 'nova', 'sun']

export default {
  data() {
    return {
      tableData: [
        {
          name: 'precio-1',
          sigo: 'sigo-10',
          rattan: 'rattan-10',
          nova: 'nova-10',
          sun: 'sun-10'
        },
        {
          name: 'precio-2',
          sigo: 'sigo-10',
          rattan: 'rattan-10',
          nova: 'nova-10',
          sun: 'sun-10'
        }
      ],
      key: 1, // table key
      formTheadOptions: ['sigo', 'rattan', 'nova', 'sun'],
      checkboxVal: defaultFormThead, // checkboxVal
      formThead: defaultFormThead // 默认表头 Default header
    }
  },
  watch: {
    checkboxVal(valArr) {
      this.formThead = this.formTheadOptions.filter(i => valArr.indexOf(i) >= 0)
      this.key = this.key + 1// 为了保证table 每次都会重渲 In order to ensure the table will be re-rendered each time
    }
  }
}
</script>

