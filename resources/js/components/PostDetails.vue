<template>
  <div>
    <div class="">

        <!-- Title -->
        <h1 class="mt-4">{{post.title}}</h1>

        <!-- Author -->
        <p class=" alert alert-info" style="width: fit-content;
        padding: 5px;
        color: #142d31;" v-if="post.category">{{post.category[0].name}}
        </p>

        <hr>

        <!-- Date/Time -->
        <p>Posted on <strong class="badge badge-primary p-1">{{post.added_at}}</strong>
            <span class="float-right"><strong class="badge badge-info p-1">{{comments.length}}</strong> comments</span></p>

        <hr>

        <!-- Preview Image -->
        <img class="img-fluid rounded" :src="'/img/'+post.image" style="width:100%;max-height:400px" alt="">

        <hr>

        <!-- Post Content -->
       <div class="text-justify"> {{post.body}} </div>
        <hr>

        <!-- Comments Form -->
        <div  class="card my-4">
          <h5 class="card-header">Leave a Comment:</h5>
          <div class="card-body">
            <form>
              <input  type="hidden" name="" v-model="post_id">
              <div class="form-group">
                <textarea class="form-control" rows="3" v-model="body"></textarea>
              </div>
              <button :disabled="!isLogged" type="submit" class="btn btn-primary" @click.prevent="addComment">Submit</button>
              <span class="text-warning" v-if="!isLogged"><b>Only users can comment.</b></span>
            </form>
          </div>
        </div>

        <!-- Single Comment -->
        <div class="media mb-4" v-for="(comment,i) in post.comments" :key="i">
          

          <img class="d-flex mr-3 rounded-circle" :src="'/img/'+comment.user.profile_img" style="height:50px;width:50px" alt="">
          <div class="media-body">
            <h5 class="mt-0"><strong>{{comment.user['name']}}  </strong></h5>
            {{comment.body}}
          </div>
        </div>



      </div>
  </div>
</template>

<script>
export default {
   data(){
     return {
       post:'',
       body:'',
       post_id : '',
       comments:[]

     }
   },
   created(){
     this.getPost();
     this.updateToken();

   },
   methods:{
     getPost(){
      axios.get('/api/posts/'+this.$route.params.slug)
      .then(res =>{
        console.log(res)
        this.post = res.data
        this.post_id = this.post.id;
        this.comments = this.post.comments

      })
      .catch(err =>{
        console.log(err)
      })
     },
     addComment(){
       let {body,post_id} = this;
       axios.post('/api/comment/create',{body,post_id})
       .then(res => {
         console.log(res)
         this.body = ''
         this.comments.unshift(res.data)
       })
     },
     updateToken(){
       let token =JSON.parse(localStorage.getItem('userToken'));
       this.$store.commit('setUserToken',token)
     }
   },
   computed:{
     isLogged(){
       return this.$store.getters.isLogged;
     }
   }


}
</script>
