<template>
    <data>
        <div class="conteneur">
            <div class="label">
                JOUEUR 1 :
            </div>
            <div>
                <input v-model="j1name" type="text">
            </div>
            <div class="label">SETS :</div>
            <div>
                <input v-model="j1sets" type="number">
            </div>
            <div class="label">
                POINTS :
            </div>
            <div>
                <input v-model="j1points" type="number">
            </div>
            <div class="label">
                JOUEUR 2 :
            </div>
            <div>
                <input v-model="j2name" type="text">
            </div>
            <div class="label">
                SETS :
            </div>
            <div>
                <input v-model="j2sets" type="number">
            </div>
            <div class="label">
                POINTS :
            </div>
            <div>
                <input v-model="j2points" type="number">
            </div>

            <div class="label">
                SERVICE :
            </div>
            <div class="selecteur">
                <select v-model="service">
                    <option value="j1">Joueur 1 : {{ this.j1name }}</option>
                    <option value="j2">Joueur 2 : {{ this.j2name }}</option>
                </select>
            </div>



        </div>
    </data>

    <score>
        <div class="user-score joueur1">
            <div class="user-name">{{ this.j1name }}</div>
            <div class="user-service">
                <Transition>
                    <div v-if="this.service == 'j1'" class="service"></div>
                </Transition>
            </div>
            <div class="user-set">{{ this.j1sets }}</div>
            <div class="user-point">{{ this.j1points }}</div>
        </div>
        <div class="user-score joueur2">
            <div class="user-name">{{ this.j2name }}</div>
            <div class="user-service">
                <Transition>
                    <div v-if="this.service == 'j2'" class="service"></div>
                </Transition>
            </div>
            <div class="user-set">{{ this.j2sets }}</div>
            <div class="user-point">{{ this.j2points }}</div>
        </div>
    </score>
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

            service: "j1",


        }
    }
}
</script>

<style scoped>
.conteneur {
    display: grid;
    grid-template-columns: 100px 200px 100px 50px 100px 100px;
    gap: 1rem
}

input {
    width: 100%;
    padding: .5rem
}

data {
    position: fixed;
    top: 50px;
    left: 50px;
    background: lightblue;
    padding: 3rem
}
.label {font-weight: bold}


score {
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

score .joueur1 {
    background: orange;
    margin-bottom: 5px;
}

score .joueur2 {
    background: green;
}

score .user-score {
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
