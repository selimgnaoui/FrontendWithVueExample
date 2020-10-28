<template>

    <div>
        <b-button @click="$bvModal.show('bv-modal-newtask')">Add new Task</b-button>
        <b-table striped hover :fields="fields" :items="tasks">

            <template #cell(action)="row">
                <b-button @click="DeleteEvent(row)" size="sm"  class="mr-2">
                 Delete
                </b-button>
                <b-button @click="showModal(row)" size="sm"  class="mr-2">
                    Edit
                </b-button>
                <b-button v-if="!row.item.done" size="sm" @click="setEventAsDone(row)"  class="mr-2">
                     Mark as Done
                </b-button>

            </template>





        </b-table>


        <b-modal ref="modal" id="bv-modal-example" hide-footer>
            <template #modal-title>
                Edit a Task
            </template>
            <div>
                <b-form inline>

                    <label class="mr-sm-2" for="inline-form-custom-select-pref">Name</label>
                    <b-input
                            id="inline-form-input-name"
                            class="mb-2 mr-sm-2 mb-sm-0"
                            placeholder="Task Name"
                            v-model="task.name"
                    ></b-input>


                    <label class="mr-sm-2" for="inline-form-custom-select-pref">Type</label>
                    <b-form-select
                            id="inline-form-custom-select-pref"
                            class="mb-2 mr-sm-2 mb-sm-0"
                            v-model="task.type"
                            :options="[{ text: 'Choose type', value: null }, 'Call', 'Meeting', 'Misc']"
                            :value="null"
                    ></b-form-select>




                </b-form>
            </div>
            <b-button @click="updateEvent" variant="primary">Save</b-button>  <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Close Me</b-button>
        </b-modal>
        <b-modal ref="modal" id="bv-modal-newtask" hide-footer>
            <template #modal-title>
                Add a new Task
            </template>
            <div>
                <b-form inline>

                    <label class="mr-sm-2" for="inline-form-custom-select-pref">Name</label>
                    <b-input
                            id="inline-form-input-name"
                            class="mb-2 mr-sm-2 mb-sm-0"
                            placeholder="Task Name"
                            v-model="task.name"
                    ></b-input>


                    <label class="mr-sm-2" for="inline-form-custom-select-pref">Type</label>
                    <b-form-select
                            id="inline-form-custom-select-pref"
                            class="mb-2 mr-sm-2 mb-sm-0"
                            v-model="task.type"
                            :options="[{ text: 'Choose type', value: null }, 'Call', 'Meeting', 'Misc']"
                            :value="null"
                    ></b-form-select>




                </b-form>
            </div>
            <b-button @click="saveNewEvent" variant="primary">Save</b-button>  <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Close Me</b-button>
        </b-modal>
    </div>


</template>

<script>
    import axios from 'axios'
    import 'bootstrap'
    import 'bootstrap/dist/css/bootstrap.min.css'
    import 'bootstrap/dist/css/bootstrap.css'
    import 'bootstrap-vue/dist/bootstrap-vue.css'

    export default {
        name: 'MainView',
        data: function () {
            return {
                tasks: [],
                fields: ["id", "name","type" ,"done","action"],
                name : "",
                type : "",
                task : {
                    "id" : "",
                    "name": "",
                    "done": false,
                    "type": ""
                }

            }
        },
        mounted () {
            this.fetchEvent();

        },
        methods: {
            DeleteEvent : function (row) {
                console.log(row.item.id)
                axios
                    .delete('http://localhost:81/api/tasks/'+row.item.id,{
                        headers: {
                            'Accept': 'application/json'
                        }
                    })
                    .then(() => {
                        this.fetchEvent()
                    })
                    .catch(error => {
                        console.log(error)
                    })
            },

            fetchEvent : function () {
                axios
                    .get('http://localhost:81/api/tasks',{
                        headers: {
                            'Accept': 'application/json'
                        }
                    })
                    .then(response => {
                        this.tasks = response.data
                    })
                    .catch(error => {
                        console.log(error)
                    })

            },

            updateEvent : function () {


                axios
                    .patch('http://localhost:81/api/tasks/'+this.task.id,JSON.stringify(this.task), {
                            headers: {
                                'Content-Type': 'application/merge-patch+json'

                            }
                        }
                       )
                    .then(() => {
                      this.fetchEvent()
                    })
                    .catch(error => {

                        console.log(error)
                    })
                    .finally(()=> {
                        this.$bvModal.hide('bv-modal-example')
                    })

            },
            saveNewEvent: function () {
                 delete this.task.id
                axios
                    .post('http://localhost:81/api/tasks',JSON.stringify(this.task), {
                            headers: {
                                'Content-Type': 'application/json'
                            }
                        }
                    )
                    .then(() => {
                        this.fetchEvent()
                    })
                    .catch(error => {

                        console.log(error)
                    })
                    .finally(()=> {
                        this.$bvModal.hide('bv-modal-newtask')
                    })
            },
            setEventAsDone: function (row) {
                let itemId=(row.item.id)
                axios
                    .patch('http://localhost:81/api/tasks/'+itemId,{
                        "done": true
                        }, {
                            headers: {
                                'Content-Type': 'application/merge-patch+json'

                            }
                        }
                    )
                    .then(() => {
                        this.fetchEvent()
                    })
                    .catch(error => {

                        console.log(error)
                    })
                    .finally(()=> {
                        this.$bvModal.hide('bv-modal-example')
                    })

            },

            showModal: function (row) {
                this.task.name  = row.item.name
                this.task.type  = row.item.type
                this.task.id  = row.item.id
                this.$bvModal.show('bv-modal-example')


            }
        }
    }

</script>

<style scoped>
    h3 {
        margin: 40px 0 0;
    }
    ul {
        list-style-type: none;
        padding: 0;
    }
    li {
        display: inline-block;
        margin: 0 10px;
    }
    a {
        color: #42b983;
    }
</style>
