<template>
    <v-data-table :headers="headers" :items="desserts" :search="search" sort-by="index" class="elevation-16 rounded-xl">
        <template v-slot:top>
            <v-toolbar flat>
                <v-toolbar-title class="ms-4"><b>รายวิชาที่เปิดสอน</b></v-toolbar-title>
                <v-divider class="mx-4" inset vertical></v-divider>
                <v-spacer></v-spacer>
                <v-dialog v-model="dialog" max-width="800px">
                    <template v-slot:activator="{}">
                        <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details
                            style="max-width: 200px;"></v-text-field>
                    </template>
                    <!-- <template v-slot:activator="{ on, attrs }">
                        <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                            New Item
                        </v-btn>
                    </template> -->
                    <v-card>
                        <v-card-title>
                            <span class="text-h5">EDIT SUBJECT</span>
                        </v-card-title>

                        <v-card-text>
                            <v-container>
                                <v-row>
                                    <v-col cols="12" sm="6" md="6">
                                        <v-text-field v-model="editedItem.name" label="ชื่อวิชา"></v-text-field>
                                    </v-col>
                                    <v-col cols="12" sm="6">
                                        <v-select v-model="editedItem.level" :items="level" item-text="value"
                                            item-value="value" attach chips label="ระดับการศึกษา"
                                            placeholder="กรุณาเลือกเรียงลงมา !!ไม่ติกข้ามไปมา!!" multiple>
                                        </v-select>
                                    </v-col>
                                    <v-col cols="12" sm="12">
                                        <div style="height:200px">

                                        </div>
                                    </v-col>

                                </v-row>
                            </v-container>
                        </v-card-text>

                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="blue darken-1" text @click="close">
                                Cancel
                            </v-btn>
                            <v-btn color="blue darken-1" text @click="edit_save">
                                Save
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
                <v-dialog v-model="dialogDelete" max-width="500px">
                    <v-card>
                        <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                            <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                            <v-spacer></v-spacer>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-toolbar>
        </template>
        <!-- eslint-disable-next-line vue/valid-v-slot -->
        <template v-slot:item.actions="{ item }">
            <!-- <v-btn color="amber darken-3" small outlined>
                <v-icon center class="text-h6" @click="editItem(item)">
                    mdi-pencil
                </v-icon>
            </v-btn> -->
        
                <v-icon class="text-h5" @click="editItem(item)" color="#26415B" style="text-decoration: underline;" v-if="status != 'opFS'">
                    mdi-pencil
                </v-icon>
           

            <!-- <v-icon small @click="deleteItem(item)">
                mdi-delete
            </v-icon> -->
        </template>
        <template v-slot:no-data>
            <v-btn color="primary" @click="initialize">
                Reset
            </v-btn>
        </template>
    </v-data-table>
</template>

<script>
import { mapState } from 'vuex';
export default {
    data: () => ({
        search: '',
        dialog: false,
        dialogDelete: false,
        headers: [
            { text: 'No.', value: 'index' },
            {
                text: 'Subject',
                align: 'start',
                sortable: false,
                value: 'name',
            },
            { text: 'Level', value: 'level' },
            { text: 'Actions', value: 'actions', sortable: false, align: 'center' },
        ],
        desserts: [],
        editedIndex: -1,
        editedItem: {
            name: '',
            level: '',
        },
        defaultItem: {
            name: '',
            level: '',
        },
        level: [
            { key: 1, value: 'ป.1' },
            { key: 2, value: 'ป.2' },
            { key: 3, value: 'ป.3' },
            { key: 4, value: 'ป.4' },
            { key: 5, value: 'ป.5' },
            { key: 6, value: 'ป.6' },
            { key: 7, value: 'ม.1' },
            { key: 8, value: 'ม.2' },
            { key: 9, value: 'ม.3' },
            { key: 10, value: 'ม.4' },
            { key: 11, value: 'ม.5' },
            { key: 12, value: 'ม.6' },
        ],
    }),

    computed: {
        formTitle() {
            return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
        },
        ...mapState(['firstName', 'status']),
    },

    watch: {
        dialog(val) {
            val || this.close()
        },
        dialogDelete(val) {
            val || this.closeDelete()
        },
    },

    created() {
        this.initialize()
    },
    mounted(){

    },

    methods: {
        initialize() {
            const db = this.$fireModule.database();
            db.ref("subject_all/").on("value", (snapshot) => {
                let item = [];
                let num = 0;
                this.desserts = [];
                const childData = snapshot.val();
                for (const key in childData) {
                    const subject = childData[key];
                    num += 1;
                    item.push({ name: subject.name, level: subject.level, key: key, index: num });
                    // console.log('>>>',item);
                }
                this.desserts = item;
            })
        },

        editItem(item) {
            this.editedIndex = this.desserts.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialog = true
        },

        deleteItem(item) {
            this.editedIndex = this.desserts.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialogDelete = true
        },

        deleteItemConfirm() {
            // console.log(this.editedItem.key);
            const db = this.$fireModule.database();
            db.ref(`subject_all/${this.editedItem.key}`).remove();
            this.closeDelete();
        },

        close() {
            this.dialog = false;
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem)
                this.editedIndex = -1
            })
        },

        closeDelete() {
            this.dialogDelete = false
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem)
                this.editedIndex = -1
            })
        },

        edit_save() {
            const db = this.$fireModule.database();
            db.ref(`subject_all/${this.editedItem.key}`).set({
                name: this.editedItem.name,
                level: this.editedItem.level,
            });
            this.close();
            // if (this.editedIndex > -1) {
            //     Object.assign(this.desserts[this.editedIndex], this.editedItem)
            // } else {
            //     this.desserts.push(this.editedItem)
            // }

        },
    },
}
</script>