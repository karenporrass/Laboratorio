<template>
  <div >
    <q-layout view="lHh lpr lFf" class="shadow-2 rounded-borders " >
      <q-header reveal elevated class="bg-orange-10">
        <q-toolbar>
          <q-toolbar-title>EMPRESA</q-toolbar-title>
        </q-toolbar>
      </q-header>

      
      <q-page-container>
        <q-page class="q-pa-md">
          <div class="row q-mt-md">
            <div class="col-1"></div>
            <div class="col-10  text-center">
                <div style="font-size:xx-large;" class="text-weight-bolder"> NOMINA </div>
            </div>
            <div class="col-1"></div>
        </div>
        <q-separator class="q-my-md  bg-orange-5" style="height: 2px; margin-left: 100px; margin-right: 100px;" />
        <div class="row ">
            <div class="col-1"></div>
            <div class="col-10 ">
                <q-btn class="bg-orange-8 text-white" @click="prompt = true">Crear nuevo usuario</q-btn>
            </div>
            <div class="col-1"></div>
        </div>
        <!-- TABLE INFO -->
       <div class="row q-mt-md">
            <div class="col-1"></div>
            <div class="col-10 ">
                <q-table style="height: 400px" flat bordered  :rows="rows" :columns="columns" row-key="index"
                    virtual-scroll v-model:pagination = "pagination"  :rows-per-page-options="[0]" />
            </div>
            <div class="col-1"></div>
        </div> 

        <q-dialog v-model="prompt">
            <q-card >
              <q-card-section class="bg-orange-10">
                <h5 class="q-mt-sm q-mb-sm text-white text-center text-weight-bold">
                  DILIGENCIA LA INFORMACIÓN
                </h5>
              </q-card-section>
              <div class="q-pa-md " >
                <pre class="bg-red-7 text-white">{{ alert }}</pre>
                <div>
                  <q-input class="q-mb-md"  filled type="text" v-model="name" label="Digite el nombre"></q-input>
                  <q-select  class="q-mb-md" filled v-model="selectCategory" :options="optionsCategory" label="Seleccione la categoria" />
                  <q-input   filled type="number" v-model="hoursTotal" label="Digite las horas laboradas"></q-input>
                  <div>
                    <br />
                    <q-btn  label="guardar" class="text-white bg-orange-8" @click="validateInfo()" />
                    <q-btn class="q-ml-md" label="cerrar" v-close-popup />
                  </div>
                </div>
              </div>
            </q-card>
          </q-dialog>
        </q-page>
      </q-page-container>
    </q-layout>
  </div>
</template>
<script setup>
import {ref} from 'vue'

let prompt = ref(false)
let selectCategory = ref()
let optionsCategory = ref([
{label:"1", value:12000},
{label:"2", value:17000},
{label:"3", value:22000}
])

let name = ref("")
let hours = ref()
let hoursTotal= ref()
let extras = ref(0)
let hoursExtras = ref(0)
let pay = ref()
let total = ref()
let alert = ref()


let pagination = ref({
        rowsPerPage: 0
      })
let columns = ref([
  {name: 'name',label: 'NOMBRE',field: 'name',align: 'center'},
  {name: 'category',label: 'CATEGORIA',field: 'category',align: 'center'},
  {name: 'hours',label: 'HORAS',align: 'center',field: row => row.hoursTotal,format: val => `${val}`,sortable: true},
  {name: 'hoursExtras',label: 'HORAS EXTRAS',align: 'center',field: row => row.hoursExtras,format: val => `${val}`,sortable: true},
  { name: 'extras', align: 'center', label: ' PAGO EXTRAS', field: 'extras',align: 'center', sortable: true },
  { name: 'pay', align: 'center', label: 'PAGO', field: 'pay',align: 'center', sortable: true },
  { name: 'total', align: 'center', label: 'TOTAL', field: 'total',align: 'center', sortable: true }
])

let rows = ref([])


function calculatePayroll(){
    if(hoursTotal.value > 40){ //A
      hoursExtras.value = hoursTotal.value - 40 //B
      hours.value = hoursTotal.value - hoursExtras.value
      extras.value = hoursExtras.value * (selectCategory.value.value*25/100) 
      pay.value = hours.value * selectCategory.value.value 
      console.log(pay.value);
      console.log(extras.value);
      
    } 
    else{
      pay.value = hoursTotal.value * selectCategory.value.value //C
    } // D
    total.value = pay.value + extras.value // E
    addRow()
    cleanModal()
  }


function validateInfo(){
  if(name.value == "" ){ //A
    alert.value ="el nombre es invalido"//B
  }
  else if(selectCategory.value == null){ //C
    alert.value ="no has seleccionado una categoria" //D
  }
  else if(hoursTotal.value <= 0 || hoursTotal.value == null){ // E
    alert.value =" La horas ingresadas son invalidas" //F
  }
  else{
    alert.value = "se ha registrado correctamente la nomina" //G
    calculatePayroll()
  } 
}


function addRow(){
  let newPayrrol= { //A
    name: name.value,
    category: selectCategory.value.label,
    hoursExtras: hoursExtras.value,
    hoursTotal: hoursTotal.value,
    extras: extras.value,
    pay: pay.value,
    total: total.value
  }
  rows.value.push(newPayrrol) //B
}


function cleanModal(){
  name.value = ""
  selectCategory.value = null
  hours.value = 0
  hoursTotal.value = 0
  extras.value = 0
  pay.value = 0
  total.value = 0
}

</script>