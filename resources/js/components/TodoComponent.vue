<template>
  <div class="container">
    <div class="modal fade" id="editModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLongTitle">Edit Todo</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close" v-on:click="btnClose">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="container py-2">
            <form @submit.prevent="updateTodo">
              <div class="form-group">
                <label for="">Title</label>
                <input v-model="form.title" type="text" class="form-control">
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal" v-on:click="btnClose">Close</button>
                <button type="submit" class="btn btn-primary">Save changes</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>




    <div class="py-5 text-center">
      <h2 class="text-white">Vue JS Todo List App</h2>
    </div>
    <div class="w-50 m-auto">
      <form @submit.prevent="saveData()">
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <button class="btn btn-success" type="submit">Add Todo</button>
          </div>
          <input v-model="form.title" type="text" class="form-control" aria-describedby="basic-addon1"/>
        </div>
      </form>
    </div>
    <div class="w-50 m-auto">
      
      <div v-for="todo in todos" :key="todo.id" class="my-1 py-2 px-2">
        <span>
          <svg v-if="todo.status == 'completed'" v-on:click="toggleTodo(todo)" xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-circle-check" width="28" height="28" viewBox="0 0 24 24" stroke-width="1.5" stroke="#03A9F4" fill="none" stroke-linecap="round" stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
            <circle cx="12" cy="12" r="9" />
            <path d="M9 12l2 2l4 -4" />
          </svg>
          <svg v-if="todo.status == 'incomplete'" v-on:click="toggleTodo(todo)" xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-circle" width="28" height="28" viewBox="0 0 24 24" stroke-width="1.5" stroke="#FFC107" fill="none" stroke-linecap="round" stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
            <circle cx="12" cy="12" r="9" />
          </svg>
        </span>
        <span class="text-white" v-if="todo.status == 'completed'"><s>{{ todo.title }}</s></span>
        <span class="text-white" v-if="todo.status == 'incomplete'">{{ todo.title }}</span>
        <div class="float-right">
          <span v-on:click="editTodo(todo)">
            <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-pencil" width="28" height="28" viewBox="0 0 24 24" stroke-width="1.5" stroke="#FFC107" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
              <path d="M4 20h4l10.5 -10.5a1.5 1.5 0 0 0 -4 -4l-10.5 10.5v4" />
              <line x1="13.5" y1="6.5" x2="17.5" y2="10.5" />
            </svg>
          </span>
          <span>
            <svg v-on:click="removeTodo(todo)" xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-trash" width="28" height="28" viewBox="0 0 24 24" stroke-width="1.5" stroke="#F44336" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
              <line x1="4" y1="7" x2="20" y2="7" />
              <line x1="10" y1="11" x2="10" y2="17" />
              <line x1="14" y1="11" x2="14" y2="17" />
              <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12" />
              <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3" />
            </svg>
          </span>
        </div>
        <hr>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    
    data(){
      return{
        editMode: false,
        todos: '',
        form: new Form({
          id: '',
          title: ''
        })
      }
    },
    methods:{
      updateTodo(){
        console.log(this.form.id)
        this.form.put('api/updateTodo/' + this.form.id).then((res) => {
          $('#editModal').modal('hide')
          Toast.fire({
            icon: 'success',
            title: res.data.message
          })
          this.getTodos()
        }).catch((err) => {
          Toast.fire({
            icon: 'danger',
            title: err
          })
        })
      },
      btnClose(){
        this.form.reset()
      },
      editTodo(todo){
        $('#editModal').modal('show')
        this.form.fill(todo)
      },
      saveData(){
        this.form.post('api/addTodo').then((res) => {
          this.form.reset()
          this.getTodos()
          Toast.fire({
            icon: 'success',
            title: res.data.message
          })
        }).catch((err) => {
          Toast.fire({
            icon: 'success',
            title: err
          })
        })
      },
      removeTodo(todo){

        Swal.fire({
          title: 'Are you sure?',
          text: "You won't be able to revert this!",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Yes, delete it!'
        }).then((result) => {
          if (result.isConfirmed) {
            axios.delete('/api/deleteTodo/' + todo.id).then((res) => {
              Toast.fire({
                icon: 'success',
                title: res.data.message
              })
              this.getTodos()
            }).catch((err) => {
              Toast.fire({
                icon: 'success',
                title: err
              })
            })
          }
          else{
            Swal.close()
          }
        })



        // console.log(todo)
        
      },
      toggleTodo(todo){
        
        axios.put('/api/toggleTodo/' + todo.id, todo).then((response) => {
          console.log(response.data)
          this.getTodos()
        }).catch((err) => {
          console.log(err)
        })

      },
      getTodos(){
        axios.get('/api/myTodo').then((res) => {
          this.todos = res.data.data
        }).catch((err) => {
          console.log(err)
        })
      }
    },
    mounted() {
      this.getTodos()
    },
  };
</script>
