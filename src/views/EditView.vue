<script setup>
import { useTodoStore } from '@/stores/todo';
import { useRoute, RouterLink } from 'vue-router';
import { onMounted, ref } from 'vue';
import Loading from '../components/Loading.vue'

const todoStore = useTodoStore()
const route = useRoute()
const isLoaded = ref(false)
const todoId = ref(-1)

onMounted(async () => {
	isLoaded.value = false
	await todoStore.loadTodo(route.params.id)
	todoId.value = parseInt(route.params.id)
	isLoaded.value = true
})

const editTodo = async (selectedTodo, todoId) => {
	isLoaded.value = false
	const bodyData = {
		name: selectedTodo.name,
		status: selectedTodo.status
	}
	await todoStore.editTodo(bodyData, todoId)
	isLoaded.value = true
}

</script>

<template>
	<div class="flex justify-center items-center flex-col">
		Edit Todo

		<div v-if="isLoaded" class="w-1/2 mx-auto">
			<label class="form-control w-full">
				<div class="label">
					<span class="label-text">Name</span>
				</div>
				<input type="text" placeholder="Type here" class="input input-bordered w-full"
					v-model="todoStore.selectedTodo.name" />
			</label>

			<label class="form-control w-full">
				<div class="label">
					<span class="label-text">Status</span>
				</div>
				<select class="select select-bordered w-full" v-model="todoStore.selectedTodo.status">
					<option>select status</option>
					<option v-for="status in todoStore.statuses" :value="status">
						{{ status }}
					</option>
				</select>
			</label>
			<div class="flex justify-between mt-4">
				<button class="btn" @click="editTodo(todoStore.selectedTodo, todoId)">Edit</button>
				<RouterLink class="btn" :to="{ name: 'todo-list' }">
					Back
				</RouterLink>
			</div>

		</div>
		<div v-else>
			<Loading></Loading>
		</div>

	</div>
</template>