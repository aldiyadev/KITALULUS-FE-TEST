<template>
  <div class >
    <div class="container">
        <div class="row">
            <div class="col mt-5">
                <button class="btn btn-outline-primary my-3" @click="setFilter()" >Filter</button>
                <table class="table table-bordered bg-light">
                    <thead class="thead-dark">
                        <tr>
                            <th scope="col">No</th>
                            <th scope="col">Title</th>
                            <th scope="col">View</th>
                            <th scope="col">Genre</th>
                            <th scope="col">Descriptions</th>
                            <th scope="col">Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-if="filter == true">
                            <td></td>
                            <td>
                                <input type="text" placeholder="Title" v-model="titleSearch" >
                            </td>
                            <td></td>
                            <td>
                                <input type="text" placeholder="Genre" v-model="genreSearch">
                            </td>
                            <td></td>
                            <td></td>
                        </tr>
                        <tr v-for="(data, index) in searchData" :key="index" >
                            <th scope="row">{{ index+1 }}</th>
                            <td>
                                <div v-show="data.isTrue == true" >
                                    <input type="text" name="title" ref="title" v-model="data.title"  >
                                </div>
                                <div v-show="data.isTrue == false" >
                                    {{data.title}}
                                </div>
                            </td>
                            <td>
                                <div v-show="data.isTrue == true">
                                    <input type="number" name="views" ref="views" v-model="data.views">
                                </div>
                                <div v-show="data.isTrue == false">
                                    {{data.views}}
                                </div>
                            </td>
                            <td>
                                <div v-show="data.isTrue == true">
                                    <input type="text" name="genre" ref="genre" v-model="data.genre">
                                </div>
                                <div v-show="data.isTrue == false">
                                    {{data.genre}}
                                </div>
                            </td>
                            <td>
                                <div v-show="data.isTrue == true">
                                    <textarea cols="30" rows="5" name="descriptions" ref="descriptions" v-model="data.descriptions"></textarea>
                                </div>
                                <div v-show="data.isTrue == false">
                                    {{sliceDesc[index]}} ... 
                                    <button class="btn text-warning" data-toggle="modal" :data-target="'#abc'+data.views">
                                    <i class="fas fa-exclamation-circle"></i>
                                    </button>
                                </div>

                                <!-- Modal -->
                                <div class="modal fade" :id="'abc'+data.views" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                                    <div class="modal-dialog">
                                        <div class="modal-content">
                                        <div class="modal-header">
                                            <h5 class="modal-title" id="exampleModalLabel">{{data.title}}</h5>
                                            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                            <span aria-hidden="true">&times;</span>
                                            </button>
                                        </div>
                                        <div class="modal-body">
                                            {{data.descriptions}}
                                        </div>
                                        <div class="modal-footer">
                                            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                        </div>
                                        </div>
                                    </div>
                                </div>

                            </td>
                            <td v-if="data.isTrue == false">
                                <button class="btn btn-primary btn-sm" @click="setEdit(index)" > 
                                        <i class="fas fa-pen"></i> 
                                </button>
                                
                            </td>
                            <td v-else>
                                <button class="btn btn-success btn-sm" @click="setUpdate(index)" > 
                                        <i class="fas fa-save" ></i> 
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>   
            </div>
        </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'

export default {
    name: "Home",
    data() {
        return {
            datas : [],
            storage : [],
            filter : false,
            titleSearch : undefined,
            genreSearch : undefined,
        }
    },
    methods: {
        getData() {

                axios
                .get(`https://andywiranata-42555.firebaseio.com/test-frontend/items.json`)
                .then(result => {
                    this.datas = result.data;

                    this.datas.forEach(function(data) {
                        Vue.set(data, "isTrue", false)
                    })

                })
                .catch(error => {
                    console.log(error);
                }) 
                
        },
        setUpdate(index) {

                let form = {
                    title : this.$refs.title[index].value ,
                    views : this.$refs.views[index].value,
                    genre : this.$refs.genre[index].value,
                    descriptions : this.$refs.descriptions[index].value,
                }

                axios
                    .put(`https://andywiranata-42555.firebaseio.com/test-frontend/items/${index}.json`, form, {
                        headers: {
                            "content-type": "application/json"}
                    })
                    .then((result) => {
    
                        console.log(result)

                        this.setEdit(index);
                        
                    })
                    .catch((error) => {
                        this.datas[index].actionActive = false;
                        console.log(error);
                })
        },
        setFilter() {
            this.filter = !this.filter;
            this.titleSearch = undefined
            this.genreSearch = undefined
        },
        setEdit(index) {
            this.datas[index].isTrue = !this.datas[index].isTrue;

        },
        
    },
    mounted() {
        this.getData() ;
    },
    computed: {
        sliceDesc() {
            let arr = [];

            this.datas.forEach(function(data) {
                let fullDesc = data.descriptions;
                let slice = fullDesc.slice(0, 25);

                arr.push(slice);
            })


            return arr;
        },
        searchData : function() {
            return this.datas.filter((data) => {
                return data.title.toLowerCase().match(this.titleSearch) && data.genre.toLowerCase().match(this.genreSearch)
            }
        )}
    }
}
</script>

<style>

</style>