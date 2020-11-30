<template>
<div>

    <div class="row justify-content-md-center">

        <div class="col-4 p-5">

            <div class="row justify-content-between align-items-center">
                <h3 class="mb-0">Todo List</h3>
                <b-button variant="success" @click="onClickAdd">Add</b-button>
            </div>

        </div>

    </div>

    <div class="row">
        <div class="col">
        <b-card-group deck class="mt-3 my-grid">
        <b-card v-for="(todo) in todos" :key="todo.id" class="col-6 mb-3 p-0" header-tag="header">
            <template #header >

                <div class="row text-center">
                    <h3 class="mb-0 pl-3">{{ todo.title }}</h3>
                </div>

            </template>

            <div class="p-3">
                <h5>{{ todo.description }}</h5>
            </div>

            <div>
                <b-button variant="danger" class="float-right" @click="onHandleClickDelete(todo.id)">
                    Delete
                </b-button>

                <b-button variant="info" class="float-right" @click="onHandleClickUpdate(todo)">
                    Update
                </b-button>
            </div>

        </b-card>
        </b-card-group>
        <b-modal id="modal-add-todo" title="Add Todo" @hidden="onHandleCancel">

            <b-form>

                <b-form-group label="Todo:" description="Todo List Title">
                    <b-form-input v-model="form.title" placeholder="Add title"></b-form-input>
                </b-form-group>

                <b-form-group label="Description" description="Todo List Description">
                    <b-form-textarea id="textarea" v-model="form.description" placeholder="Add description.." rows="3" max-rows="6"></b-form-textarea>
                </b-form-group>

            </b-form>

            <template #modal-footer>

                <div class="w-100">
                    <b-button class="float-left" @click="onHandleCancel">
                        Close
                    </b-button>

                    <b-button variant="primary" class="float-right" @click="onHandleUpdate" v-if="form.id">
                        Update
                    </b-button>

                    <b-button variant="primary" class="float-right" @click="onHandleConfirm" v-else>
                        Confirm
                    </b-button>
                </div>

            </template>

        </b-modal>

       
        </div>

    </div>
    

</div>
</template>

<script>
// @ is an alias to /src

export default {
    name: "Home",
    data() {
        return {
            todos: [],
            form: {
                title: '',
                description: '',
                comments: ''
            }
        }
    },

    mounted() {
        this.getTodos()
    },

    methods: {
        getTodos() {
            this.$http.get('/todos')
                .then(res => {
                    this.todos = res.data
                })
        },

        onClickAdd() {
            this.$bvModal.show('modal-add-todo')
        },

        onHandleConfirm() {
            this.$http.post('/todos', this.form)
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo'),
                        this.getTodos()
                })

        },

        onHandleUpdate() {
            this.$http.patch(`/todos/${this.form.id}`, this.form)
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo'),
                        this.getTodos()
                })

        },

        onHandleCancel() {
            this.form = {
                title: '',
                description: ''
            }
            this.$bvModal.hide('modal-add-todo')
        },

        onHandleClickDelete(id) {
            this.$http.delete(`/todos/${id}`)
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo'),
                        this.getTodos()
                })
        },

        onHandleClickUpdate(todo) {
            this.form = todo
            this.$bvModal.show('modal-add-todo')

        },

    },
};
</script>

<style  scoped>
.my-grid {
  display: grid;
  justify-items: center;
  /* 280px is the minimum a column can get, you might need to adjust it based on your needs. */
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  
}

.my-grid > * {
  width: 100%;
  max-width: 20rem;
}
</style>
