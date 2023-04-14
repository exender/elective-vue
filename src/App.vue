

<style scoped>
/* Style pour la liste de tâches */
ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

input[type="checkbox"] {
  margin-right: 8px;
}

input[type="text"] {
  flex: 1;
  padding: 8px;
  border: none;
  border-bottom: 1px solid #ccc;
}

select {
  margin-left: 8px;
}

button {
  margin-left: 8px;
  background-color: #007bff;
  color: #fff;
  border: none;
  padding: 8px;
  cursor: pointer;
}

/* Style pour le filtre */
#filter {
  margin-top: 16px;
  padding: 8px;
  border: 1px solid #ccc;
}

/* Style pour le compteur de tâches */
p {
  margin: 8px 0;
}
</style>
<!-- eslint-disable vue/require-v-for-key -->
<template>
  <div>
    <h1 style="color: #007bff;">Ma Todo List</h1>

<!-- Formulaire d'ajout de tâches -->
<form @submit.prevent="addTask" style="margin-bottom: 16px;">
  <label for="new-task">Ajouter une tâche :</label>
  <input type="text" id="new-task" v-model="newTask" style="margin-right: 8px;">
  <select v-model="taskResponsible" style="margin-right: 8px;">
    <option value="">Assigné à</option>
    <option v-for="name in fakeNames" :value="name">{{ name }}</option>
  </select>
  <button type="submit" style="background-color: #007bff; color: #fff; border: none; padding: 8px; cursor: pointer;">Ajouter</button>
  <!-- Afficher un message d'erreur si le nom de la tâche ou le responsable n'est pas renseigné -->
</form>

<!-- Liste de tâches -->
<ul>
  <li v-for="(task, index) in filteredTasks" :key="index">
    <!-- Checkbox pour sélectionner/désélectionner la tâche -->
    <input type="checkbox" v-model="task.completed" @click="updateTask(task)">

    <!-- Champ de texte pour éditer la tâche -->
    <input type="text" v-model="task.text" @blur="updateTask(task)">

    <!-- Champ de texte pour assigner une personne à la tâche -->
    <!-- Liste déroulante pour assigner une personne à la tâche -->
    <select v-model="task.assignedTo">
      <option value="">Assigné à</option>
      <option v-for="name in fakeNames" :value="name">{{ name }}</option>
    </select>

    <!-- Bouton pour supprimer la tâche -->
    <button @click="deleteTask(index)">Supprimer</button>

    {{ task.text }}
  </li>
</ul>



      <!-- Bouton pour supprimer les tâches sélectionnées -->
      <button @click="deleteSelectedTasks">Supprimer les tâches sélectionnées</button>

      <!-- Filtres -->
      <div>
          <label for="filter">Filtre :</label>
          <select id="filter" v-model="currentFilter">
              <option value="all">Toutes</option>
              <option value="completed">Terminées</option>
              <option value="uncompleted">Non terminées</option>
          </select>
      </div>

      <!-- Compteur de tâches -->
      <div>
          <p>Nombre de tâches : {{ tasks.length }}</p>
          <p>Nombre de tâches sélectionnées : {{ selectedTasks.length }}</p>
      </div>
  </div>
</template>

<script>
export default {
 data() {
  return {
    tasks: [
      { text: 'Tâche 1', completed: false, assignedTo: '' },
      { text: 'Tâche 2', completed: true, assignedTo: '' },
      { text: 'Tâche 3', completed: false, assignedTo: '' }
    ],
    newTask: '',
    selectedTasks: [],
    currentFilter: 'all',
    fakeNames: ['John', 'Jane', 'Alice', 'Bob', 'Charlie'],
    taskResponsible: '' // Ajouter cette ligne pour le responsable de la tâche
  }
},



  computed: {
      // Filtrer les tâches en fonction du filtre sélectionné
      filteredTasks() {
          if (this.currentFilter === 'completed') {
              return this.tasks.filter(task => task.completed)
          } else if (this.currentFilter === 'uncompleted') {
              return this.tasks.filter(task => !task.completed)
          } else {
              return this.tasks
          }
      }
  },
  methods: {
      // Ajouter une tâche
    // Ajouter une tâche
    addTask() {
  if (this.newTask.trim() && this.taskResponsible) {
    this.tasks.push({ text: this.newTask, completed: false, assignedTo: this.taskResponsible });
    this.newTask = '';
    this.taskResponsible = '';
  } else {
    // Afficher un message d'erreur si les champs sont vides
    alert('Veuillez renseigner le nom de la tâche et le responsable.');
  }
},
      // Supprimer une tâche
      deleteTask(index) {
          this.tasks.splice(index, 1)
      },
      // Supprimer les tâches sélectionnées
      deleteSelectedTasks() {
          this.selectedTasks.sort().reverse().forEach(index => {
              this.tasks.splice(index, 1)
          })
          this.selectedTasks = []
      },
      // Mettre à jour une tâche
      updateTask(task) {
    // Mettre à jour la tâche dans la liste des tâches
    this.tasks.splice(this.tasks.indexOf(task), 1, task);
  }
}
}
</script>