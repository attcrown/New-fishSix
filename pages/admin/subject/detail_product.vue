<template>
    <div>
        <v-card class="elevation-16 rounded-t-xl px-5 pt-3" style="background-color: #ebe4de">
            <v-container fluid>
                <h5><b>เพิ่ม Detail Product</b></h5>
                <v-row align="center">
                    <v-col cols="12" sm="3">
                        <v-select v-model="data_add.idClass" :items="classPro" 
                            item-text="name" item-value="id" attach
                            chips label="เลือกประเภท Product">
                        </v-select>
                    </v-col>
                    <v-col cols="12" sm="3">
                        <v-text-field label="ชื่อ Product" placeholder="ระบุวิชา" :rules="rules.name"
                            v-model="data_add.name" required></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="2">
                        <v-btn elevation="10" small color="#322E2B" style="color: white" :disabled="!data_add.name && !data_add.class"
                            @click="callsaveDetailProduct(data_add)">เพิ่มรายวิชา
                            <span class="mdi mdi-plus"></span>
                        </v-btn>
                    </v-col>
                </v-row>
            </v-container>
        </v-card>
    </div>
</template>
<script>
import { CheckedEventBus } from './controller/product_controller.vue'
export default {
    data() {
        return {
            classPro:[],
            data_add: [],
            rules: {
                // age: [(val) => val < 10 || `I don't believe you!`],
                select: [(val) => (val || '').length > 0 || 'กรุณาระบุข้อมูล'],
                name: [(val) => (val || '').length > 0 || 'กรุณาระบุข้อมูล'],
            },
        }
    },
    mounted() {
        this.callsearch_product()
    },
    methods: {
        handleCalendarResult(result) {
            this.data_add = []
            console.log('result:', result)
        },        
        callsearch_product() {
            CheckedEventBus.$emit('Search_Product', (result) => {                
                this.classPro = result;              
                console.log(this.classPro)
            })
            console.log('ค้นหา')
        },
        callsaveDetailProduct(item){
            console.log(item);
            CheckedEventBus.$emit('SaveDetailProduct', item, (result) => {     
                this.handleCalendarResult(result)           
                console.log(result);
            })
        }
    },
}
</script>
