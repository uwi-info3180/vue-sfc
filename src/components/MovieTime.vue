<script setup>
import { ref, onMounted } from 'vue';
import MovieCard from '@/components/MovieCard.vue';

const alertSuccessClass = 'alert-success';
const alertErrorClass = 'alert-danger';

let message = ref("Hello VueJS SFC!")
let flashMessage = ref('')
let displayFlash = ref(false);
let isSuccess = ref(false);
let movieTitle =  ref('');
let movieDescription = ref('');
let movieRating =  ref(1);
let editMode = ref(false);
let currentlyEditing = ref(null);
let addMovieModal = null;


const movies = ref([
    {
        title: 'The Lord of the Rings: The Fellowship of the Ring',
        description: 'A meek Hobbit from the Shire and eight companions set out on a journey to destroy the powerful One Ring and save Middle-earth from the Dark Lord Sauron.',
        rating: 5
    },
    {
        title: 'The Bourne Identity',
        description: 'A man is picked up by a fishing boat, bullet-riddled and suffering from amnesia, before racing to elude assassins and regain his memory.',
        rating: 4
    },
    {
        title: 'Wonder Woman',
        description: 'When a pilot crashes and tells of conflict in the outside world, Diana, an Amazonian warrior in training, leaves home to fight a war, discovering her full powers and true destiny.',
        rating: 5
    },
    {
        title: 'Black Panther',
        description: "T'Challa, heir to the hidden but advanced kingdom of Wakanda, must step forward to lead his people into a new future and must confront a challenger from his country's past.",
        rating: 5
    }
]);

onMounted(() => {
    addMovieModal = bootstrap.Modal.getOrCreateInstance(document.querySelector('#addMovieModal'));
});

function addMovie() {
    disableEditing();

    let movie = {
        title: movieTitle.value,
        description: movieDescription.value,
        rating: parseInt(movieRating.value)
    };
    // console.log(movie);
    movies.value.push(movie);
    clearFormFields();

    displayFlash.value = true;
    isSuccess.value = true;
    flashMessage.value = 'Movie added successfully!';

    // Hide Bootstrap Modal
    addMovieModal.hide();

    // let self = this;
    setTimeout(function() {
        displayFlash.value = false;
        // console.log(self.displayFlash);
    }, 3000);
}

function editMovie(index) {
    let movie = movies.value[index];
    enableEditing();

    movieTitle.value = movie.title;
    movieDescription.value = movie.description;
    movieRating.value = parseInt(movie.rating);
    currentlyEditing.value = index;

    // Show Bootstrap Modal
    addMovieModal.show();
}

function updateMovie() {
    let movie = {
        title: movieTitle.value,
        description: movieDescription.value,
        rating: parseInt(movieRating.value)
    };
    // console.log(movie);
    movies.value[currentlyEditing.value] = movie;

    clearFormFields();
    currentlyEditing.value = null;

    displayFlash.value = true;
    isSuccess.value = true;
    flashMessage.value = 'Movie updated successfully!';

    // Hide Bootstrap Modal
    addMovieModal.hide();
}

function displayModal() {
    clearFormFields();
    disableEditing();

    // Show modal
    addMovieModal.show();
}

function removeMovie(index) {
    displayFlash.value = true;
    isSuccess.value = false;
    flashMessage.value = 'Movie deleted!';
    movies.value.splice(index, 1);

    setTimeout(function() {
        displayFlash.value = false;
    }, 3000);
}

function enableEditing() {
    editMode.value = true;
}

function disableEditing() {
    editMode.value = false;
}

function clearFormFields() {
    movieTitle.value = '';
    movieDescription.value = '';
    movieRating.value = 1;
}
</script>

