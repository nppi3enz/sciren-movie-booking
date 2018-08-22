<template>
    <div class="box">
        <h3 class="title">{{movieId}}</h3>
        <div class="columns">
            <div v-for="m in movies" :class="className(m.id)" @click="chooseMovie(m.id)">
                <figure class="images">
                    <img :src="imgSrc(m.image)">
                </figure>
                
            </div>
        </div>
    </div>
</template>

<script>
import { movies } from 'Others/movie.json'

export default{
    props: [ 'movieId' ],
    data() {
        return {
            movies
        }
    },
    methods: {
        imgSrc(image) {
            return `/movies/${ image }.png`
        },
        chooseMovie(movieId){
            this.$emit('chooseMovie', movieId)
        },
        className(movieId){
            //return object
            return [
                'column', 'pointer', {'chosen': this.movieId === movieId}
            ]
        }
    },
    mounted() {
        this.chooseMovie(movies[0].id)
    }
}
</script>

<style>
.pointer{
    cursor: pointer;
}
.chosen{
    border:1px solid black;
}
</style>