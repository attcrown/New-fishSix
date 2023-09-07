<template>
    <div class="mx-15">
        <h3 style="font-size:32px"><b>รายวิชา</b></h3>
        <v-row class="pt-5">
            <v-col cols="12" sm="12">
                <sub_ject :hidden="status == 'opFS'"></sub_ject>
                <tablesubject style="margin-top: -5px;"></tablesubject>
            </v-col>
        </v-row>
        <h3 style="font-size:32px" class="mt-10"><b>สถานที่สอน</b></h3>
        <v-row class="pt-5">
            <v-col cols="12" sm="12">
                <input_location :hidden="status == 'opFS'"></input_location>
                <table_location style="margin-top: -5px;"></table_location>
            </v-col>
        </v-row>
        <h3 style="font-size:32px" class="mt-10"><b>Product</b></h3>
        <v-row class="pt-5">
            <v-col cols="12" sm="12">
                <product></product>
                <table_product></table_product>
            </v-col>
        </v-row>
        <h3 style="font-size:32px" class="mt-10"><b>Detail Product</b></h3>
        <v-row class="pt-5">
            <v-col cols="12" sm="12">
                <detail_product></detail_product>
                <table_detail_product></table_detail_product>
            </v-col>
        </v-row>


        <product_controller></product_controller>
    </div>
</template>
<script>
import tablesubject from './table_sub.vue';
import sub_ject from './sub.vue';
import input_location from './input_location.vue';
import table_location from './table_location.vue';
import product from './product.vue';
import product_controller from './controller/product_controller.vue';
import table_product from './table_product.vue';
import table_detail_product from './table_detail_product.vue';
import detail_product from './detail_product.vue';
import { mapState } from 'vuex';
export default {
    data() {
        return {
            
        }
    },
    components: {
    tablesubject,
    sub_ject,
    input_location,
    table_location,
    product,
    product_controller,
    table_product,
    detail_product,
    table_detail_product
},
    computed:{
        ...mapState(['firstName', 'status']),
    },
    methods:{
        
    }
}
</script>