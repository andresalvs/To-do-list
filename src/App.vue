<template>
  <v-app>
    <v-toolbar dark color="black">
      <v-toolbar-title>To-Do List</v-toolbar-title>
    </v-toolbar>

    <v-container class="container pa-10">
      <!-- ToDo Input Section -->
      <v-card class="pa-4 mb-4">
        <v-text-field class="text-white" v-model="task" label="Add a new task" @keyup.enter="addTask" />
        <v-btn color="orange" @click="addTask">Add Task</v-btn>
      </v-card>

      <!-- In Progress Section -->
      <v-card class="pa-15 in-progress-background mb-4">
        <div class="text-center">
          <v-card class="in-progress-header" outlined>
            <v-card-text style="margin: 0; color: white;">In Progress</v-card-text>
          </v-card>
        </div>
        <v-list style="margin: 0; color: white; border-radius: 10px; background-color: #133;">
          <v-list-item-group>
            <v-list-item v-for="(task, index) in inProgressTasks" :key="index">
              <v-list-item-content>
                <v-list-item-title v-if="!isEditing[index]">
                  {{ index + 1 }}. {{ task }} <span class="task-time">{{ taskTimes[index] }}</span>
                </v-list-item-title>
                <v-text-field
                  v-else
                  v-model="editedTasks[index]"
                  @keyup.enter="updateTask(index)"
                  @blur="stopEditing(index)"
                  single-line
                  hide-details
                />
              </v-list-item-content>
              <v-list-item-action>
                <v-btn icon @click="completeTask(index)">
                  <v-icon>mdi-check</v-icon>
                </v-btn>
                <v-btn icon v-if="!isEditing[index]" @click="startEditing(index)">
                  <v-icon>mdi-pencil</v-icon>
                </v-btn>
                <v-btn v-if="isEditing[index]" @click="cancelEdit(index)">
                  Cancel
                </v-btn>
              </v-list-item-action>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-card>

      <!-- Completed Tasks Section -->
      <v-card class="pa-4 completed-background mb-4">
        <div class="text-center">
          <v-card class="completed-header" outlined>
            <v-card-text style="margin: 0; color: white;">Completed</v-card-text>
          </v-card>
        </div>
        <v-list style="margin: 0; color: white; border-radius: 10px; background-color: #555;">
          <v-list-item-group>
            <v-list-item v-for="(task, index) in completedTasks" :key="index">
              <v-list-item-content>
                <v-list-item-title>{{ task }}</v-list-item-title>
              </v-list-item-content>
              <v-list-item-action>
                <v-btn icon @click="removeTask(index)">
                  <v-icon>mdi-delete</v-icon>
                </v-btn>
              </v-list-item-action>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-card>
    </v-container>
  </v-app>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';

export default {
  setup() {
    const task = ref('');
    const inProgressTasks = reactive([]);
    const completedTasks = reactive([]);
    const isEditing = reactive([]);
    const editedTasks = reactive([]);
    const taskTimes = reactive([]); // Array to store the times tasks are added

    const addTask = () => {
      if (task.value.trim()) {
        inProgressTasks.push(task.value.trim());
        taskTimes.push(new Date().toLocaleTimeString()); // Store the current time
        task.value = '';
        initializeTasks();
      }
    };

    const completeTask = (index) => {
      const task = inProgressTasks[index];
      inProgressTasks.splice(index, 1);
      completedTasks.push(task);
      taskTimes.splice(index, 1); // Remove the associated time
      initializeTasks();
    };

    const removeTask = (index) => {
      completedTasks.splice(index, 1);
    };

    const startEditing = (index) => {
      isEditing[index] = true;
      editedTasks[index] = inProgressTasks[index];
    };

    const stopEditing = (index) => {
      isEditing[index] = false;
    };

    const updateTask = (index) => {
      if (editedTasks[index] !== inProgressTasks[index]) {
        inProgressTasks[index] = editedTasks[index];
      }
      stopEditing(index);
    };

    const cancelEdit = (index) => {
      editedTasks[index] = inProgressTasks[index];
      stopEditing(index);
    };

    const initializeTasks = () => {
      isEditing.length = inProgressTasks.length;
      editedTasks.length = inProgressTasks.length;
      isEditing.fill(false);
      for (let i = 0; i < inProgressTasks.length; i++) {
        editedTasks[i] = inProgressTasks[i];
      }
    };

    onMounted(() => {
      initializeTasks();
    });

    return {
      task,
      inProgressTasks,
      completedTasks,
      isEditing,
      editedTasks,
      taskTimes, // Expose taskTimes
      addTask,
      completeTask,
      removeTask,
      startEditing,
      stopEditing,
      updateTask,
      cancelEdit,
    };
  }
};
</script>

<style scoped>
.container-background {
  background: linear-gradient(to bottom, rgba(0, 0, 0, 0.8), rgba(255, 165, 0, 0.5));
  color: white;
  min-height: 100vh;
}

.in-progress-background {
  background: linear-gradient(to right, #00b09b, #156fa6); /* Green to Blue gradient */
  color: white;
}

.completed-background {
  background: linear-gradient(to right, #ff7e5f, #feb47b); /* Orange to Black gradient */
  color: white;
}

.in-progress-header, .completed-header {
  font-weight: bold;
  font-size: 20px;
  border-radius: 10px;
  display: inline-block;
  padding: 4px 8px;
  margin-bottom: 16px;
  border: 2px solid orange;
}

.task-time {
  font-size: 0.8em;
  color: #ccc; /* Lighter color for time */
}

.v-btn {
  background-color: orange !important;
}

.v-card {
  background-color: rgba(68, 44, 223, 0.7);
  border-radius: 10px;
}
</style>
