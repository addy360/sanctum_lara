<template>
    <div class="row">
        <div class="col-md-6 mx-auto">
            <div class="borderd p-1" v-if="user">
                <p>email : {{ user.email }}</p>
                <div @click="logout" class="btn btn-primary">logout</div>
            </div>
            <form v-if="!user" action="" @submit.prevent="handleLogin">
                <h4 class="text-center">Login</h4>
                <div class="input-group mt-3">
                    <input class="form-control" type="email" v-model="formData.email" placeholder="your email">
                    <input class="form-control" type="password" v-model="formData.password" placeholder="your password">
                    <button type="submit" class="btn btn-outline-success">Login</button>
                </div>
            </form>
        </div>
        <div v-if="error_msg" class=" col-12 mt-4 alert alert-danger">
            <p v-text="error_msg"></p>
        </div>
        <div v-if="posts && !posts.length" class=" col-12 mt-4 alert alert-info">
            <p>You have no posts at the moment.. sorry!</p>
        </div>
        <div v-if="posts" class="col-md-8 mt-4 mx-auto">
            <ul>
                <li v-for="post in posts" :key="post.id">
                    <p v-text="post.post"></p>
                    <small v-text="post.created_at">date</small>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                formData:{
                    email:'',
                    password:''
                },
                posts:null,
                user:null,
                error_msg:null
            }
        },
        mounted() {
            this.getLoggedInUser()
            this.getPosts()
        },
        methods:{
            async handleLogin(){
                try {
                    let res = await axios.get('/sanctum/csrf-cookie')
                    res = await axios.post('/login',this.formData)
                    await this.getPosts()
                    this.getLoggedInUser()
                } catch (error) {
                    this.error_msg = error.response.data.message
                    setTimeout(()=>this.error_msg = null, 5000);  
                }
            },
            async getPosts(){
                try {
                    let res = await axios.get('/api/post')
                    this.posts  = res.data
                    console.log(res)
                } catch (error) {
                    this.error_msg = error.response.data.message
                    setTimeout(()=>this.error_msg = null, 5000);                
                }
            },
            async getLoggedInUser(){
                try {
                    let res = await axios.get('/api/user')
                    this.user  = res.data
                } catch (error) {
                    this.error_msg = error.response.data.message
                    setTimeout(()=>this.error_msg = null, 5000);                
                }
            },
            async logout(){
                try {
                    let res = await axios.post('/logout')
                    this.user = null
                    this.posts = null
                } catch (error) {
                    this.error_msg = error.response.data.message
                    setTimeout(()=>this.error_msg = null, 5000);                
                }
            }
        },
    }
</script>
