<template>
	<div class="container">
		<h3 class="mb-3">Table Of Note</h3>
		<table class="table">
			<thead>
				<tr>
					<th>Title</th>
					<th>Subject</th>
					<th>Published</th>
					<th>Action</th>
				</tr>
			</thead>
			<tbody>
				<tr v-for="note in notes" :key="note.slug">
					<td><router-link :to="{ name: 'notes.show', params: {noteSlug: note.slug} }"> {{ note.title }} </router-link></td>
					<td> {{ note.subject }} </td>
					<td> {{ note.published }} </td>
					<td> 
						<router-link :to="{ name: 'notes.edit', params: {noteSlug: note.slug} }" class="btn-text text-primary">Edit</router-link> 
						<DeleteNote :endpoint="note.slug" />	
					</td>
				</tr>
			</tbody>
		</table>
	</div>
</template>

<script>
import DeleteNote from './Delete'
export default {
	components: {
		'DeleteNote': DeleteNote
	},
	data () {
		return {
			notes: [],
		}
	},

	mounted() {
		this.getNotes()
	},

	methods: {
		async getNotes() {
			let response = await axios.get('/api/notes')
			this.notes = response.data.data
		}
	}
}
</script>