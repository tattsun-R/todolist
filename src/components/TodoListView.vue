<!--TodoListView.vue-->
<script setup>
    import { ref } from "vue";
    import { statuses } from "../const/statuses";
    
    let items = ref(JSON.parse(localStorage.getItem("items"))) || [];
    let inputContent = ref(); //タスクの内容
    let inputLimit = ref(); //タスクの期限
    let inputState = ref(); //タスクのステータス
    let errorMessage = ref('');
    
    function onEdit(id) {
        let isOnEditOther = false;
        items.value.map((item) => {
            if(item.onEdit) {
                isOnEditOther = true;
                return;
            }
        });
        if(isOnEditOther){
            errorMessage.value = "他に編集中のタスクがあります";
            isErrorMessage.value = true;
            return;
        }
        inputContent.value = items.value[id].content;
        inputLimit.value = items.value[id].limit;
        inputState.value = items.value[id].state;
        items.value[id].onEdit = true;
    }

    let isErrorMessage = ref(false);

    function onUpdate(id) {

        if(inputContent.value =="" || inputLimit.value =="") {
            errorMessage.value = 'タスクの内容と期限を入力してください。'
            isErrorMessage.value = true;
            return;
        }

        const newItem = {
            id: id,
            content: inputContent.value,
            limit: inputLimit.value,
            state: inputState.value,
            onEdit: false,
        };

        items.value.splice(id, 1, newItem);

        localStorage.setItem("items", JSON.stringify(items.value));

        isErrorMessage.value = false;
    }

    let isShowModal = ref(false);
    let deleteItemId = ''; //削除対象のItemのID
    let deleteItemContent = ref(); //削除対象のItemの内容

    function showDeleteModal() {
        isShowModal.value = true;
        deleteItemId = id;
        deleteItemContent.value = items.value[id].content;
    }

    
    function onDeleteItem() {
        items.value.splice(deleteItemId, 1);
        isShowModal.value = false;

        items.value = items.value.map((item, index) => ({
            id: index,
            contet: item.content,
            limit: item.limit,
            state: item.state,
            onEdit: item.onEdit,
        }));

        localStorage.setItem("items", JSON.stringify(items.value));
    }

    function onHideModal() {
        isShowModal.value = false;
    }
</script>

<template>
    <p v-if="isErrorMessage">{{ errorMessage }}</p>
    <tr v-for="item in items" :key="item.id">
        <td>{{ item.id }}</td>
        <td>
            <span v-if="!item.onEdit">{{ item.content }}</span>
            <input v-else v-model="inputContent" type="text" />
        </td>
        <td>
            <span v-if="!item.onEdit">{{ item.limit }}</span>
            <input v-else v-model="inputLimit" type="date" />
        </td>
        <td>
            <span v-if="!item.onEdit">{{ item.state.value }}</span>
            <select v-else v-model="inputState">
               <option
                v-for="state in statuses"
                :key="state.id"
                :value="state"
                :selected="state.id == item.state.id"
                >
                    {{ state.value }}
                </option> 
            </select>
        </td>
        <td>
            <button v-if="!item.onEdit" @click="onEdit(item.id)">編集</button>
            <button v-else @click="onUpdate(item.id)">完了</button>
        </td>
        <td><button @click="showDeleteModal()">削除</button></td>
    </tr>

    <div v-if="isShowModal" class="modal">
        <div class="modal-content">
            <p>{{ deleteItemContent }}を削除してもよろしいですか？</p>
            <button @click="onDeleteItem()">はい</button>
            <button @click="onHideModal()">キャンセル</button>
        </div>
    </div>
</template>