<template>
    <div>
        <h1>Все записи</h1>
        <div class="row">
            <div class="col-sm-6">
                <form @submit.prevent="addPost" class="mb-4">
                    <div class="mb-3">
                        <label for="title" class="form-label">Название</label>
                        <input v-model="post.title" type="text" class="form-control" id="title" >
                    </div>
                    <div class="form-floating">
                        <label for="description">Описание</label>
                        <textarea  v-model="post.description" class="form-control" placeholder="Leave a comment here" id="description"></textarea>
                    </div>
                    <button type="submit" class="btn btn-primary">Добавить</button>
                </form>
            </div>
        </div>
        <div v-if="errored"  class="alert alert-denger" role="alert">
            Записи не загрузились, попробуйте позже
        </div>
        <table v-else class="table">
            <div v-if="loading"> Загрузка... </div>
            <thead class="thead-dark">
                <tr>
                    <th scope="col">ID</th>
                    <th scope="col">Название</th>
                    <th scope="col">Описание</th>
                    <th scope="col">Кнопки</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts" :key="post.id">
                    <th >{{ post.id }}</th>
                    <td>{{ post.title }}</td>
                    <td>{{ post.description }}</td>
                    <td>
                        <button class="btn btn-success">Редактировать</button>
                        <button @click="deletePost(post.id)" class="btn btn-danger">Удалить</button>
                    </td>
                </tr>
            </tbody>
        </table>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item">
                    <a :class="{ disabled: !pagination.prev_page_url }"
                        @click.prevent="getPosts(pagination.prev_page_url)"
                        class="page-link" href="#">
                        Назад
                    </a>
                </li>
                <li class="page-item">
                    <a class="page-link disabled" href="#">
                        Страница {{ pagination.current_page }} из {{ pagination.last_page }}
                    </a>
                </li>
                <li class="page-item">
                    <a  :class="{ disabled: !pagination.next_page_url }"
                        @click.prevent="getPosts(pagination.next_page_url)"
                        class="page-link" href="#">
                        Следущая
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                posts: [],
                post: {
                    id: '',
                    title: '',
                    description: ''
                },
                post_id: '',
                pagination: {},
                edit: false,
                loading: true,
                errored: false
            }
        },
        mounted() {
            this.getPosts()
        },
        methods:{
            getPosts(page_url) {
                page_url = page_url || '/api/posts'
                axios
                    .get(page_url)
                    .then(response => {
                        this.posts = response.data.data
                        this.makePagination(response.data)
                    })
                    .catch(error => this.errored = true)
                    .finally(()=> this.loading = false)
            },
            makePagination(response){
                let pagination = {
                    current_page: response.current_page,
                    last_page: response.last_page,
                    prev_page_url: response.prev_page_url,
                    next_page_url: response.next_page_url,
                }

                this.pagination = pagination
            },
            deletePost(id){
                axios
                    .delete('/api/posts/'+id)
                    .then(response => {
                        this.getPosts()
                        console.log(response)
                    })
                    .catch(error => console.log(error))
            },
            addPost(){
                if (this.edit == false) {
                    //Добавление поста
                    axios
                        .post('/api/posts',{
                            title: this.post.title,
                            description: this.post.description
                        })
                        .then(response => {
                            this.post.title = ""
                            this.post.description = ""
                            this.getPosts()
                            console.log(response)
                        })
                        .catch(error => console.log(error))
                }  else {
                    // Редактирование
                }
            }
        }
    }
</script>


