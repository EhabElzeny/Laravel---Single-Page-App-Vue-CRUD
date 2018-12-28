<template>
                    <div >
                        <button class="btn btn-primary btn-block" @click="createModal">Add Task</button>
                        <table class="table" v-if="tasks.length > 0">
                            <thead>
                            <th>id</th>
                            <th>task</th>
                            <th>body</th>
                            <th>created At:</th>
                            </thead>
                            <tbody>
                            <tr v-for="(task,index) in tasks">
                                <td>{{task.id}}</td>
                                <td>{{task.name}}</td>
                                <td>{{task.body}}</td>
                                <td>{{task.created_at}}</td>
                                <td>
                                    <button class="btn btn-primary" v-on:click="showUpdateModal(index)">edit</button>
                                </td>
                                <td>
                                    <button class="btn btn-danger" v-on:click="deleteTask(index)">delete</button>
                                </td>
                            </tr>
                            </tbody>
                        </table>
                        <div class="modal" id="create_modal" tabindex="-1" role="dialog">
                            <div class="modal-dialog" role="document">
                                <div class="modal-content">
                                    <div class="modal-header">
                                        <h5 class="modal-title">Task Create</h5>
                                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                            <span aria-hidden="true">&times;</span>
                                        </button>
                                    </div>
                                    <div class="modal-body">
                                        <div class="alert alert-danger" v-if="errors.length > 0">
                                            <ul >
                                                <li v-for="error in errors">
                                                    {{error}}
                                                </li>
                                            </ul>
                                        </div>
                                        <div class="form-group">
                                            <label for="name">name</label>
                                            <input type="text" v-model="task.name"  id="name"  class="form-control">
                                        </div>
                                        <div class="form-group">
                                            <label for="body">body</label>
                                            <input type="text" v-model="task.body"  id="body" class="form-control">
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="button" class="btn btn-primary" v-on:click="createTask">Save task</button>
                                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal" id="update_modal" tabindex="-1" role="dialog">
                            <div class="modal-dialog" role="document">
                                <div class="modal-content">
                                    <div class="modal-header">
                                        <h5 class="modal-title">Task update</h5>
                                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                            <span aria-hidden="true">&times;</span>
                                        </button>
                                    </div>
                                    <div class="modal-body">
                                        <div class="alert alert-danger" v-if="errors.length > 0">
                                            <ul >
                                                <li v-for="error in errors">
                                                    {{error}}
                                                </li>
                                            </ul>
                                        </div>
                                        <div class="form-group">
                                            <label for="name">name</label>
                                            <input type="text" v-model="new_update_task.name"   class="form-control">
                                        </div>
                                        <div class="form-group">
                                            <label for="body">body</label>
                                            <input type="text" v-model="new_update_task.body"  class="form-control">
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="button" class="btn btn-primary" v-on:click="saveUpdate">update task</button>
                                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
</template>

<script>
    export default {
        data(){
          return{
              task:{
                  name:'',
                  body:'',
              },
              tasks:[],
              errors:[],
              new_update_task:[],
              url:'http://localhost:8000/tasks/'
          }
        },methods:{
            createTask(){
            axios.post(this.url,{name:this.task.name,body:this.task.body}).then(response=>{
                this.tasks.push(response.data.task);
                this.task.name = '';
                this.task.body = '';

                $('#create_modal').modal('hide');
            }).catch(error=>{
                this.errors=[];
                if (error.response.data.errors.name){
                    this.errors.push(error.response.data.errors.name[0])
                }
                if (error.response.data.errors.body){
                    this.errors.push(error.response.data.errors.body[0])
                }

            });
            },
            createModal(){
                $('#create_modal').modal('show')
            },
        showUpdateModal(index){
            this.errors = [];
           this.new_update_task = this.tasks[index];
                $('#update_modal').modal('show')
        },
            saveUpdate(){
                axios.patch(this.url + this.new_update_task.id,{
                    name:this.new_update_task.name,
                    body:this.new_update_task.body,
                }).then(response=>{
                    $('#update_modal').modal('hide')
                }).catch(error=>{
                    if (error.response.data.errors.name){
                        this.errors.push(error.response.data.errors.name[0])
                    }
                    if (error.response.data.errors.body){
                        this.errors.push(error.response.data.errors.body[0])
                    }
                })
            },

        deleteTask(index){
                let confrimBox = confirm('Yor Are Sure ?');
                if (confrimBox == true){
                    axios.delete(this.url + this.tasks[index].id).then(response=>{
                        this.$delete(this.tasks,index)
                    });
                }
        }
        ,
            test(){

            }
            ,
            getTasks(){
                axios.get(this.url).then(response=>{
                    this.tasks=response.data.tasks ;
                    setTimeout(this.getTasks, 5000);

                });

            }
        },
        mounted() {
            this.getTasks();
            console.log('Component mounted.')
        }
    }
</script>
