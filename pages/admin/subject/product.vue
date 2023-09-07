<template>
    <div>
        <v-card class="elevation-16 rounded-t-xl px-5 pt-3" style="background-color:#EBE4DE">
            <v-container fluid>
                <h5><b>เพิ่ม Product</b></h5>
                <v-row align="center">
                    <v-col cols="12" sm="3">
                        <v-text-field label="ชื่อ Product" placeholder="ระบุวิชา" :rules="rules.name"
                            v-model="data_add.name" required></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="2">
                        <v-btn elevation="10" small color="#322E2B" style="color:white" :disabled="!data_add.name"
                            @click="callsaveProduct(data_add)">เพิ่มรายวิชา
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
            data_add: [],
            rules: {
                // age: [(val) => val < 10 || `I don't believe you!`],
                select: [(val) => (val || "").length > 0 || "กรุณาระบุข้อมูล"],
                name: [(val) => (val || "").length > 0 || "กรุณาระบุข้อมูล"],
            },
        }
    },
    methods: {
        handleCalendarResult(result) {
            this.data_add=[];
            console.log('result:', result);
        },
        callsaveProduct(item) {
            CheckedEventBus.$emit('SaveProduct', item, (result) => {
                this.handleCalendarResult(result);
            });
            console.log('callsaveProduct');
        },
    }
}
</script>