<template>
    <div class="w-25">
            <form @submit.prevent="saveTodo">
                <div class="input-group mb-3 w-100">
                    <input
                    v-model="form.title"
                    :class="{'is-invalid' : form.errors.has('title')}"
                    @keydown="form.errors.clear('title')"
                     type="text"
                     class="form-control form-control-lg"
                     aria-label="Recipient's username"
                     aria-describedby="button-addon2">

                    <div class="input-group-append">
                        <button class="btn btn-success" type="submit" id="button-addon2">Add Todo to list</button>
                    </div>
                </div>
                <span class="text-danger pt-3" v-if="form.errors.has('title')" v-text="form.errors.get('title')"></span>

            </form>
            <ul v-for="todoItem in todos" :key="todoItem.id" class="list-group">
                <li class="list-group-item">{{todoItem.title}}</li>
            </ul>
    </div>
</template>

<script>
import axios from 'axios';
import Form from '../Form';

    export default {

        data(){
           return {
            todos:[],
            form: new Form({
                title: ''
            })

           }
        },
        methods: {

            getData(){
                    axios.get('/api/todo').then((res)=>{
                        this.todos = res.data;
                    }).catch((err)=>{
                        console.log("error : " , err);
                    });
            },

            saveTodo(){

                let data = new FormData();
                data.append('title' , this.form.title);

                //send to api controller
                axios.post("/api/todo",data).then((res)=>{

                    this.form.reset();
                    this.getData();

                }).catch((err)=>{
                    if( err.response.data.errors.title[0] !== undefined ){
                        console.log( err.response.data.errors.title[0]);
                        //alert(err.response.data.errors.title[0] );
                        this.form.errors.record(err.response.data.errors);
                    }
                });


            }
        },
        mounted() {
            console.log('Component mounted!');
            this.getData();
        },


    }
</script>
