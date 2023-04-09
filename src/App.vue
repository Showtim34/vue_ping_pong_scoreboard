<script setup>
/*import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
https://openclassrooms.com/fr/courses/1603881-creez-votre-site-web-avec-html5-et-css3/8061410-decouvrez-les-bases-de-css-grids
*/
</script>

<script>
export default {
    data() {
        return {
            j1name: 'Charlotte lutz',
            j2name: 'elena zacharia',

            j1sets: 0,
            j2sets: 0,

            j1points: 0,
            j2points: 0,

            j1color: '#454457',
            j2color: '#45aa77',

            j1winner: false,
            j2winner: false,

            j1looser: false,
            j2looser: false,

            tm1:false,
            tm2: false,

            service: "j1",
            ptsPerSet: 11,
            changeEvery: 2,
            setWinner: 3,

            gameStarted: false,

            playerOrder: [1,2],

            resume: ''
        }
    },
    watch: {
        gameStarted() {
            if (this.gameStarted) {
                this.j1points = 0
                this.j1sets = 0
                this.j2points = 0
                this.j2sets = 0

                this.j1winner = false
                this.j2looser = false
                this.j2winner = false
                this.j1looser = false

                this.tm1 = false
                this.tm2 = false
                this.resume = ''
            }

        },
        j1points() {
            if (this.j1points < 0) {
                this.j1points = 0;
            }
            if (this.j1points >= this.ptsPerSet && (this.j1points - this.j2points >= 2)) {
                this.resume = this.resume + ' | ' + '<strong>' + this.j1points + '</strong>' + ' - ' + this.j2points
                this.j1sets++
                this.j1points = 0;
                this.j2points = 0;
            }
            this.checkService()

            if (this.j1sets == this.setWinner) {
                this.j1winner = true
                this.j2looser = true
                this.gameStarted = false
            }

        },
        j2points() {
            if (this.j2points < 0) {
                this.j2points = 0;
            }

            if (this.j2points >= this.ptsPerSet && (this.j2points - this.j1points >= 2)) {
                this.resume = this.resume + ' | ' + this.j1points + ' - ' + '<strong>' + this.j2points + '</strong>'
                this.j2sets++
                this.j1points = 0;
                this.j2points = 0;
            }
            this.checkService()

            if (this.j2sets == this.setWinner) {
                this.j2winner = true
                this.j1looser = true

                this.gameStarted = false
            }
        } 
    },
    created() {
        window.addEventListener('keydown', (e) => {

            if (e.key == 'Escape') {
                this.gameStarted = false
            }

            if (e.key == 'Enter') {
                this.gameStarted = true
            }

            if (! this.gameStarted)
                return;

            if (e.key == 'ArrowUp') {
                this.j1points++;
            }

            if (e.key == 'ArrowLeft') {
                this.j1points--;
            }

            if (e.key == 'ArrowDown') {
                this.j2points++;
            }
            if (e.key == 'ArrowRight') {
                this.j2points--;
            }

        });
    },
    methods: {
        changeOrder() {
            if (this.playerOrder[0] == 1)
                this.playerOrder=[2,1]
            else
                this.playerOrder=[1,2]
        },
        checkService() {
            let total = this.j1points + this.j2points
            if (total > 20 || total % this.changeEvery == 0) {
                this.service = (this.service == 'j1') ? 'j2' : 'j1'
            }
       },

       setFinish() {
            if (confirm("Terminer le match ?")) {
                this.gameStarted = false
            }
       },
       tm(i) {
            if (i == 1) this.tm1 = true
            if (i == 2) this.tm2 = true
       }
    }
}
</script>

<style scoped>
.data {
    background: lightblue;
    padding: 3rem;
}
.label {font-weight: bold}


.score {
    cursor: none;
    margin: 3rem;
    width: 500px;
    height: 100px;
    position: fixed;
    bottom: 0;
    left: 0;
    font-weight: bold !important;
    font-size: 30px;
    color: #fff;
    line-height: 1;
    
}

.score .joueur1 {
    background: orange;
    margin-bottom: 5px;
}

.score .joueur2 {
    background: green;
    margin-bottom: 5px;
}

.score .user-score {
    /*height: 40px;*/
    display: grid;
    grid-template-columns: 370px 30px 50px 50px;
    grid-template-rows: auto;
    grid-column-gap: 0px;
    grid-row-gap: 0px;
    border-radius: 5px;
}

.user-name {
    grid-area: 1 / 1 / 2 / 1;
    text-align: left;
    padding: 5px;
    text-transform: uppercase
}

.user-service {
    grid-area: 1 / 2 / 2 / 2;
    text-align: left;
    padding: 5px
}

.user-set {
    grid-area: 1 / 3 / 2 / 3;
    background: #fff;
    color: #000;
    text-align: center;
    padding: 5px
}

.user-point {
    grid-area: 1 / 4 / 2 / 4;
    color: #000;
    text-align: center;
    padding: 5px
}

.service {
    width: 15px;
    height: 15px;
    background: #fff;
    border-radius: 50px;
    margin-top: 5px
}