<template>
    <section class="jumbotron p-5 mb-4 bg-light rounded-3 text-center">
        <h1>Movie Time</h1>
        <p>{{ message }}</p>
        <button class="btn btn-primary btn-lg" @click="displayModal">Add a Movie</button>
    </section>

    <transition name="fade">
        <div v-if="displayFlash" v-bind:class="[isSuccess ? alertSuccessClass : alertErrorClass]" class="alert">
            {{ flashMessage }}
        </div>
    </transition>

    <section class="movies">
        <div v-if="!movies.length" class="no-movies">
            You haven't added any movies.
        </div>
        <!-- <div v-for="(movie, index) in movies" class="card">
            <svg class="bd-placeholder-img card-img-top" width="100%" height="180" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Placeholder: Image cap" preserveAspectRatio="xMidYMid slice" focusable="false"><title>Placeholder</title><rect width="100%" height="100%" fill="#868e96"></rect><text x="50%" y="50%" fill="#dee2e6" dy=".3em">Image</text></svg>
            <div class="card-body">
                <h5 class="card-title">{{ movie.title }}</h5>
                <p class="card-text">{{ movie.description }}</p>
                <p class="card-text text-center text-muted">
                    <img src="@/assets/star.svg" alt="star" v-for="n in movie.rating" /> <br><small>({{ movie.rating + '/5' }} {{ (movie.rating > 1) ? 'stars' : 'star' }})</small>
                </p>
            </div>
            <div class="card-footer text-muted d-flex">
                <button @click="editMovie(index)" class="btn btn-primary btn-sm text-right"><img src="@/assets/pencil.svg" alt=""> Edit</button>
                <button @click="removeMovie(index)" class="btn btn-danger btn-sm text-right"><img src="@/assets/trashcan.svg" alt=""> Remove</button>
            </div>
        </div> -->
        <MovieCard v-for="(movie, index) in movies"
                    v-bind:movie="movie"
                    v-bind:key="index"
                    v-on:edit="editMovie(index)"
                    v-on:remove="removeMovie(index)" />
    </section>

    <div id="addMovieModal" class="modal fade" tabindex="-1" role="dialog" aria-labelledby="modalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalLabel"> {{ editMode ? 'Edit' : 'Add New' }} Movie</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <form action="" method="post">
                        <div class="form-group">
                            <label for="movie-title">Movie Title</label>
                            <input type="text" v-model="movieTitle" name="movie_title" id="movie-title" class="form-control" />
                        </div>
                        <div class="form-group">
                            <label for="movie-title">Description</label>
                            <textarea v-model="movieDescription" name="movie_description" id="movie-description" cols="30" rows="10" class="form-control"></textarea>
                        </div>
                        <div class="form-group">
                            <label for="movie-rating">Rating</label>
                            <input type="number" v-model="movieRating" min="1" max="5" step="1" name="movie_rating" id="movie-rating" class="form-control" />
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" v-if="!editMode" v-on:click="addMovie" class="btn btn-primary"><img src="@/assets/plus.svg" />  Add Movie</button>
                    <button type="button" v-else v-on:click="updateMovie" class="btn btn-primary"><img src="@/assets/pencil.svg" />  Update</button>
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css?family=Bangers|Open+Sans:400,400i,700,700i');

body {
    /* margin-top: 70px; */
    /* background: linear-gradient(rgba(27, 6, 94, 1), rgba(255, 71, 218, 1)) */
    font-family: 'Open Sans';
}

header {
    margin-bottom: 20px;
}

.jumbotron {
    background: linear-gradient(rgba(27, 6, 94, 1), rgba(255, 71, 218, 1));
    color: white;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.8);
}

.jumbotron h1 {
    font-family: 'Bangers';
    font-size: 56px;
    transform: scale(1.0);
    transition: all 0.5s ease-out;
}

.jumbotron h1:hover {
    transform: scale(1.2);
    /* font-size: 72px; */
    transition: all 0.5s ease-out;
}

.movies {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    grid-gap: 20px;
}

.movies .no-movies {
    font-size: 32px;
    font-weight: bold;
    text-align: center;
}

.card {
    box-shadow: none;
    transition: all 0.3s ease-in-out;
}

.card:hover {
    box-shadow: 0 3px 5px rgba(0, 0, 0, 0.2);
    transition: all 0.3s ease-in-out;
    /* border: 1px solid rgba(151, 151, 151, 0.3); */
}

.bd-placeholder-img {
    font-size: 1.125rem;
    text-anchor: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
}

.card-title {
    font-weight: bold;
}

footer {
    margin-top: 20px;
    background: #444444;
    padding: 25px 0;
    color: white;
}

.fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
}

.card-footer.d-flex {
    justify-content: space-between;
}

.card-footer {
    /* background: rgba(27, 6, 94, 1); */
}
</style>