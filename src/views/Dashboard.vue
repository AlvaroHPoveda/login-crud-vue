<template>
  <div>
    <Header />
    <div class="container" id="app">
      <h3>{{ title }}</h3>
      <input
        type="text"
        placeholder="Enter task"
        class="form-control my-3"
        v-model="newTask"
        v-on:keyup.enter="addTask"
      />

      <button class="btn btn-primary" @click="addTask">Add</button>
      <input
        @input="searchTasks"
        v-model="nameSearch"
        type="text"
        class="form-control-label mx-auto my-4 d-block"
        placeholder="Search..."
      />

      <div class="mt-3" v-for="(item, index) of tasks" :key="item.name">
        <div
          role="alert"
          :class="['alert', item.status ? 'alert-success' : 'alert-danger']"
        >
          <div class="d-flex justify-content-between align-items-center">
            <span v-if="inputUpdate && indexUpdate == index">
              <input v-model="nameUpdate" type="text" class="form-control" />
            </span>
            <span v-else> {{ index }} - {{ item.name }} </span>
            <div>
              <span v-if="inputUpdate && indexUpdate == index">
                <button
                  class="btn btn-secondary btn-sm"
                  @click="saveTask(index)"
                >
                  SAVE
                </button>
              </span>
              <button
                class="btn btn bg-success btn-sm text-white mx-2"
                @click="acceptTask(index)"
              >
                OK
              </button>
              <button
                class="btn btn bg-danger btn-sm text-white mx-2"
                @click="deleteTask(index)"
              >
                X
              </button>
              <button
                class="btn btn bg-warning btn-sm text-white mx-2"
                @click="seeInputUpdate(index)"
              >
                UPDATE
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer />
  </div>
</template>
<script>
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";

export default {
  components: {
    Header,
    Footer,
  },
  data() {
    return {
      title: "Tasks",
      tasks: [],
      newTask: "",
      indexUpdate: -1,
      nameUpdate: "",
      inputUpdate: false,
      nameSearch: "",
      originalTasks: [],
    };
  },
  methods: {
    addTask: function() {
      this.tasks.push({
        name: this.newTask,
        status: false,
      });
      this.newTask = "";
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
    acceptTask: function(index) {
      this.tasks[index].status = true;
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
    deleteTask: function(index) {
      this.tasks.splice(index, 1);
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
    seeInputUpdate: function(index) {
      this.indexUpdate = index;
      this.nameUpdate = this.tasks[index].name;
      this.inputUpdate = true;
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
    saveTask: function(index) {
      this.inputUpdate = false;
      this.tasks[index].name = this.nameUpdate;
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
    searchTasks: function() {
      if (this.nameSearch !== "") {
        let filterTasks = this.tasks.filter((item) => {
          return item.name
            .toLowerCase()
            .includes(this.nameSearch.toLowerCase());
        });
        this.tasks = filterTasks;
      } else {
        this.tasks = this.originalTasks;
      }
      localStorage.setItem("tasks-vue", JSON.stringify(this.tasks));
    },
  },
  created: function() {
    let dataDB = JSON.parse(localStorage.getItem("tasks-vue"));
    if (dataDB === null) {
      this.tasks = [];
      this.originalTasks = [];
    } else {
      this.tasks = dataDB;
      this.originalTasks = dataDB;
    }
  },
};
</script>
<style scoped>
#app {
  background-color: rgba(186, 218, 255, 255);
  margin: 2% auto;
  padding: 2%;
  border-radius: 10px;
}
</style>
