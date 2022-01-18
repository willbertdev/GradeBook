<template>
    <v-container>
        <v-row>
            <v-col cols="12">
                <v-data-table
                    :headers="headers"
                    :items="records"
                    sort-by="calories"
                    class="elevation-1"
                >
                    <template v-slot:top>
                    <v-toolbar flat >
                        <v-toolbar-title>Grade Book</v-toolbar-title>
                        <v-spacer></v-spacer>
                        <v-dialog
                            v-model="dialog"
                            max-width="500px"
                        >
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn
                                color="primary"
                                dark
                                class="mb-2"
                                v-bind="attrs"
                                v-on="on">
                                New Item
                            </v-btn>
                        </template>
                        <v-card>
                            <v-card-title>
                            <span class="text-h5">{{ formTitle }}</span>
                            </v-card-title>

                            <v-card-text>
                                <v-container>
                                    <v-form ref="form" v-model="valid" lazy-validation>
                                        <v-row>
                                            <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                            >
                                                <v-text-field v-model="editedItem.firstname"
                                                :rules="firstnameRule"
                                                label="Legal first name*"
                                                required
                                                ></v-text-field>
                                            </v-col>
                                            <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                            >
                                                <v-text-field v-model="editedItem.middlename"
                                                label="Legal middle name"
                                                hint="example of helper text only on focus"
                                                ></v-text-field>
                                            </v-col>
                                            <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                            >
                                                <v-text-field v-model="editedItem.lastname"
                                                :rules="lastnameRule"
                                                label="Legal last name*"
                                                persistent-hint
                                                required
                                                ></v-text-field>
                                            </v-col>
                                            <v-col cols="12">
                                                <v-text-field v-model="editedItem.grade"
                                                :rules="gradeRule"
                                                label="Grade*"
                                                required
                                                type="number"
                                                ></v-text-field>
                                            </v-col>
                                        </v-row>
                                    </v-form>
                                </v-container>
                            </v-card-text>

                            <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                color="blue darken-1"
                                text
                                @click="close"
                            >
                                Close
                            </v-btn>
                            <v-btn
                                color="blue darken-1"
                                text
                                @click="save"
                            >
                                Save
                            </v-btn>
                            </v-card-actions>
                        </v-card>
                        </v-dialog>
                        <v-dialog v-model="dialogDelete" max-width="500px">
                        <v-card>
                            <v-card-title class="text-h5">Are you sure you want to delete this record?</v-card-title>
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
                    <template v-slot:item.actions="{ item }">
                    <v-icon
                        small
                        class="mr-2"
                        @click="editItem(item)"
                    >
                        mdi-pencil
                    </v-icon>
                    <v-icon
                        small
                        @click="deleteItem(item)"
                    >
                        mdi-delete
                    </v-icon>
                    </template>
                </v-data-table>
            </v-col>
        </v-row>
        <v-row>
            <v-col cols="5">
                <v-btn color="primary" dark @click="clearRecord()">
                    Clear Grades
                </v-btn>
            </v-col>
            <v-col cols="2">
                <h4 align="center" :class="!sAverage ? 'hidden-sm-and-up' : ''">Class Average: <i>{{ average }}</i></h4>
            </v-col>
            <v-col cols="5">
                <v-btn class="float-end" align="end" color="primary" dark @click="getAverage()">
                    Get Final Average
                </v-btn>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
  export default {
    data: () => ({
        valid: true,
        dialog: false,
        dialogDelete: false,
        sAverage: false,
        average: 0,
        headers: [
            {
                text: 'ID',
                width: '5%',
                value: 'id'
            },
            {
                text: 'Student Name',
                align: 'start',
                value: 'fullName',
            },
            { text: 'Grade', value: 'grade' },
            { text: 'Actions', value: 'actions', sortable: false },
        ],
        records: [],
        editedIndex: -1,
        editedItem: {
            fullName: '',
            firstname: '',
            lastname: '',
            middlename: '',
            grade: '',
        },
        defaultItem: {
            fullName: '',
            firstname: '',
            lastname: '',
            middlename: '',
            grade: '',
        },
        firstnameRule: [
            v => !!v || 'Name is required',
        ],
        lastnameRule: [
            v => !!v || 'Name is required',
        ],
        gradeRule: [
            v => !!v || 'Name is required',
        ],
    }),

    computed: {
        formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
        },
    },

    watch: {
        dialog (val) {
        val || this.close()
        },
        dialogDelete (val) {
        val || this.closeDelete()
        },
    },

    created () {
        this.initialize()
    },

    methods: {
        initialize () {
            this.records = []
        },

        editItem (item) {
            console.log(item);
            this.editedIndex = this.records.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialog = true
        },

        deleteItem (item) {
            this.editedIndex = this.records.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialogDelete = true
        },

        deleteItemConfirm () {
            this.records.splice(this.editedIndex, 1)
            this.closeDelete()
        },

        close () {
            this.dialog = false
            this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem)
            this.editedIndex = -1
            })
        },

        closeDelete () {
            this.dialogDelete = false
            this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem)
            this.editedIndex = -1
            })
        },

        save () {
            if (this.editedIndex > -1) {
                this.editedItem.fullName = this.capitalize(this.editedItem.lastname) + ", " + this.capitalize(this.editedItem.firstname) + " " + this.capitalize(this.editedItem.middlename)
                Object.assign(this.records[this.editedIndex], this.editedItem)
            } else {
                if (this.$refs.form.validate()) {
                    let newRecord = {
                        id: this.records.length + 1,
                        firstname: this.capitalize(this.editedItem.firstname),
                        lastname: this.capitalize(this.editedItem.lastname),
                        middlename: this.capitalize(this.editedItem.middlename),
                        fullName: this.capitalize(this.editedItem.lastname) + ", " + this.capitalize(this.editedItem.firstname) + " " + this.capitalize(this.editedItem.middlename),
                        grade: this.editedItem.grade
                    };

                    this.records.push(newRecord);
                    this.$refs.form.reset();
                }
            }
        },
        getAverage() {
            let totalAverage = 0

            for(let i in this.records) {
                totalAverage = parseInt(this.records[i].grade) + parseInt(totalAverage)
            }
            
            this.average = parseInt(totalAverage) / this.records.length;
            this.average = this.average.toFixed(1);

            if (this.average != 0) {
                this.sAverage = true;
            }
        },
        clearRecord() {
            this.records.splice(0, this.records.length)
        },
        capitalize(s) {
            return s && s[0].toUpperCase() + s.slice(1)
        }
    },
  }
</script>