<template>
    <div>

        <ul>
            <li v-for="(task, index) in tasks" :key="index">
                {{ task }}
                <button @click="deleteTask(index)">Supprimer</button>
            </li>
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
    },
    methods: {
        deleteTask(index) {
            this.$emit('deleteTask', index)
        }
    }
}
</script>
