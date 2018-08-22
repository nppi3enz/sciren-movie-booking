<template>
    <div class="box">
        <h3 class="title">Movie : {{ movieId }}</h3>
        <p>Count: {{ status.count }}</p>
        <p>Price: {{ status.price }}</p>
        <movie @chooseMovie="handleChooseMovie" :movieId="movieId" />
        <seat 
            :movieId="movieId" 
            @chooseSeat="handleChooseSeat"
            :selectSeats="selectSeats"
            :firebaseSeats="firebaseSeats"
             /> <!-- ":movieId" is send props -->
    </div>
</template>

<script>
import firebase from 'firebase'
import _ from 'lodash'

import Movie from 'Components/Movie.vue'
import Seat from 'Components/Seat.vue'
import { pushToArray } from 'Others/lib.js'

const config = {  
    databaseURL: "https://playground-9cef4.firebaseio.com"
}

firebase.initializeApp(config)

const db = firebase.database()

export default{
    components: { Movie, Seat },
    data() {
        return {
            movieId: '',
            selectSeats: [],
            firebaseSeats: [],
            status: { count: 0, price: 0 }
        }
    },
    methods: {
        handleChooseMovie(movieId) {
            if (this.status.count) {
                if (confirm('Data will be lost?')) {
                    //clear status
                    this.status = { count:0, price:0 }
                    this.selectSeats = []
                } else {
                    return false;
                }
            }
            this.movieId = movieId

            const movieRef = db.ref('/').child(this.movieId) 
            //check onChange data in firebase
            movieRef.on('value', snapshot => {
                // console.log(snapshot.val())
                const seats = snapshot.val()

                this.firebaseSeats = [] //clear old data
                _.forOwn(seats, s => {
                    pushToArray(s, this.firebaseSeats)
                })

                // console.log(this.firebaseSeats.length)

            })           
        },
        handleChooseSeat(seat){
            // const ids = this.selectSeats.map( s => s.id )
            // const idx = ids.indexOf(seat.id)
            
            // if(idx === -1) {
            //     this.selectSeats.push(seat)
            // } else {
            //     this.selectSeats.splice(idx, 1)
            // }
            // console.log(this.selectSeat.length)
            // console.log(ids)
            pushToArray(seat, this.selectSeats)

            const movieRef = db.ref('/').child(this.movieId)
            movieRef.push(seat)

            this.status = this.selectSeats.reduce((summary, s) => {
                summary.count++
                summary.price += s.price
                return summary
            }, { count: 0, price: 0 })
        }
            
    }
}
</script>