<template>
	<div class="container">
		<h1> {{ note.title }} </h1>
		<hr>
		<div class="mb-3">
			<small class="text-muted"> {{ note.published }} - {{ note.subject }} </small>
		</div>
		<p> {{ note.description }} </p>
	</div>
</template>

<script>
export default {
	data () {
		return {
			note: []
		}
	},

	mounted () {
		this.getNote()
	},

	methods: {
		async getNote() {
			let response = await axios.get('/api/notes/' + this.$route.params.noteSlug)
			this.note = response.data.data
		}
	}
}
</script>