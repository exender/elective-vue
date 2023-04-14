<template>
    <div>
        <h2>Liste des tâches :</h2>
        <ul>
            <li v-for="(task, index) in tasks" :key="index">{{ task }}</li>
        </ul>
    </div>
</template>

<script>
export default {
    props: {
        tasks: {
            type: Array,
            required: true
        }
    }
}
</script>
