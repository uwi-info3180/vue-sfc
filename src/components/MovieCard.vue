<template>
    <div class="card">
        <svg class="bd-placeholder-img card-img-top" width="100%" height="180" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Placeholder: Image cap" preserveAspectRatio="xMidYMid slice" focusable="false"><title>Placeholder</title><rect width="100%" height="100%" fill="#868e96"></rect><text x="50%" y="50%" fill="#dee2e6" dy=".3em">Image</text></svg>
        <div class="card-body">
            <h5 class="card-title">{{ movie.title }}</h5>
            <p class="card-text">
                {{ movie.description }}
            </p>
            <p class="card-text text-center text-muted">
                <img src="@/assets/star.svg" alt="star" v-for="n in movie.rating" /> <br><small>({{ movie.rating + '/5' }} {{ (movie.rating > 1) ? 'stars' : 'star' }})</small>
            </p>
        </div>
        <div class="card-footer text-muted d-flex">
            <button @click="editMovie" class="btn btn-primary btn-sm text-right"><img src="@/assets/pencil.svg" alt=""> Edit</button>
            <button @click="removeMovie" class="btn btn-danger btn-sm text-right"><img src="@/assets/trashcan.svg" alt=""> Remove</button>
        </div>
    </div>
</template>

<script>
export default {
    props: ['movie'],
    data() {
        return {
        }
    },
    methods: {
        editMovie() {
            this.$emit('edit');
        },
        removeMovie() {
            this.$emit('remove');
        }
    }
}
</script>