.tm {
    position: absolute; 
    top: .8rem;
    right: .5rem;
    font-size: 0.8rem;
    font-weight: bold;;
}

.resume {
    font-size: 0.9rem;
    color: #444;
}
</style>

<template>
    <div class="data">
        <div class="container">
            <div v-for="i in playerOrder">
                <div class="row" v-if="i==1">
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">JOUEUR 1 :</label>
                            <input type="email" class="form-control" v-model="j1name">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">SET joueur 1 :</label>
                            <input v-model="j1sets" type="number" class="form-control">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">Points 1 :</label>
                            <input type="number" class="form-control" v-model="j1points">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">Couleur joueur 1 :</label>
                            <input type="text" class="form-control" v-model="j1color">
                        </div>
                    </div>
                </div>
                <div class="row" v-if="i==2">
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">JOUEUR 2 :</label>
                            <input type="email" class="form-control" v-model="j2name">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">SET joueur 2 :</label>
                            <input v-model="j2sets" type="number" class="form-control">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">Points 2 :</label>
                            <input type="number" class="form-control" v-model="j2points">
                        </div>
                    </div>
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">Couleur joueur 2 :</label>
                            <input type="text" class="form-control" v-model="j2color">
                        </div>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-12">
                    <h1>Match</h1>
                </div>
                <div class="col-3">
                    <div class="mb-3">
                        <label class="form-label">Service :</label>
                        <select v-model="service" class="form-control">
                            <option value="j1">Joueur 1 : {{ this.j1name }}</option>
                            <option value="j2">Joueur 2 : {{ this.j2name }}</option>
                        </select>
                    </div>
                </div>
                <div class="col-2">
                    <div class="mb-3">
                        <label class="form-label">Points / sets</label>
                        <input type="text" class="form-control" v-model="ptsPerSet">
                    </div>
                </div>
                <div class="col-2">
                    <div class="mb-3">
                        <label class="form-label">Sets gagnants</label>
                        <input type="text" class="form-control" v-model="setWinner">
                    </div>
                </div>
                <div class="col-2">
                    <div class="mb-3">
                        <label class="form-label">Chang. service</label>
                        <input type="text" class="form-control" v-model="changeEvery">
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-3" v-if="gameStarted == false">
                    <button @click="this.gameStarted = true" class="btn btn-success mt-3">Commencer le match</button>
                </div>
                <div class="col-3" v-if="this.gameStarted == true">
                    <button @click="this.setFinish()" class="btn btn-danger mt-3">Terminer le match</button>
                </div>
                <div class="col-2">
                    <button @click="changeOrder" class="btn btn-primary mt-3">Chang. de coté</button>
                </div>
                <div class="col-1">
                    <button @click="this.tm(1)" class="btn btn-primary mt-3">TM1</button>
                </div>
                <div class="col-1">
                    <button @click="this.tm(2)" class="btn btn-primary mt-3">TM2</button>
                </div>
            </div>
            <div class="card mt-4">
                <div class="card-body">
                    <p>
                        <span style="margin-right: 1rem;">Joueur 1</span>
                        <i class="bi bi-arrow-up-square-fill"></i>
                        <span style="display: inline-block; margin-right: 1rem;">&nbsp;+1</span>
                        <i class="bi bi-arrow-left-square-fill"></i> -1<br>
                        <span style="margin-right: 1rem;">Joueur 2</span>
                        <i class="bi bi-arrow-down-square-fill"></i>
                        <span style="display: inline-block; margin-right: 1rem;">&nbsp;+1</span>
                        <i class="bi bi-arrow-right-square-fill"></i> -1<br>
                    </p>
                </div>
            </div>
            
        </div>
    </div>

    <div class="score">
        <div v-for="i in playerOrder">
            <div class="user-score joueur1" :style="{'background-color': this.j1color}" v-if="i == 1">
                <div class="user-name">
                    <i class="bi bi-trophy" v-if="this.j1winner"></i>
                    <i class="bi bi-emoji-dizzy" v-if="this.j1looser"></i>
                     {{ this.j1name }}
                    <span class="tm" v-if="this.tm1">T</span>
                </div>

                <div class="user-service">
                    <Transition>
                        <div v-if="this.service == 'j1'" class="service"></div>
                    </Transition>
                </div>
                <div class="user-set">{{ this.j1sets }}</div>
                <div class="user-point">{{ this.j1points }}</div>
            </div>
            <div class="user-score joueur2" :style="{'background-color': this.j2color}"  v-if="i == 2">
                <div class="user-name">
                    <i class="bi bi-trophy" v-if="this.j2winner"></i>
                    <i class="bi bi-emoji-dizzy" v-if="this.j2looser"></i>
                     {{ this.j2name }}
                     <span class="tm" v-if="this.tm2">T</span>
                </div>
                <div class="user-service">
                    <Transition>
                        <div v-if="this.service == 'j2'" class="service"></div>
                    </Transition>
                </div>
                <div class="user-set">{{ this.j2sets }}</div>
                <div class="user-point">{{ this.j2points }}</div>
            </div>
        </div>
        <div class="resume" v-html="this.resume"></div>
    </div>
</template>
