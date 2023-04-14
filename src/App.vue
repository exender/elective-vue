<!-- eslint-disable vue/require-v-for-key -->
<template>
  <div>
    <h1 style="color: #007bff">Ma Todo List</h1>

    <!-- Formulaire d'ajout de tâches -->
    <form @submit.prevent="addTask" style="margin-bottom: 16px">
      <label for="new-task">Ajouter une tâche :</label>
      <input
        type="text"
        id="new-task"
        v-model="newTask"
        style="margin-right: 8px"
      />
      <label for="time-imp">Temps imparti :</label>
      <input
        type="number"
        id="time-imp"
        v-model="newTimeImp"
        min="0"
        style="margin-right: 8px"
      />
      <label for="responsible">Responsable :</label>
      <select v-model="taskResponsible" style="margin-right: 8px">
        <option value="">Assigné à</option>
        <option v-for="name in fakeNames" :value="name">{{ name }}</option>
      </select>
      <button
        type="submit"
        style="
          background-color: #007bff;
          color: #fff;
          border: none;
          padding: 8px;
          cursor: pointer;
        "
      >
        Ajouter
      </button>
    </form>

    <!-- Liste de tâches -->
    <!-- Liste de tâches -->
<ul>
  <li v-for="(task, index) in filteredTasks" :key="index">
    <!-- Bouton pour marquer une tâche comme terminée -->
    <button
      @click="toggleCompleted(task)"
      :class="{ completed: task.completed }"
    >
      {{ task.completed ? 'Non terminée' : 'Terminée' }}
    </button>

    <!-- Champ de texte pour éditer la tâche -->
    <input type="text" v-model="task.text" @blur="updateTask(task)" />

    <!-- Champ de texte pour assigner une personne à la tâche -->
    <!-- Liste déroulante pour assigner une personne à la tâche -->
    <select v-model="task.assignedTo">
      <option value="">Assigné à</option>
      <option v-for="name in fakeNames" :value="name">{{ name }}</option>
    </select>

    <!-- Champ de texte pour le temps imparti de la tâche -->
    <input
      type="number"
      v-model="task.timeImp"
      @blur="updateTask(task)"
      min="0"
    />

    <!-- Bouton pour supprimer la tâche -->
    <button @click="deleteTask(index)">Supprimer</button>
    <!-- Checkbox pour activer/désactiver la suppression des tâches sélectionnées -->
    <input
      type="checkbox"
      v-model="task.selected"
      @change="onTaskSelectionChange(task)"
    />
  </li>
</ul>


    <!-- Bouton pour supprimer les tâches sélectionnées -->
    <button @click="deleteSelectedTasks">
      Supprimer les tâches sélectionnées
    </button>

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
      <p>Temps total de toutes les tâches : {{ totalTime }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      deleteMode: false,
      tasks: [],
      newTask: "",
      newTimeImp: "",
      selectedTasks: [],
      currentFilter: "all",
      fakeNames: ["John", "Jane", "Alice", "Bob", "Charlie"],
      taskResponsible: "", // Ajouter cette ligne pour le responsable de la tâche
    };
  },

  computed: {
    // Filtrer les tâches en fonction du filtre sélectionné
    filteredTasks() {
      if (this.currentFilter === "completed") {
        return this.tasks.filter((task) => task.completed);
      } else if (this.currentFilter === "uncompleted") {
        return this.tasks.filter((task) => !task.completed);
      } else {
        return this.tasks;
      }
    },
    // Calculer le temps total de toutes les tâches
    totalTime() {
      return this.tasks.reduce(
        (total, task) => total + parseInt(task.timeImp || 0),
        0
      );
    },
  },
  methods: {
    // Ajouter une tâche
    addTask() {
      if (this.newTask.trim() && this.newTimeImp && this.taskResponsible) {
        this.tasks.push({
          text: this.newTask,
          timeImp: this.newTimeImp,
          completed: false,
          assignedTo: this.taskResponsible,
        });
        this.newTask = "";
        this.newTimeImp = "";
        this.taskResponsible = "";
      } else {
        // Afficher un message d'erreur si les champs sont vides
        alert(
          "Veuillez renseigner le nom de la tâche, le temps imparti et le responsable."
        );
      }
    },
    // Supprimer une tâche
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    onTaskSelectionChange(task) {
      if (task.selected) {
        this.selectedTasks.push(task);
      } else {
        this.selectedTasks = this.selectedTasks.filter((t) => t !== task);
      }
    },
    // Supprimer les tâches sélectionnées
    deleteSelectedTasks() {
      // Créer un tableau pour stocker les index des tâches à supprimer
      const indexesToDelete = [];
      // Parcourir le tableau des tâches
      for (let i = 0; i < this.tasks.length; i++) {
        // Vérifier si la case à cocher est cochée
        if (this.tasks[i].selected) {
          // Ajouter l'index de la tâche à supprimer dans le tableau
          indexesToDelete.push(i);
        }
      }
      // Supprimer les tâches en partant de la fin du tableau pour éviter les décalages d'index
      for (let i = indexesToDelete.length - 1; i >= 0; i--) {
        this.tasks.splice(indexesToDelete[i], 1);
      }
      // Réinitialiser le tableau des tâches sélectionnées
      this.selectedTasks = [];
      // Réinitialiser le mode de suppression
      this.deleteMode = false;
    },
    // Mettre à jour une tâche
    updateTask(task) {
      // Vérifier si le champ texte de la tâche est vide
      if (!task.text.trim()) {
        alert("Le champ texte ne peut pas être vide");
        return;
      }

      // Vérifier si le champ temps imparti de la tâche est vide
      if (!task.timeImp) {
        alert("Le champ temps imparti ne peut pas être vide");
        return;
      }

      // Mettre à jour la tâche
      task.timeImp = parseInt(task.timeImp);
    },
    markAsCompleted(task) {
      task.completed = true;
    },

  },
};
</script>

<style scoped>
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
  background-color: #ff0000;
  color: #fff;
  border: none;
  padding: 8px;
  cursor: pointer;
}

#filter {
  margin-top: 16px;
  padding: 8px;
  border: 1px solid #ccc;
}

p {
  margin: 8px 0;
}
.completed {
  background-color: #28a745;
}
</style>
