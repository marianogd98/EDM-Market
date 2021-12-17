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
        <el-tooltip content="Crear Comparativa" effect="dark" placement="bottom">
          <el-button class="text-right" type="primary" @click="handleCreate">
            <svg-icon icon-class="edit" />
          </el-button>
        </el-tooltip>
        <el-tooltip content="Cargar Lista" effect="dark" placement="bottom">
          <el-button class="text-right" type="danger" icon="el-icon-upload2" @click="handleUpload" />
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
        <el-select v-model="listQuery.compd" placeholder="Competencia" clearable class="filter-item" style="width: 130px">
          <el-option v-for="item in everyCompetences" :key="item.text" :label="item.text" :value="item.text" />
        </el-select>
        <el-select v-model="listQuery.depId" placeholder="Departamento" clearable class="filter-item" style="width: 130px">
          <el-option v-for="item in everyDepartaments" :key="item.text" :label="item.text" :value="item.text" />
        </el-select>
        <el-select v-model="listQuery.groupId" placeholder="Grupo" clearable class="filter-item" style="width: 130px">
          <el-option v-for="item in everyGroups" :key="item.text" :label="item.text" :value="item.text" />
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
            <el-tag type="info">{{ row.detail.substring(0, 15) }}</el-tag>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column label="Estado" min-width="90px">
        <template slot-scope="{row}">
          <span>{{ row.state }} </span>
        </template>
      </el-table-column>
      <el-table-column label="Ciudad" min-width="100px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.city }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Departamento" min-width="100px">
        <template slot-scope="{row}">
          <span>{{ row.department }} </span>
        </template>
      </el-table-column>
      <el-table-column label="Grupo" min-width="110px" align="center">
        <template slot-scope="{row}">
          <el-tooltip :content="row.name">
            <span>{{ row.name.substring(0, 12) }}</span>
          </el-tooltip>
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
      <!-- <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c" type="danger">{{ row.competence }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c" type="info">Empate</el-tag>
        </template>
      </el-table-column> -->
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
      <!-- <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c2" type="danger">{{ row.competence2 }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c2" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c2" type="info">Empate</el-tag>
        </template>
      </el-table-column> -->
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
      <!-- <el-table-column label="Ganador" min-width="70px" align="center">
        <template slot-scope="{row}">
          <el-tag v-if="row.price_rio>row.price_c3" type="danger">{{ row.competence3 }}</el-tag>
          <el-tag v-if="row.price_rio<row.price_c3" type="success">Rio</el-tag>
          <el-tag v-if="row.price_rio==row.price_c3" type="info">Empate</el-tag>
        </template>
      </el-table-column> -->
      <el-table-column label="Fecha" class-name="status-col" min-width="100">
        <template slot-scope="{row}">
          <span>{{ row.date }}</span>
        </template>
      </el-table-column>
      <!-- <el-table-column label="Acciones" align="center" width="230" class-name="small-padding fixed-width">
        <template slot-scope="{row,$index}">
          <el-tooltip content="Editar" effect="dark" placement="bottom">
            <el-button type="primary" icon="el-icon-edit" size="mini" @click="handleUpdate(row)" />
          </el-tooltip>
          <el-tooltip content="Eliminar" effect="dark" placement="bottom">
            <el-button v-if="row.status!='Eliminado'" icon="el-icon-delete" size="mini" type="danger" @click="handleDelete(row, $index)" />
          </el-tooltip>
        </template>
      </el-table-column> -->
    </el-table>

    <pagination v-show="totalC>0" :total="totalC" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getDataCompetence" />

    <el-dialog :title="textMap[dialogStatus]" type="primary" :visible.sync="dialogFormVisible">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="left" label-width="180px" style="width: 50em; margin-left:50px;">
        <el-form-item label="Codigo SAP:" prop="codId">
          <el-input v-model="temp.codId" placeholder="Por favor, ingresar codigo" />
        </el-form-item>
        <el-form-item label="Producto:" prop="prodId">
          <el-input v-model="temp.prodId" placeholder="Por favor, ingresar descripcion" />
        </el-form-item>
        <el-form-item label="Estado:" prop="stateId">
          <el-select v-model="temp.stateId" class="filter-item" placeholder="Por favor, seleccionar estado">
            <el-option v-for="item in everyStates" :key="item.id" :label="item.text" :value="item.id" />
          </el-select>
        </el-form-item>
        <el-form-item label="Ciudad:" prop="cityId">
          <el-select v-model="temp.cityId" class="filter-item" placeholder="Por favor, seleccionar ciudad">
            <el-option v-for="item in everyCities" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
        </el-form-item>
        <!-- <el-form-item label="Competencia:" prop="compId">
          <el-drag-select v-model="temp.compId" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyCompetences" :key="item.text" :label="item.text" :value="item.text" />
          </el-drag-select>
        </el-form-item> -->
        <el-form-item label="Departamento:" prop="depId">
          <el-select v-model="temp.depId" placeholder="Por favor, seleccionar departamento">
            <el-option v-for="item in everyDepartaments" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
        </el-form-item>
        <el-form-item label="Grupo:" prop="groupId">
          <el-select v-model="temp.groupId" placeholder="Por favor, seleccionar grupo">
            <el-option v-for="item in everyGroups" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
        </el-form-item>
        <el-form-item label="Precio Rio:" prop="prerId">
          <el-input v-model="temp.prerId" placeholder="Por favor, ingresar precio" />
        </el-form-item>
        <el-form-item label="Datos Competencia I:" prop="comp1Id">
          <el-select v-model="temp.comp1Id" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyCompetences" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
          <el-input v-model="temp.precId" placeholder="Por favor, ingresar precio" style="width: 50% !important;padding-left:10px" />
        </el-form-item>
        <el-form-item label="Datos Competencia III:" prop="comp2Id">
          <el-select v-model="temp.comp2Id" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyCompetences" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
          <el-input v-model="temp.prec2Id" placeholder="Por favor, ingresar precio" style="width: 50% !important;padding-left:10px" />
        </el-form-item>
        <!-- <el-form-item label="Competencia I:" prop="groupId">
        </el-form-item> -->
        <el-form-item label="Datos Competencia III:" prop="comp3Id">
          <el-select v-model="temp.comp3Id" placeholder="Por favor, seleccionar">
            <el-option v-for="item in everyCompetences" :key="item.text" :label="item.text" :value="item.id" />
          </el-select>
          <el-input v-model="temp.prec3Id" placeholder="Por favor, ingresar precio" style="width: 50% !important;padding-left:10px" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="danger" icon="el-icon-error" @click="dialogFormVisible = false">
          Cancelar
        </el-button>
        <el-button type="success" icon="el-icon-success" @click="createProduct()">
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
        Inactivo: 'info',
        Eliminado: 'danger'
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
      Products: null,
      Competences: [],
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 20,
        codId: undefined,
        prodId: undefined,
        stateId: undefined,
        cityId: undefined,
        comp1Id: undefined,
        precId: undefined,
        comp2Id: undefined,
        prec2Id: undefined,
        comp3Id: undefined,
        prec3Id: undefined,
        statId: undefined,
        depId: undefined,
        groupId: undefined,
        sort: '+id'
      },
      States: [],
      Cities: [],
      Dpts: [],
      Status: [],
      Groups: [],
      temp: {
        id: undefined,
        codId: '',
        prodId: '',
        stateId: '',
        cityId: '',
        statId: '',
        comp1Id: '',
        depId: '',
        prer: '',
        precId: '',
        comp2Id: '',
        prec2Id: '',
        comp3Id: '',
        prec3Id: ''
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: 'Editar producto',
        create: 'Datos producto'
      },
      rules: {
        stateId: [{ required: true, message: 'estado es requerido', trigger: 'change' }],
        cityId: [{ required: true, message: 'ciudad es requerido', trigger: 'change' }],
        codId: [{ required: true, message: 'codigo es requerido', trigger: 'blur' }],
        prodId: [{ required: true, message: 'descripcion es requerido', trigger: 'blur' }],
        statId: [{ required: true, message: 'estatus es requerido', trigger: 'change' }],
        depId: [{ required: true, message: 'departamento es requerido', trigger: 'change' }],
        groupId: [{ required: true, message: 'grupo es requerido', trigger: 'change' }],
        prerId: [{ required: true, message: 'precio rio es requerido', trigger: 'blur' }],
        comp1Id: [{ required: true, message: 'datos competencia es requerido', trigger: 'change' }],
        comp2Id: [{ required: true, message: 'datos competencia es requerido', trigger: 'change' }],
        comp3Id: [{ required: true, message: 'datos competencia es requerido', trigger: 'change' }]
      },
      //   value: [],
      options: [],
      downloadLoading: false
    }
  },
  computed: {
    everyDepartaments: function() {
      const total = []
      let id = 1
      Object.entries(this.Dpts).forEach(([key, val]) => {
        total.push({ id: id, text: val.departamento }) // the value of the current key.
        id++
      })
      return total
    },
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
    },
    everyGroups: function() {
      const total = []
      let id = 1
      Object.entries(this.Groups).forEach(([key, val]) => {
        total.push({ id: id, text: val.name }) // the value of the current key.
        id++
      })
      return total
    },
    everyCompetences: function() {
      const total = []
      let id = 1
      Object.entries(this.Competences).forEach(([key, val]) => {
        total.push({ id: id, text: val.name }) // the value of the current key.
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
    this.getDataProduct()
    this.getStates()
    this.getCities()
    this.getStatus()
    this.getDepartment()
    this.getGroups()
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
    handleDelete(row, index) {
      this.$notify({
        title: 'Exito',
        message: 'Eliminado sastifactoriamente',
        type: 'success',
        duration: 2000
      })
      this.Competences.splice(index, 1)
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
    async getDepartment() {
      await axios.get('http://172.50.0.22:8302/api/Inside/ventas/departamento/inside?tiendaId=3&FechaIni=2021-11-01&FechaFin=2021-11-01')
        .then(({ data }) => {
          console.log(data)
          const dptsRio = []
          this.Dpts = data

          data.forEach(element => {
            dptsRio.push(element.departamento)
          })
        }).catch(e => {
          console.log(e)
        })
    },
    async getGroups() {
      await axios.get('http://172.50.3.62:5000/api/group')
        .then(({ data }) => {
          console.log(data)
          const groupRio = []
          this.Groups = data
          data.forEach(element => {
            groupRio.push(element.name)
          })
        }).catch(e => {
          console.log(e)
        })
    },
    async createProduct() {
      const data = {
        cod: this.temp.codId,
        product: this.temp.prodId,
        state: this.temp.stateId,
        city: this.temp.cityId,
        dept: this.temp.depId,
        group: this.temp.groupId,
        prer: this.temp.prerId,
        comp: this.temp.comp1Id,
        prec: this.temp.precId,
        comp2: this.temp.comp2Id,
        prec2: this.temp.prec2Id,
        comp3: this.temp.comp3Id,
        prec3: this.temp.prec3Id
      }
      console.log(data)
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          axios.post('http://172.50.3.62:5000/api/product', data)
            .then(response => {
              this.resetTemp()
              this.temp.id
              this.temp.codId = ''
              this.temp.prodId = ''
              this.temp.stateId = ''
              this.temp.cityId = ''
              this.temp.depId = ''
              this.temp.groupId = ''
              this.temp.prerId = ''
              this.temp.comp1Id = ''
              this.temp.precId = ''
              this.temp.comp2Id = ''
              this.temp.prec2Id = ''
              this.temp.comp3Id = ''
              this.temp.prec3Id = ''
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
          this.Products.push(data)
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
      this.temp = {
        id: undefined,
        codId: '',
        prodId: '',
        stateId: '',
        cityId: '',
        depId: '',
        groupId: '',
        prerId: '',
        comp1Id: '',
        precId: '',
        comp2Id: '',
        prec2Id: '',
        comp31Id: '',
        prec3Id: ''
      }
    },
    handleCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    handleUpload() {
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
