<template>
    <v-container style="margin-top:30px;">
        <v-row>
            <v-row>
                <v-dialog
                v-model="dialog"
                persistent
                max-width="600px"
                >
                <template v-slot:activator="{ on, attrs }">
                    <v-container>
                        <v-row>
                            <v-col cols="12">
                                <v-btn
                                color="primary"
                                dark
                                v-bind="attrs"
                                v-on="on"
                                >
                                Add new record
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-container>
                </template>
                <v-card>
                    <v-card-title>
                    <span class="text-h5">New Record</span>
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
                                    <v-text-field v-model="firstname"
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
                                    <v-text-field v-model="middlename"
                                    label="Legal middle name"
                                    hint="example of helper text only on focus"
                                    ></v-text-field>
                                </v-col>
                                <v-col
                                    cols="12"
                                    sm="6"
                                    md="4"
                                >
                                    <v-text-field v-model="lastname"
                                    :rules="lastnameRule"
                                    label="Legal last name*"
                                    persistent-hint
                                    required
                                    ></v-text-field>
                                </v-col>
                                <v-col cols="12">
                                    <v-text-field v-model="grade"
                                    :rules="gradeRule"
                                    label="Grade*"
                                    required
                                    type="number"
                                    ></v-text-field>
                                </v-col>
                            </v-row>
                        </v-form>
                    </v-container>
                    <small>*indicates required field</small>
                    </v-card-text>
                    <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                        color="blue darken-1"
                        text
                        @click="dialog = false"
                    >
                        Close
                    </v-btn>
                    <v-btn type="submit"
                        color="blue darken-1"
                        text
                        @click="saveRecord()"
                    >
                        Save
                    </v-btn>
                    </v-card-actions>
                </v-card>
                </v-dialog>
            </v-row>
        </v-row>
        <v-row>
            <v-col cols="12">
                <v-card>
                    <v-card-title>
                    <v-text-field
                        v-model="search"
                        append-icon="mdi-magnify"
                        label="Search"
                        single-line
                        hide-details
                    ></v-text-field>
                    </v-card-title>
                    <template> 
                        <v-data-table
                        :headers="headers"
                        :items="records"
                        :search="search">
                            <template v-slot:top>
                                
                            </template>
                        </v-data-table>
                    </template>
                </v-card>
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
    data () {
        
      return {
        valid: true,
        dialog: false,
        sAverage: false,
        average: 0,
        firstname: '',
        middlename: '',
        lastname: '',
        grade: '',
        search: '',
        headers: [
          {
            text: 'ID',
            align: 'start',
            value: 'id',
            width: '5%'
          },
          { text: 'Student Name', value: 'fullName' },
          { text: 'Grade', value: 'grade' },
        ],
        records: [
          {
            id: 1,
            fullName: 'Farinas, Willbert F',
            grade: 89,
          },
        ],
        firstnameRule: [
            v => !!v || 'Name is required',
        ],
        lastnameRule: [
            v => !!v || 'Name is required',
        ],
        gradeRule: [
            v => !!v || 'Name is required',
        ],
      }
    },
    methods: {
        saveRecord() {
            if (this.$refs.form.validate()) {
                let newRecord = {
                    id: this.records.length + 1,
                    fullName: this.capitalize(this.lastname) + ", " + this.capitalize(this.firstname) + " " + this.capitalize(this.middlename),
                    grade: this.grade
                };

                this.records.push(newRecord);
                this.$refs.form.reset();
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
    }
  }
</script>

<style>

</style>