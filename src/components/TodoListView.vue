<!--TodoListView.vue-->
<script setup>
    const items = JSON.parse(localStorage.getItem("items")) || [];
</script>

<template>
    <tr v-for="item in items" :key="item.id">
        <td>{{ item.id }}</td>
        <td>{{ item.content }}</td>
        <td>{{ item.limit }}</td>
        <td>{{ item.state.value }}</td>
        <td><button>編集</button></td>
        <td><button>削除</button></td>
    </tr>
</template>