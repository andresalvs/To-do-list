<template>
  <v-app>
    <v-toolbar dark color="indigo">
  <v-toolbar-title class="title">
    <v-icon left>mdi-format-list-checks</v-icon> <!-- Icon added -->
    To-Do List
  </v-toolbar-title>
</v-toolbar>


    <v-container class="container pa-4">
      <!-- ToDo Input Section -->
      <v-card class="input-card mb-4">
        <v-card-title>
          <span class="headline">Add New Task</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="taskName" label="Task Name" outlined dense />
          <v-text-field
            v-model="taskDescription"
            label="Task Description"
            multiline
            outlined
            dense
          />
        </v-card-text>
        <v-card-actions>
          <v-btn class="highlighted-button" @click="addTask" elevation="2">Add Task</v-btn>
        </v-card-actions>
      </v-card>

      <!-- In Progress Section -->
      <v-card class="in-progress-card mb-4">
        <v-card-title>
          <span class="headline">In Progress</span>
          <v-spacer></v-spacer>
        </v-card-title>

        <v-card-text>
          <v-row>
            <v-col v-for="(task, index) in inProgressTasks" :key="index" cols="12" sm="6" md="4">
              <v-card class="task-item">
                <v-card-title>
                  <strong @click="toggleCompletion(index)" :class="{ 'line-through': task.completed }">
                    {{ index + 1 }}. {{ task.name }}
                  </strong>
                </v-card-title>
                <v-card-text>
                  <div class="task-description">{{ task.description }}</div>
                  <div class="task-time">{{ task.time }}</div>
                </v-card-text>
                <v-card-actions>
                  <v-btn icon @click="startEditing(index)" v-if="!isEditing[index]">
                    <v-icon>mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn icon @click="completeTask(index)" v-if="!task.completed">
                    <v-icon>mdi-check</v-icon>
                  </v-btn>
                  <v-btn v-if="isEditing[index]" @click="cancelEdit(index)">Cancel</v-btn>
                </v-card-actions>
                <v-text-field
                  v-if="isEditing[index]"
                  v-model="editedTasks[index]"
                  @keyup.enter="updateTask(index)"
                  @blur="stopEditing(index)"
                  single-line
                  hide-details
                  outlined
                  dense
                />
              </v-card>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-container>
  </v-app>
</template>



<script>
import { ref, reactive, onMounted } from 'vue';

export default {
  setup() {
    const taskName = ref('');
    const taskDescription = ref('');
    const inProgressTasks = reactive([]);
    const isEditing = reactive([]);
    const editedTasks = reactive([]);

    const addTask = () => {
      if (taskName.value.trim()) {
        inProgressTasks.push({
          name: taskName.value.trim(),
          description: taskDescription.value.trim(),
          completed: false,
          time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
        });
        taskName.value = '';
        taskDescription.value = '';
        initializeTasks();
      }
    };

    const completeTask = (index) => {
      inProgressTasks.splice(index, 1);
      initializeTasks();
    };

    const startEditing = (index) => {
      isEditing[index] = true;
      editedTasks[index] = inProgressTasks[index].name;
    };

    const stopEditing = (index) => {
      isEditing[index] = false;
    };

    const updateTask = (index) => {
      if (editedTasks[index] !== inProgressTasks[index].name) {
        inProgressTasks[index].name = editedTasks[index];
      }
      stopEditing(index);
    };

    const cancelEdit = (index) => {
      editedTasks[index] = inProgressTasks[index].name;
      stopEditing(index);
    };

    const initializeTasks = () => {
      isEditing.length = inProgressTasks.length;
      editedTasks.length = inProgressTasks.length;
      isEditing.fill(false);
    };

    const toggleCompletion = (index) => {
      inProgressTasks[index].completed = !inProgressTasks[index].completed;
    };

    onMounted(() => {
      initializeTasks();
    });

    return {
      taskName,
      taskDescription,
      inProgressTasks,
      isEditing,
      editedTasks,
      addTask,
      completeTask,
      startEditing,
      stopEditing,
      updateTask,
      cancelEdit,
      toggleCompletion,
    };
  },
};
</script>


<style scoped>
.container {
  background: url('./assets/background.jpg') no-repeat center center; 
  background-size: cover; 
  color: #34495E; 
  min-height: 100vh;
  font-family: 'Arial', sans-serif;
  position: relative;
  padding: 16px; /* Add some padding to the container */
}

.input-card {
  background: white;
  border-radius: 8px; 
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1); 
  padding: 16px;
}

.in-progress-card {
  background: #ECF0F1; 
  border-radius: 8px;
}

/* Maintain the card's responsiveness */
.task-item {
  background: white;
  border-radius: 16px; 
  padding: 10px;
  margin-bottom: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

/* Additional styles for mobile-friendly text size */
.task-description {
  font-weight: normal; 
  color: #7f8c8d;
}

.task-time {
  font-size: 0.85em; 
  color: #95a5a6;
}

/* Make headlines slightly smaller on mobile */
.headline {
  font-weight: bold;
  font-size: 1.1em; /* Adjust this as needed */
}

.v-btn {
  color: #2980B9; 
}

@media (max-width: 600px) {
  .headline {
    font-size: 1em; /* Smaller headline for mobile */
  }

  .task-item {
    padding: 8px; /* Adjust padding for smaller devices */
  }
}
</style>
