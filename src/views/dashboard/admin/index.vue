<template>
  <div class="dashboard-editor-container">

    <el-table
      :key="tableKey"
      v-loading="listLoading"
      :data="Products"
      border
      fit
      highlight-current-row
      style="width: 100%;"
      @sort-change="sortChange"
    >
      <el-table-column label="COD SAP" width="90px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.cod_sap }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Producto" width="150px" align="center">
        <template slot-scope="{row}">
          <el-tooltip :content="row.detail">
            <el-tag type="info">{{ row.detail.substring(0, 12) }}</el-tag>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column label="Estado" min-width="80px">
        <template slot-scope="{row}">
          <span>{{ row.state }} </span>
        </template>
      </el-table-column>
      <el-table-column label="Ciudad" min-width="100px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.city }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Precio" min-width="50px">
        <template slot-scope="{row}">
          <span class="link-type" @click="handleUpdate(row)">{{ row.price_rio }}$</span>
        </template>
      </el-table-column>
      <el-table-column label="Precio Comp. I" min-width="110px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.price_c }}$ </span>
          <el-tag>{{ row.competence }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Diferencia" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tooltip content="Sobre el precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio>row.price_c" type="danger">{{ Math.abs((((row.price_rio-row.price_c)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="Debajo del precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio<row.price_c" type="success">{{ Math.abs((((row.price_rio-row.price_c)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="No hay diferencia" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio==row.price_c" type="info">{{ Math.abs((((row.price_rio-row.price_c)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c" type="danger">{{ row.competence }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c" type="info">Empate</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Precio Comp. II" min-width="110px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.price_c2 }}$ </span>
          <el-tag>{{ row.competence2 }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Diferencia" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tooltip content="Sobre el precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio>row.price_c2" type="danger">{{ Math.abs((((row.price_rio-row.price_c2)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="Debajo del precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio<row.price_c2" type="success">{{ Math.abs((((row.price_rio-row.price_c2)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="No hay diferencia" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio==row.price_c2" type="info">{{ Math.abs((((row.price_rio-row.price_c2)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c2" type="danger">{{ row.competence2 }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c2" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c2" type="info">Empate</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Precio Comp. III" min-width="110px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.price_c3 }}$ </span>
          <el-tag>{{ row.competence3 }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Diferencia" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tooltip content="Sobre el precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio>row.price_c3" type="danger">{{ Math.abs((((row.price_rio-row.price_c3)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="Debajo del precio" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio<row.price_c3" type="success">{{ Math.abs((((row.price_rio-row.price_c3)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
          <el-tooltip content="No hay diferencia" effect="dark" placement="bottom">
            <el-tag v-if="row.price_rio==row.price_c3" type="info">{{ Math.abs((((row.price_rio-row.price_c3)/row.price_rio)*100).toFixed(2)) }}%</el-tag>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c3" type="danger">{{ row.competence3 }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c3" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c3" type="info">Empate</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Fecha" class-name="status-col" min-width="70">
        <template slot-scope="{row}">
          <span>{{ row.date }}</span>
        </template>
      </el-table-column>
    </el-table>

    <panel-group @handleSetLineChartData="handleSetLineChartData" />

    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
      <line-chart :chart-data="lineChartData" />
    </el-row>

    <el-row :gutter="32">
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <raddar-chart />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <pie-chart />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <bar-chart />
        </div>
      </el-col>
    </el-row>

    <el-row :gutter="8">
      <el-col :xs="{span: 24}" :sm="{span: 24}" :md="{span: 24}" :lg="{span: 12}" :xl="{span: 12}" style="padding-right:8px;margin-bottom:30px;">
        <transaction-table />
      </el-col>
      <el-col :xs="{span: 24}" :sm="{span: 12}" :md="{span: 12}" :lg="{span: 6}" :xl="{span: 6}" style="margin-bottom:30px;">
        <todo-list />
      </el-col>
      <el-col :xs="{span: 24}" :sm="{span: 12}" :md="{span: 12}" :lg="{span: 6}" :xl="{span: 6}" style="margin-bottom:30px;">
        <box-card />
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
import PanelGroup from './components/PanelGroup'
import LineChart from './components/LineChart'
import RaddarChart from './components/RaddarChart'
import PieChart from './components/PieChart'
import BarChart from './components/BarChart'
import TransactionTable from './components/TransactionTable'
import TodoList from './components/TodoList'
import BoxCard from './components/BoxCard'

const lineChartData = {
  newVisitis: {
    expectedData: [100, 120, 161, 134, 105, 160, 165],
    actualData: [120, 82, 91, 154, 162, 140, 145]
  },
  messages: {
    expectedData: [200, 192, 120, 144, 160, 130, 140],
    actualData: [180, 160, 151, 106, 145, 150, 130]
  },
  purchases: {
    expectedData: [80, 100, 121, 104, 105, 90, 100],
    actualData: [120, 90, 100, 138, 142, 130, 130]
  },
  shoppings: {
    expectedData: [130, 140, 141, 142, 145, 150, 160],
    actualData: [120, 82, 91, 154, 162, 140, 130]
  }
}

export default {
  name: 'DashboardAdmin',
  components: {
    PanelGroup,
    LineChart,
    RaddarChart,
    PieChart,
    BarChart,
    TransactionTable,
    TodoList,
    BoxCard
  },
  data() {
    return {
      lineChartData: lineChartData.newVisitis,
      Products: [],
      listLoading: true
    }
  },
  created() {
    this.getDataProduct()
  },
  methods: {
    sortChange(data) {
      const { prop, order } = data
      if (prop === 'id') {
        this.sortByID(order)
      }
    },
    handleSetLineChartData(type) {
      this.lineChartData = lineChartData[type]
    },
    async getDataProduct() {
      const total = []
      this.listLoading = true
      await axios.get('http://172.50.3.62:5000/api/product')
        .then(({ data }) => {
          console.log(data)
          this.Products = data
          Object.entries(this.totalC).forEach(([key, data]) => {
            total.push(data.id)
            console.log(data.id)
          })

          return total
        }).catch(e => {
          console.log(e)
        })
      setTimeout(() => {
        this.listLoading = false
      }, 1.5 * 1000)
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width:1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
</style>
