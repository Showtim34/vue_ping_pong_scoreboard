<script setup>
/*
  - Vous pouvez mettre vos imports ou composants ici si besoin.
*/
</script>

<script>
export default {
    data() {
        return {
            pass: '',
            accepted: (window.location.port == '5173') ? true : false,

            // -------------------------------------------
            // Ajout d'une propriété isMaster
            // Pour l'exemple, on la force à 'true' si on est en 5173 (dev local)
            // Mais vous pouvez aussi la gérer manuellement :
            //   isMaster: true / false
            // ou ajouter un checkbox dans le template.
            // -------------------------------------------
            isMaster: (window.location.port == '5173') ? true : false,

            j1name: (window.location.port == '5173') ? 'UCHAUD (ARTHUR/EMMA)' : 'Joueur 1',
            j2name: (window.location.port == '5173') ? 'UCHAUD (LUCAS/MAEL)' : 'Joueur 2',

            tournementName: '',
            tournementColor: '#fda815',

            fontSize: '12',

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

            tm1: false,
            tm2: false,

            service: "j1",
            ptsPerSet: 11,
            changeEvery: 2,
            setWinner: 3,

            gameStarted: false,

            playerOrder: [1, 2],

            resume: ''
        }
    },
    watch: {
        // --- On conserve tes watchers existants pour la logique du score ---
        pass() {
            if (this.pass === 'vivelepingpong') {
                this.accepted = true
            }
        },
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
                this.j1points = 0
            }
            if (this.j1points >= this.ptsPerSet && (this.j1points - this.j2points >= 2)) {
                this.resume = this.resume + ' | ' + '<strong>' + this.j1points + '</strong>' + ' - ' + this.j2points
                this.j1sets++
                this.j1points = 0
                this.j2points = 0
            }
            this.checkService()
            if (this.j1sets === this.setWinner) {
                this.j1winner = true
                this.j2looser = true
                this.gameStarted = false
            }
        },
        j2points() {
            if (this.j2points < 0) {
                this.j2points = 0
            }
            if (this.j2points >= this.ptsPerSet && (this.j2points - this.j1points >= 2)) {
                this.resume = this.resume + ' | ' + this.j1points + ' - ' + '<strong>' + this.j2points + '</strong>'
                this.j2sets++
                this.j1points = 0
                this.j2points = 0
            }
            this.checkService()
            if (this.j2sets === this.setWinner) {
                this.j2winner = true
                this.j1looser = true
                this.gameStarted = false
            }
        },

        // ----------------------------------------------------------------
        // WATCH GLOBAL : on sauvegarde les data, UNIQUEMENT si isMaster==true
        // ----------------------------------------------------------------
        $data: {
            handler(val) {
                if (!this.isMaster) return // si on est "slave", on ne sauvegarde pas

                // Sinon, on sauvegarde toutes les données
                localStorage.setItem('pingpong-data', JSON.stringify(val))
            },
            deep: true
        }
    },
    created() {
        // -- 1) On initialise depuis localStorage s'il y a déjà des données --
        const storedData = localStorage.getItem('pingpong-data')
        if (storedData) {
            try {
                const parsed = JSON.parse(storedData)
                // On assigne chaque propriété
                Object.keys(parsed).forEach(key => {
                    this[key] = parsed[key]
                })
            } catch (err) {
                console.warn("Impossible de parser les données du localStorage :", err)
            }
        }

        // -- 2) On écoute l'événement 'storage' pour détecter les changements dans les autres onglets --
        window.addEventListener('storage', (event) => {
            if (event.key === 'pingpong-data') {
                try {
                    const newData = JSON.parse(event.newValue)
                    // On met à jour toutes les clés locales
                    Object.keys(newData).forEach(key => {
                        this[key] = newData[key]
                    })
                } catch (err) {
                    console.warn("Impossible de parser la nouvelle valeur du localStorage :", err)
                }
            }
        })

        // -- 3) On conserve ton listener existant pour la gestion du score via le clavier --
        window.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') {
                this.gameStarted = false
            }
            if (e.key === 'Enter') {
                this.gameStarted = true
            }
            if (!this.gameStarted) return

            if (e.key === 'ArrowUp') {
                this.j1points++
            }
            if (e.key === 'ArrowLeft') {
                this.j1points--
            }
            if (e.key === 'ArrowDown') {
                this.j2points++
            }
            if (e.key === 'ArrowRight') {
                this.j2points--
            }
        })
    },
    methods: {
        // Bouton "Reset Storage" :
        resetStorage() {
            if (confirm("Voulez-vous vraiment réinitialiser tout le localStorage ?")) {
                localStorage.removeItem('pingpong-data')
                // On peut recharger la page pour repartir à zéro
                location.reload()
            }
        },
        changeOrder() {
            if (this.playerOrder[0] === 1) {
                this.playerOrder = [2, 1]
            } else {
                this.playerOrder = [1, 2]
            }
        },
        checkService() {
            let total = this.j1points + this.j2points
            // On alterne le service tous les 'changeEvery' points ou si total > 20
            if (total > 20 || total % this.changeEvery === 0) {
                this.service = (this.service === 'j1') ? 'j2' : 'j1'
            }
        },
        setFinish() {
            if (confirm("Terminer le match ?")) {
                this.gameStarted = false
            }
        },
        tm(i) {
            if (i === 1) this.tm1 = !this.tm1
            if (i === 2) this.tm2 = !this.tm2
        },
        setlogo1(event) {
            const files = event.target.files
            if (!files || !files[0]) return
            const fileReader = new FileReader()
            fileReader.addEventListener('load', () => {
                this.j1Logo = fileReader.result
            })
            fileReader.readAsDataURL(files[0])
        },
        setlogo2(event) {
            const files = event.target.files
            if (!files || !files[0]) return
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
body {
    cursor: none;
}
input[type="color"] {
    height: 38px;
}
.data {
    background: #999999;
    padding: 3rem;
    cursor: pointer;
}
.label {
    font-weight: bold;
}
.score {
    margin: 3rem;
    width: 500px;
    height: 100px;
    margin: 200px auto;
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
    display: grid;
    grid-template-columns: 370px 30px 50px 50px;
    grid-template-rows: auto;
    border-radius: 5px;
}
.user-name {
    grid-area: 1 / 1 / 2 / 1;
    text-align: left;
    padding: 5px 1px 5px 8px;
    text-transform: uppercase;
    font-size: 1.8rem;
}
.long {
    font-size: 1rem;
    padding-top: 0rem;
    line-height: 1;
    display: inline-block;
    vertical-align: 0.2rem;
}
.user-service {
    grid-area: 1 / 2 / 2 / 2;
    text-align: left;
    padding: 5px;
}
.user-set {
    grid-area: 1 / 3 / 2 / 3;
    background: #fff;
    color: #000;
    text-align: center;
    padding: 5px;
}
.user-point {
    grid-area: 1 / 4 / 2 / 4;
    color: #000;
    text-align: center;
    padding: 5px;
}
.service {
    width: 15px;
    height: 15px;
    background: #fff;
    border-radius: 50px;
    margin-top: 5px;
}
.tm {
    position: absolute;
    top: .8rem;
    right: .2rem;
    font-size: 0.8rem;
    font-weight: bold;
}
.resume {
    font-size: 0.9rem;
    color: #444;
    position: fixed;
    top: 50px;
    right: 50px;
}
h1 {
    font-size: 1.5rem;
}
.logo {
    position: absolute;
    left: -50px;
    width: 42px;
}
.logo.j1 {
    top: -2px;
}
.logo.j2 {
    bottom: 0px;
}
.tournement {
    border-radius: 5px;
    margin-bottom: 5px;
    text-align: center;
    padding-bottom: 5px;
}
.tournement span {
    font-weight: bold;
}
</style>

<template>
    <div v-if="!accepted" style="cursor: pointer; padding: 200px; text-align: center;">
        <input
            type="text"
            focus
            v-model="pass"
            class="form-control m-auto"
            style="max-width: 220px; margin-left: 150px"
            placeholder="mot de passe"
        >
    </div>

    <div class="data" v-if="accepted">
        <div class="container">
            <!-- Exemple d'affichage pour la propriété isMaster (facultatif) -->
            <div class="row">
                <div class="col-3">
                    <label>Est Master ?</label>
                    <input type="checkbox" v-model="isMaster" style="margin-left: 10px;">
                </div>
            </div>

            <div class="row">
                <div class="col-3">
                    <div class="mb-3">
                        <label class="form-label">Nom du tournoi :</label>
                        <input type="email" class="form-control" v-model="tournementName">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Couleur</label>
                        <input type="color" class="form-control" v-model="tournementColor">
                    </div>
                </div>
                <div class="col-1">
                    <div class="mb-3">
                        <label class="form-label">Taille font :</label>
                        <input type="number" class="form-control" v-model="fontSize">
                    </div>
                </div>
            </div>

            <!-- On itère sur playerOrder pour déterminer qui est J1 / J2 -->
            <div v-for="i in playerOrder" :key="i">
                <div class="row" v-if="i == 1">
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">JOUEUR 1 :</label>
                            <input type="email" class="form-control" v-model="j1name">
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
                    <div class="col-2">
                        <div class="mb-3">
                            <label class="form-label">Logo joueur 1 :</label>
                            <input
                                type="file"
                                class="form-control"
                                @change="setlogo1"
                                accept="image/*"
                                v-if="!j1Logo"
                            >
                            <div
                                class="btn btn-sm btn-danger"
                                @click="j1Logo = null"
                                v-else
                            >
                                Supprimer
                            </div>
                        </div>
                    </div>
                </div>

                <div class="row" v-if="i == 2">
                    <div class="col-3">
                        <div class="mb-3">
                            <label class="form-label">JOUEUR 2 :</label>
                            <input type="email" class="form-control" v-model="j2name">
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
                    <div class="col-2">
                        <div class="mb-3">
                            <label class="form-label">Logo joueur 2 :</label>
                            <input
                                type="file"
                                class="form-control"
                                @change="setlogo2"
                                accept="image/*"
                                v-if="!j2Logo"
                            >
                            <div
                                class="btn btn-sm btn-danger"
                                @click="j2Logo = null"
                                v-else
                            >
                                Supprimer
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Configuration du match -->
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
                        <input type="color" class="form-control" v-model="pointColor">
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

            <!-- Boutons d'action -->
            <div class="row">
                <div class="col-2" v-if="gameStarted == false">
                    <button @click="gameStarted = true" class="btn btn-success mt-3">
                        Commencer le match
                    </button>
                </div>
                <div class="col-2" v-if="gameStarted == true">
                    <button @click="setFinish()" class="btn btn-danger mt-3">
                        Terminer le match
                    </button>
                </div>
                <div class="col-2">
                    <button @click="changeOrder" class="btn btn-primary mt-3">
                        Changement de coté
                    </button>
                </div>
                <div class="col-1">
                    <button @click="tm(1)" class="btn btn-primary mt-3">
                        TM1
                    </button>
                </div>
                <div class="col-1">
                    <button @click="tm(2)" class="btn btn-primary mt-3">
                        TM2
                    </button>
                </div>
                <!-- NOUVEAU bouton RESET à côté de TM2 -->
                <div class="col-2">
                    <button @click="resetStorage" class="btn btn-warning mt-3">
                        Effacer données
                    </button>
                </div>
            </div>

            <!-- Images décoratives -->
            <img
                src="./assets/touche.png"
                style="width: 300px; position: absolute; right: 150px; top: -20px"
            >
            <img
                src="./assets/scoreboard.png"
                style="width: 150px; position: fixed; left: 10px; top: 30px"
            >
            <a href="https://abc-camera.com/" target="blank" rel="no opener">
                <img
                    src="./assets/abc.png"
                    style="width: 150px; position: fixed; right: 30px; top: 250px"
                >
            </a>
        </div>
    </div>

    <!-- Affichage du score -->
    <div class="score">
        <div
            class="tournement"
            :style="{'background-color': tournementColor}"
            v-if="tournementName"
        >
      <span
          :style="{
          color: nameColor,
          'font-size': 15 + 'px',
          'white-space': 'pre',
          'vertical-align': 'middle'
        }"
          v-html="tournementName"
      ></span>
        </div>

        <!-- On réutilise playerOrder pour l'affichage du score -->
        <div v-for="i in playerOrder" :key="'score-' + i">
            <!-- Joueur 1 -->
            <div
                class="user-score joueur1"
                :style="{'background-color': j1color}"
                v-if="i == 1"
            >
                <img
                    :src="j1Logo"
                    class="logo"
                    v-bind:class="{
            'j1': playerOrder[0] == 1,
            'j2': playerOrder[0] == 2
          }"
                    v-if="j1Logo"
                >
                <div class="user-name">
                    <i class="bi bi-trophy me-1" v-if="j1winner"></i>
                    <span
                        :style="{
              color: nameColor,
              'font-size': fontSize + 'px',
              'white-space': 'pre',
              'vertical-align': 'middle'
            }"
                        v-html="j1name"
                    ></span>
                    <span class="tm" v-if="tm1" :style="{'color': nameColor}">T</span>
                </div>
                <div class="user-service">
                    <Transition>
                        <div
                            v-if="service === 'j1'"
                            class="service"
                            :style="{'background': serviceColor}"
                        ></div>
                    </Transition>
                </div>
                <div
                    class="user-set"
                    :style="{'color': setColor, 'background': setBackground}"
                >
                    {{ j1sets }}
                </div>
                <div
                    class="user-point"
                    :style="{'color': pointColor}"
                >
                    {{ j1points }}
                </div>
            </div>

            <!-- Joueur 2 -->
            <div
                class="user-score joueur2"
                :style="{'background-color': j2color}"
                v-if="i == 2"
            >
                <img
                    :src="j2Logo"
                    class="logo"
                    v-bind:class="{
            'j2': playerOrder[0] == 1,
            'j1': playerOrder[0] == 2
          }"
                    v-if="j2Logo"
                >
                <div class="user-name">
                    <i class="bi bi-trophy me-1" v-if="j2winner"></i>
                    <span
                        :style="{
              color: nameColor,
              'font-size': fontSize + 'px',
              'white-space': 'pre',
              'vertical-align': 'middle'
            }"
                        v-html="j2name"
                    ></span>
                    <span class="tm" v-if="tm2" :style="{'color': nameColor}">T</span>
                </div>
                <div class="user-service">
                    <Transition>
                        <div
                            v-if="service === 'j2'"
                            class="service"
                            :style="{'background': serviceColor}"
                        ></div>
                    </Transition>
                </div>
                <div
                    class="user-set"
                    :style="{'color': setColor, 'background': setBackground}"
                >
                    {{ j2sets }}
                </div>
                <div
                    class="user-point"
                    :style="{'color': pointColor}"
                >
                    {{ j2points }}
                </div>
            </div>
        </div>
        <div class="resume" v-html="resume"></div>
    </div>
</template>
