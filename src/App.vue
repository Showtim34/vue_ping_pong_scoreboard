<template>
    <div class="data">
        <div class="container">
            <div v-for="i in this.order">
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
                        <label class="form-label">Sets</label>
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
                <div class="col-3">
                        <button @click="changeOrder" class="btn btn-primary mt-4">Changement de coté</button>
                    </div>
            </div>

            <div class="card">
                <div class="card-body">
                    <h3>Aide</h3>
                    <p><i class="bi bi-arrow-left"></i></p>
                </div>
            </div>
            
        </div>
    </div>

    <div class="score">
        <div v-for="i in this.order">
            <div class="user-score joueur1" :style="{'background-color': this.j1color}" v-if="i == 1">
                <div class="user-name">{{ this.j1name }}</div>
                <div class="user-service">
                    <Transition>
                        <div v-if="this.service == 'j1'" class="service"></div>
                    </Transition>
                </div>
                <div class="user-set">{{ this.j1sets }}</div>
                <div class="user-point">{{ this.j1points }}</div>
            </div>
            <div class="user-score joueur2" :style="{'background-color': this.j2color}"  v-if="i == 2">
                <div class="user-name">{{ this.j2name }}</div>
                <div class="user-service">
                    <Transition>
                        <div v-if="this.service == 'j2'" class="service"></div>
                    </Transition>
                </div>
                <div class="user-set">{{ this.j2sets }}</div>
                <div class="user-point">{{ this.j2points }}</div>
            </div>
        </div>
    </div>
</template>


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

            service: "j1",
            ptsPerSet: 11,
            changeEvery: 2,
            setWinner: 5,

            gameStarted: false,

            order: [1,2]
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

            console.log(e.key)

        });
    },
    methods: {
        changeOrder() {
            if (this.order[0] == 1)
                this.order=[2,1]
            else
                this.order=[1,2]

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
    cursor: progress;
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
</style>
