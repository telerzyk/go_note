<template>
	<div class="container-fluid p-0 m-0 vh-100">
		<div class="row p-0 m-0 h-100">
			<div class="col-12 col-md-5 col-lg-4 bg-light p-0 h-100">
				<h3 class="w-100 text-center p-4">
					<i class="bi bi-clipboard-check text-secondary"></i>
					GoNote
				</h3>
				<button type="button" class="btn btn-dark w-100 py-2 rounded-0" v-on:click="onAddForm()">
					<i class="bi bi-pen-fill"></i>
					Stwórz nową notatkę
				</button>
				<notes-list v-bind:notes="notes" v-on:onShowNote="showNote($event)" v-on:onDeleteNote="deleteNote($event)"></notes-list>
			</div>
			<div class="col-12 col-md-7 col-lg-8 bg-white p-0">
				<div class="cover"></div>
				<div class="p-5 h-50">
					<note v-if="currentNote !== null" v-on:onDeleteNote="deleteNote($event)" v-bind:note="currentNote"></note>
					<note-add-form v-else-if="showAddForm" v-on:onAddNote="addNote($event)"></note-add-form>
					<div v-else class="alert alert-secondary rounded-5" role="alert">
						Brak aktywnej notatki
					</div>				
				</div>
			</div>
		</div>
		<nav class="navbar fixed-bottom navbar-dark bg-dark">
			<div class="container-fluid justify-content-end p-2">
				<span class="text-muted mx-5 d-none d-md-block">
					©2021 Polityka prywatności | Warunki korzystania z usługi
				</span>
				<span class="text-light">
					<i class="bi bi-heart-fill"></i>
					Moje konto
				</span>
			</div>
		</nav>
	</div>
</template>

<script>
	import NotesList from "./components/NotesList.vue";
	import NoteAddForm from "./components/NoteAddForm.vue";
	import Note from "./components/Note.vue";
	export default {
		components: {NotesList, NoteAddForm, Note},
		data: function (){
			return {
				notes: [],
				showAddForm: false,
				currentNote: null,
			}
		},
		methods: {
			addNote: function (note){
				var newNote = {
					name: note.name,
					content: note.content,
				};
				this.$http.post('notes', newNote).then((response) => {
					this.notes.push(response.data);
					this.showAddForm = false;
					this.currentNote = response.data;
				});
			},
			onAddForm: function (){
				this.currentNote = null;
				this.showAddForm = true;
			},
			deleteNote: function (note){
				this.$http.delete(`notes/${note.id}`).then(() => {
					this.showAddForm = false;
					this.currentNote = null;
					this.loadNotes();
				});
			},
			loadNotes: function () {
				this.$http.get('notes').then(response => {
					this.notes = response.data;
				});
			},
			showNote: function (note){
				this.showAddForm = false;
				this.currentNote = note;
			}
		},
		mounted() {
			this.$http.get('notes').then(response => {
				this.notes = response.data;
			});
		}
	}
</script>

<style>
	.cover {
	background-image: url("./assets/background.jpg");
	background-position: center;
	background-size: cover;
	min-height: 150px;
	}
</style>						