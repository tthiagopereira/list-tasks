<template>
	<div id="app">
		<h1>Tarefas</h1>
    <TaskProgress :progress="progress"/>
    <NewTask @taskAdded="addTask"/>
    <TasksGrid
        @taskDeleted="deleteTask"
        @taskStateChanged="toggleTaskState"
        :tasks="tasks"/>
	</div>
</template>

<script>
import TasksGrid from "./components/TasksGrid";
import NewTask from "./components/NewTask";
import TaskProgress from "./components/TaskProgress";
export default {
  components: { TasksGrid, NewTask, TaskProgress },
  data() {
    return {
      tasks: []
    }
  },
  computed: {
    progress() {
      const total = this.tasks.length
      const done = this.tasks.filter(t => !t.pending).length
      return Math.round(done / total * 100) || 0
    },
  },
  watch: {
    tasks: {
      deep: true,
      handler() {
        localStorage.setItem('tasks', JSON.stringify(this.tasks))
      }
    }
  },
  created() {
    const json = localStorage.getItem('tasks')
    this.tasks = JSON.parse(json) || []
  },
  methods: {
    addTask(task) {
      const sameName = t => t.name === task.name
      const reallNew = this.tasks.filter(sameName).length == 0
      if (reallNew) {
        this.tasks.push({
          name: task.name,
          pending: task.pending || true
        })
      }else{
        alert('Nome da tarefa já existente')
      }
    },
    deleteTask(i) {
      this.tasks.splice(i,1)
    },
    toggleTaskState(i) {
      this.tasks[i].pending = !this.tasks[i].pending
    }
  }
}
</script>

<style>
	body {
		font-family: 'Lato', sans-serif;
		background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
		color: #FFF;
	}

	#app {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	#app h1 {
		margin-bottom: 5px;
		font-weight: 300;
		font-size: 3rem;
	}
</style>
