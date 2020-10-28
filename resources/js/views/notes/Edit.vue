<template>
    <div class="container">
        <div class="row">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">New Note</div>
                    <div class="card-body">
                        <form action="#" method="post" @submit.prevent="update"> 
                            <div class="form-group">
                                <label for="title">Title</label>
                                <input type="text" v-model="form.title" id="title" class="form-control">
                                <div v-if="messageErrors.title" class="mt-2 text-danger">{{ messageErrors.title[0] }}</div>
                            </div>

                            <div class="form-group">
                                <label for="subject">Subject</label>
                                <select @change="selectedSubject" name="subject" id="subject" class="form-control">
                                    <option :value="form.subject_id" v-text="form.subject"></option>
                                    <template v-for="subject in subjects">
                                        <option v-if="form.subject_id !== subject.id" :key="subject.id" :value="subject.id">
                                            {{ subject.name }}
                                        </option>
                                    </template>    
                                </select>
                                <div v-if="messageErrors.subject" class="mt-2 text-danger">{{ messageErrors.subject[0] }}</div>
                            </div>

                            <div class="form-group">
                                <label for="description">Description</label>
                                <textarea v-model="form.description" name="description" id="description" rows="5" class="form-control"></textarea>    
                                <div v-if="messageErrors.description" class="mt-2 text-danger">{{ messageErrors.description[0] }}</div>
                            </div> 

                            <button class="btn btn-primary d-flex align-items-center" type="submit">
                                Update
                                <template v-if="loading">
                                    <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: none; display: block; shape-rendering: auto;" width="30px" height="20px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid"> <rect x="17.5" y="30" width="15" height="40" fill="#e3e5ea"> <animate attributeName="y" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="18;30;30" keySplines="0 0.5 0.5 1;0 0.5 0.5 1" begin="-0.2s"></animate> <animate attributeName="height" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="64;40;40" keySplines="0 0.5 0.5 1;0 0.5 0.5 1" begin="-0.2s"></animate> </rect> <rect x="42.5" y="30" width="15" height="40" fill="#cfdbf2"> <animate attributeName="y" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="20.999999999999996;30;30" keySplines="0 0.5 0.5 1;0 0.5 0.5 1" begin="-0.1s"></animate> <animate attributeName="height" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="58.00000000000001;40;40" keySplines="0 0.5 0.5 1;0 0.5 0.5 1" begin="-0.1s"></animate> </rect> <rect x="67.5" y="30" width="15" height="40" fill="#cde0ff"> <animate attributeName="y" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="20.999999999999996;30;30" keySplines="0 0.5 0.5 1;0 0.5 0.5 1"></animate> <animate attributeName="height" repeatCount="indefinite" dur="1s" calcMode="spline" keyTimes="0;0.5;1" values="58.00000000000001;40;40" keySplines="0 0.5 0.5 1;0 0.5 0.5 1"></animate> </rect> </svg>
                                </template>
                            </button>
                        </form>  
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data () {
        return {
            form: [],
            subjects: [],
            messageErrors: [],
            selected: '',
            loading: false
        }
    },

    mounted () {
        this.getSubjects()
        this.getNote()
    },

    methods: {
        async getSubjects() {
            let response = await axios.get('/api/subjects')
            if (response.status == 200) {
                this.subjects = response.data
            }
        },

        async getNote() {
            let response = await axios.get(`/api/notes/${this.$route.params.noteSlug}`)
            this.form = response.data.data
        },

        selectedSubject(e) {
            this.selected = e.target.value
        },

        async update() {
            this.loading = true
            this.form['subject'] = this.selected || this.form.subject_id
            try {
                let response = await axios.patch(`/api/notes/${this.$route.params.noteSlug}/edit`, this.form)
                if (response.status == 200) {
                    this.$toasted.show(response.data.message, {
                        type: 'success',
                        duration: 3000
                    })
                    this.loading = false
                this.$router.push('/notes/table')
                }
            } catch(e) {
                this.loading = false
                this.messageErrors = e.response.data.errors
            }
        }
    }
}
</script>