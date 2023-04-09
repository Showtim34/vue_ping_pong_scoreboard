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

            j1tmpName: '',
            j2tmpName: '',
            j1name: 'Charlotte lutz',
            j2name: 'elena zacharia',

            j1sets: 0,
            j2sets: 0,

            j1points: 0,
            j2points: 0,

            j1Logo: null,
            j2Logo: null,

            j1color: '#454457',
            j2color: '#45aa77',

            pointColor: '#ffffff',
            setColor: '#ffffff',
            setBackground: '#000000',
            nameColor: '#ffffff',

            serviceColor: '#ffffff',

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
                this.j1name = this.j1tmpName
                this.j2name = this.j2tmpName
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
        } ,

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
            if (i == 1) this.tm1 = ! this.tm1
            if (i == 2) this.tm2 = ! this.tm2
       },
       setlogo1(event) {
            const files = event.target.files
            console.log(files)
            let filename = files[0].name
            const fileReader = new FileReader()
            fileReader.addEventListener('load', () => {
                this.j1Logo = fileReader.result
            })
            fileReader.readAsDataURL(files[0])
        },
        setlogo2(event) {
            const files = event.target.files
            let filename = files[0].name
            const fileReader = new FileReader()
            fileReader.addEventListener('load', () => {
                this.j2Logo = fileReader.result
            })
            fileReader.readAsDataURL(files[0])
        }
    }
}
</script>

<style scoped>
.data {
    background: #999999;
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
    padding: 5px 1px 5px 8px;
    text-transform: uppercase;
    font-size: 1.8rem;
}
.long { font-size: 1rem; padding-top: 0rem; line-height: 1; display: inline-block; display: inline-block; vertical-align: 0.2rem;}



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
    right: .2rem;
    font-size: 0.8rem;
    font-weight: bold;;
}

.resume {
    font-size: 0.9rem;
    color: #444;
    position: fixed; top: 50px; right: 50px;
    ;
}
h1 {font-size: 1.5rem;}

.logo {position: fixed; left: 3px; width:42px; }
.logo.j1 {
    bottom: 107px;
    
}
.logo.j2 {
    bottom: 60px;
    
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
                            <input type="email" class="form-control" v-model="j1tmpName">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">SET :</label>
                            <input v-model="j1sets" type="number" class="form-control">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Points :</label>
                            <input type="number" class="form-control" v-model="j1points">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Couleur :</label>
                            <input type="color" class="form-control" v-model="j1color">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Logo :</label>
                            <input type="file" class="form-control" @change="setlogo1" accept="image/*" v-if="!j1Logo">
                            <div class="btn btn-sm btn-danger" @click="j1Logo = null" v-else>Supprimer</div>
                        </div>
                    </div>
                </div>
                <div class="row" v-if="i==2">
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">JOUEUR 2 :</label>
                            <input type="email" class="form-control" v-model="j2tmpName">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">SET :</label>
                            <input v-model="j2sets" type="number" class="form-control">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Points :</label>
                            <input type="number" class="form-control" v-model="j2points">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Couleur</label>
                            <input type="color" class="form-control" v-model="j2color">
                        </div>
                    </div>
                    <div class="col-1">
                        <div class="mb-3">
                            <label class="form-label">Logo :</label>
                            <input type="file" class="form-control" @change="setlogo2" accept="image/*" v-if="!j2Logo">
                            <div class="btn btn-sm btn-danger" @click="j2Logo = null" v-else>Supprimer</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-12">
                    <h1>Match</h1>
                </div>
                <div class="col-2">
                    <div class="mb-3">
                        <label class="form-label">Service :</label>
                        <select v-model="service" class="form-control">
                            <option value="j1">J1: {{ j1name }}</option>
                            <option value="j2">J2: {{ j2name }}</option>
                        </select>
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Points / sets</label>
                        <input type="text" class="form-control" v-model="ptsPerSet">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Sets gagn.</label>
                        <input type="text" class="form-control" v-model="setWinner">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Chang. serv.</label>
                        <input type="text" class="form-control" v-model="changeEvery">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Nom</label>
                        <input type="color" class="form-control" v-model="nameColor">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Point</label>
                        <input type="color" class="form-control " v-model="pointColor">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Set</label>
                        <input type="color" class="form-control" v-model="setColor">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Set Fond</label>
                        <input type="color" class="form-control" v-model="setBackground">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Service</label>
                        <input type="color" class="form-control" v-model="serviceColor">
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-3" v-if="gameStarted == false">
                    <button @click="gameStarted = true" class="btn btn-success mt-3">Commencer le match</button>
                </div>
                <div class="col-3" v-if="gameStarted == true">
                    <button @click="setFinish()" class="btn btn-danger mt-3">Terminer le match</button>
                </div>
                <div class="col-2">
                    <button @click="changeOrder" class="btn btn-primary mt-3">Chang. de coté</button>
                </div>
                <div class="col-1">
                    <button @click="tm(1)" class="btn btn-primary mt-3">TM1</button>
                </div>
                <div class="col-1">
                    <button @click="tm(2)" class="btn btn-primary mt-3">TM2</button>
                </div>
            </div>
            <img src="./assets/touche.png" style="width: 300px; position: absolute; right: 0; top: 190px">
            <img src="./assets/scoreboard.png" style="width: 150px; position: fixed; left: 10px; top: 30px">
        </div>
    </div>

    <div class="score">
        <div v-for="i in playerOrder">
            <div class="user-score joueur1" :style="{'background-color': j1color}" v-if="i == 1">
                <div class="user-name">
                    <i class="bi bi-trophy" v-if="j1winner"></i>
                    <span :style="{'color': nameColor}" v-bind:class="{'long': (j1name.length > 18)}">{{ j1name }}</span>
                    <span class="tm" v-if="tm1" :style="{'color': nameColor}">T</span>
                </div>

                <div class="user-service">
                    <Transition>
                        <div v-if="service == 'j1'" class="service" :style="{'background': serviceColor}"></div>
                    </Transition>
                </div>
                <div class="user-set" :style="{'color': setColor,'background': setBackground}">{{ j1sets }}</div>
                <div class="user-point" :style="{'color': pointColor}">{{ j1points }}</div>
            </div>
            <div class="user-score joueur2" :style="{'background-color': j2color}"  v-if="i == 2">
                <div class="user-name" >
                    <i class="bi bi-trophy" v-if="j2winner"></i>
                     <span :style="{'color': nameColor}" v-bind:class="{'long': (j2name.length > 24)}">{{ j2name }}</span>
                     <span class="tm" v-if="tm2" :style="{'color': nameColor}">T</span>
                </div>
                <div class="user-service">
                    <Transition>
                        <div v-if="service == 'j2'" class="service" :style="{'background': serviceColor}"></div>
                    </Transition>
                </div>
                <div class="user-set" :style="{'color': setColor, 'background': setBackground}">{{ j2sets }}</div>
                <div class="user-point" :style="{'color': pointColor}">{{ j2points }}</div>
            </div>
        </div>
        <div class="resume" v-html="resume"></div>
        <img :src="j1Logo" class="logo j1" v-if="j1Logo">
        <img :src="j2Logo" class="logo j2" v-if="j2Logo">
    </div>
</template>
