<template>
  <div class="app-container">
    <el-card class="box-card">
      <div slot="header">
        <a class="link-type link-title" target="_blank" href="https://panjiachen.github.io/vue-element-admin-site/guide/advanced/theme.html">
          Registro
        </a>
        <el-tooltip content="Abrir Filtro" effect="dark" placement="bottom">
          <el-button type="default" icon="el-icon-s-tools" />
        </el-tooltip>
        <el-tooltip content="Crear Competencia" effect="dark" placement="bottom">
          <el-button class="text-right" type="primary" @click="handleCreate">
            <svg-icon icon-class="edit" />
          </el-button>
        </el-tooltip>
        <el-tooltip content="Exportar Excel" effect="dark" placement="bottom">
          <el-button v-waves :loading="downloadLoading" class="text-right" type="success" @click="handleDownload">
            <svg-icon icon-class="excel" />
          </el-button>
        </el-tooltip>
      </div>
      <div class="box-item">
        <el-select v-model="listQuery.stateId" placeholder="Estado" clearable style="width: 90px" class="filter-item">
          <el-option v-for="item in everyStates" :key="item.text" :label="item.text" :value="item.text" />
        </el-select>
        <el-select v-model="listQuery.cityId" placeholder="Ciudad" clearable class="filter-item" style="width: 130px">
          <el-option v-for="item in everyCities" :key="item.text" :label="item.text" :value="item.text" />
        </el-select>
        <el-input v-model="listQuery.compId" placeholder="Competencia" style="width: 200px;" class="filter-item" @keyup.enter.native="handleFilter" />
        <el-select v-model="listQuery.statId" placeholder="Estatus" clearable class="filter-item" style="width: 130px">
          <el-option v-for="item in everyStatus" :key="item.text" :label="item.text" :value="item.text" />
        </el-select>
        <el-select v-model="listQuery.sort" style="width: 140px" class="filter-item" @change="handleFilter">
          <el-option v-for="item in sortOptions" :key="item.key" :label="item.label" :value="item.key" />
        </el-select>
        <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
          Buscar
        </el-button>
      </div>
    </el-card>

    <el-table
      :key="tableKey"
      v-loading="listLoading"
      :data="Competences"
      border
      fit
      highlight-current-row
      style="width: 100%;"
      @sort-change="sortChange"
    >
      <el-table-column label="ID" prop="id" sortable="custom" align="center" width="80" :class-name="getSortClass('id')">
        <template slot-scope="{row}">
          <span>{{ row.id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Estado" width="150px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.state }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Ciudad" min-width="150px">
        <template slot-scope="{row}">
          <span class="link-type" @click="handleUpdate(row)">{{ row.city }} </span>
          <el-tag>{{ row.name }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Competencia" min-width="110px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.name }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Estatus" class-name="status-col" width="100">
        <template slot-scope="{row}">
          <el-tag :type="row.status | statusFilter">
            {{ row.status }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="Fecha" class-name="status-col" min-width="100">
        <template slot-scope="{row}">
          <span>{{ row.date }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Acciones" align="center" width="230" class-name="small-padding fixed-width">
        <template slot-scope="{row,$index}">
          <el-tooltip content="Editar" effect="dark" placement="bottom">
            <el-button type="primary" icon="el-icon-edit" size="mini" @click="handleUpdate(row)" />
          </el-tooltip>
          <el-tooltip content="Eliminar" effect="dark" placement="bottom">
            <el-button icon="el-icon-delete" size="mini" type="danger" @click="handleDelete(row,$index)" />
          </el-tooltip>
          <el-button v-if="row.status!='Activo'" size="mini" type="success" @click="handleModifyStatus(row,'Activo')">
            Activo
          </el-button>
          <el-button v-if="row.status!='Inactivo'" size="mini" @click="handleModifyStatus(row,'Inactivo')">
            Inactivo
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="totalC>0" :total="totalC" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getDataCompetence" />

    <el-dialog :title="textMap[dialogStatus]" type="primary" :visible.sync="dialogFormVisible">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="left" label-width="180px" style="width: 50em; margin-left:50px;">
        <el-form-item label="Estado:" prop="stateId">
          <el-select v-model="temp.stateId" class="filter-item" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyStates" :key="item.id" :label="item.text" :value="item.text" />
          </el-select>
        </el-form-item>
        <el-form-item label="Ciudad:" prop="cityId">
          <el-select v-model="temp.cityId" class="filter-item" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyCities" :key="item.text" :label="item.text" :value="item.text" />
          </el-select>
        </el-form-item>
        <el-form-item label="Nombre competencia:" prop="compId">
          <el-input v-model="temp.compId" placeholder="Por favor, ingresar" />
        </el-form-item>
        <!-- <el-form-item label="Departamento:">
          <el-drag-select v-model="value" multiple placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyDepartaments" :key="item.text" :label="item.text" :value="item.text" />
          </el-drag-select>
        </el-form-item> -->
        <el-form-item label="Estatus:" prop="statId">
          <el-select v-model="temp.statId" class="filter-item" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyStatus" :key="item.text" :label="item.text" :value="item.text" />
          </el-select>
        </el-form-item>
        <el-form-item label="Observación:">
          <i class="ml--3">(Opcional)</i>
          <el-input v-model="temp.noteId" :autosize="{ minRows: 2, maxRows: 4}" type="textarea" placeholder="Por favor, ingresar" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="danger" icon="el-icon-error" @click="dialogFormVisible = false">
          Cancelar
        </el-button>
        <el-button type="success" icon="el-icon-success" @click="createCompetence()">
          Confirmar
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>

// import ElDragSelect from '@/components/DragSelect'
import { toggleClass } from '@/utils'
import axios from 'axios'
import waves from '@/directive/waves'
import { parseTime } from '@/utils'
import '@/assets/custom-theme/index.css' // the theme changed version element-ui css
import Pagination from '@/components/Pagination'

const calendarTypeOptions = [
  { key: 'CN', display_name: 'Nueva Esparta' },
  { key: 'US', display_name: 'Sucre' },
  { key: 'JP', display_name: 'Monagas' }
]

const calendarTypeKeyValue = calendarTypeOptions.reduce((acc, cur) => {
  acc[cur.key] = cur.display_name
  return acc
}, {})

export default {
  name: 'Theme',
  components: { Pagination },
  directives: { waves },
  filters: {
    statusFilter(status) {
      const statusMap = {
        Activo: 'success',
        Inactivo: 'info'
      }
      return statusMap[status]
    },
    typeFilter(type) {
      return calendarTypeKeyValue[type]
    }
  },
  //   components: { ElDragSelect },
  data() {
    return {
      theme: false,
      tags: [
        { name: 'Tag One', type: '' },
        { name: 'Tag Two', type: 'info' },
        { name: 'Tag Three', type: 'success' },
        { name: 'Tag Four', type: 'warning' },
        { name: 'Tag Five', type: 'danger' }
      ],
      totalC: 0,
      tableKey: 0,
      slideValue: 50,
      sortOptions: [{ label: 'ID Primero', key: '+id' }, { label: 'ID Ultimo', key: '-id' }],
      radio: 3,
      Competences: [],
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 20,
        stateId: undefined,
        cityId: undefined,
        compId: undefined,
        statId: undefined,
        sort: '+id'
      },
      States: [],
      Cities: [],
      //   Dpts: [],
      Status: [],
      temp: {
        stateId: '',
        cityId: '',
        statId: '',
        compId: '',
        noteId: ''
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: 'Editar competencia',
        create: 'Datos competencia'
      },
      rules: {
        stateId: [{ required: true, message: 'estado es requerido', trigger: 'change' }],
        cityId: [{ required: true, message: 'ciudad es requerido', trigger: 'change' }],
        compId: [{ required: true, message: 'nombre es requerido', trigger: 'blur' }],
        statId: [{ required: true, message: 'estatus es requerido', trigger: 'change' }]
      },
      //   value: [],
      options: [],
      downloadLoading: false
    }
  },
  computed: {
    // everyDepartaments: function() {
    //   const total = []
    //   let id = 1
    //   Object.entries(this.Dpts).forEach(([key, val]) => {
    //     total.push({ id: id, text: val.departamento }) // the value of the current key.
    //     id++
    //   })
    //   return total
    // },
    everyStates: function() {
      const total = []
      let id = 1
      Object.entries(this.States).forEach(([key, val]) => {
        total.push({ id: id, text: val.state }) // the value of the current key.
        id++
      })
      return total
    },
    everyCities: function() {
      const total = []
      let id = 1
      Object.entries(this.Cities).forEach(([key, val]) => {
        total.push({ id: id, text: val.city }) // the value of the current key.
        id++
      })
      return total
    },
    everyStatus: function() {
      const total = []
      let id = 1
      Object.entries(this.Status).forEach(([key, val]) => {
        total.push({ id: id, text: val.current_status }) // the value of the current key.
        id++
      })
      return total
    }
  },
  watch: {
    theme() {
      toggleClass(document.body, 'custom-theme')
    }
  },
  created() {
    this.getDataCompetence()
    this.getStates()
    this.getCities()
    this.getStatus()
  },
  methods: {
    handleFilter() {
      this.listQuery.page = 1
      this.getDataCompetence()
    },
    sortChange(data) {
      const { prop, order } = data
      if (prop === 'id') {
        this.sortByID(order)
      }
    },
    sortByID(order) {
      if (order === 'ascending') {
        this.listQuery.sort = '+id'
      } else {
        this.listQuery.sort = '-id'
      }
      this.handleFilter()
    },
    handleUpdate(row) {
      this.temp = Object.assign({}, row) // copy obj
      console.log(this.temp)
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    handleDelete(row, $index) {
      this.$notify({
        title: 'Exito',
        message: 'Eliminado sastifactoriamente',
        type: 'success',
        duration: 2000
      })
      this.Competences.splice(row, $index)
    },
    handleModifyStatus(row, status) {
      this.$message({
        message: 'Estatus cambiado',
        type: 'success'
      })
      row.status = status
    },
    async getDataCompetence() {
      const total = []
      this.listLoading = true
      await axios.get('http://172.50.3.62:5000/api/competence')
        .then(({ data }) => {
          console.log(data)
          this.Competences = data
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
    },
    async getStates() {
      await axios.get('http://172.50.3.62:5000/api/states')
        .then(({ data }) => {
          console.log(data)
          const statesRio = []
          this.States = data
          data.forEach(element => {
            statesRio.push(element.state)
          })
        }).catch(e => {
          console.log(e)
        })
    },
    async getCities() {
      await axios.get('http://172.50.3.62:5000/api/cities')
        .then(({ data }) => {
          console.log(data)
          const citiesRio = []
          this.Cities = data
          data.forEach(element => {
            citiesRio.push(element.city)
          })
        }).catch(e => {
          console.log(e)
        })
    },
    async getStatus() {
      await axios.get('http://172.50.3.62:5000/api/status')
        .then(({ data }) => {
          console.log(data)
          const statusRio = []
          this.Status = data
          data.forEach(element => {
            statusRio.push(element.current_status)
          })
        }).catch(e => {
          console.log(e)
        })
    },
    // async getDepartment() {
    //   await axios.get('http://172.50.0.22:8302/api/Inside/ventas/departamento/inside?tiendaId=3&FechaIni=2021-11-01&FechaFin=2021-11-01')
    //     .then(({ data }) => {
    //       console.log(data)
    //       const dptsRio = []
    //       this.Dpts = data

    //       data.forEach(element => {
    //         dptsRio.push(element.departamento)
    //       })
    //     }).catch(e => {
    //       console.log(e)
    //     })
    // },
    async createCompetence() {
      const data = {
        state: this.temp.stateId,
        city: this.temp.cityId,
        name: this.temp.compId,
        // departament: this.value,
        status: this.temp.statId,
        note: this.temp.noteId
      }
      console.log(data)
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          axios.post('http://172.50.3.62:5000/api/competence', data)
            .then(response => {
              this.resetTemp()
              this.temp.stateId = ''
              this.temp.cityId = ''
              this.temp.compId = ''
              this.temp.statId = ''
              this.temp.noteId = ''
            })
            .catch(e => {
            /* si el token es invalido o esta vencido */
              if (e.response.status === 401) {
                this.$notify({
                  title: 'Error',
                  message: 'Un problema al crear el registro',
                  type: 'danger',
                  duration: 2000
                })
                this.$gate.removeLogin()
                this.$router.push('/Login')
              }
              if (e.response.data) {
                console.log('Enviado correctamente')
              }
            })
          this.dialogFormVisible = false
          this.Competences.push(data)
          this.$notify({
            title: 'Exito',
            message: 'Creado sastifactoriamente',
            type: 'success',
            duration: 2000
          })
        }
      })
    },
    resetTemp() {
      this.temp.stateId = ''
      this.temp.cityId = ''
      this.temp.compId = ''
      //   this.value = []
      this.temp.statId = ''
      this.temp.noteId = ''
    },
    handleCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    handleDownload() {
      const dt = new Date()
      const publish = '_' + dt.getDate().toString().padStart(2, '0') + '_' + (dt.getMonth() + 1).toString().padStart(2, '0') + '_' + dt.getFullYear().toString().padStart(4, '0') + '_' + dt.getHours().toString().padStart(2, '0') + dt.getMinutes().toString().padStart(2, '0') + dt.getSeconds().toString().padStart(2, '0')
      this.downloadLoading = true
      import('@/vendor/Export2Excel').then(excel => {
        const tHeader = ['id', 'state', 'city', 'name', 'status', 'date']
        const filterVal = ['id', 'state', 'city', 'name', 'status', 'date']
        const data = this.formatJson(filterVal)
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: 'Lista Competencia' + publish
        })
        this.downloadLoading = false
      })
    },
    formatJson(filterVal) {
      return this.Competences.map(v => filterVal.map(j => {
        if (j === 'timestamp') {
          return parseTime(v[j])
        } else {
          return v[j]
        }
      }))
    },
    getSortClass: function(key) {
      const sort = this.listQuery.sort
      return sort === `+${key}` ? 'ascending' : 'descending'
    }
  }
}
</script>

<style scoped>

.ml--3{
    margin-right: -4.6em !important;
}

.field-label{
  vertical-align: middle;
}
.box-card {
  width: 70em;
  max-width: 100%;
  margin: 20px auto;
}

.block {
  padding: 30px 24px;
}

.tag-item {
  margin-right: 15px;
}

.text-right{
    text-align: right !important;
}

.el-dialog__headerbtn .el-dialog__close {
    color: white !important;
}

.el-dialog__header {
    background-color: #1890ff !important;
}

.el-dialog__title {
    color: white !important;
}

</style>
