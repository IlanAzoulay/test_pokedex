<template>
    <div class="card-container" :class="getAnimationClass()">

        <div class="background"></div>
        <img class="char-img" v-bind:src="getImage()">
        <img class="card-img" v-bind:src="getCardURL()">

        <h1 :style="`color: ${textcolor};`">{{getName()}}</h1>
        <p :style="`color: ${textcolor};`">{{getDescription()}}</p>
    </div>
</template>

<script>
import data from '../../static/data.json'

export default {
    props: {
        pokemon: {type: Object, default: null},
        showing: {type: Boolean, default: false}
    },
    data() {
        return {
            json: data,
            textcolor: "black",
            opening: false,
            closing: false
        }
    },

    watch: {
        showing: function(newVal, oldVal) {
            if (newVal) {
                console.log("sending opening")
                this.openingAnimation();
            } else {
                this.closingAnimation();
            }
        }
    },

    methods: {
        getCardURL() {
            if (this.pokemon == null || this.pokemon == undefined) {return "";}
            for (let i=0; i < this.json.length; i++) {
                if (this.pokemon.types[0].type.name === this.json[i].type){
                    if (this.json[i].type == "dark"){this.textcolor = "white";}
                    else {this.textcolor = "black";}
                    return this.json[i].url;
                }
            }
            // No matching type found (give colorless by default)
            this.textcolor = "black";
            return this.json[this.json.length - 1].url;
        },
        getImage() {
            if (this.pokemon == null || this.pokemon == undefined) {return "";}
            return this.pokemon.sprites.other["official-artwork"].front_default;
        },
        getName() {
            if (this.pokemon == null || this.pokemon == undefined) {return "";}
            return (this.pokemon.name).toUpperCase();
        },
        getDescription() {
            if (this.pokemon == null || this.pokemon == undefined) {return "";}
            let text = "ID: " + this.pokemon.id + "\n";
            text += "Type: " + this.pokemon.types[0].type.name + "\n";
            text += "Abilities:\n";
            for (let i=0; i<this.pokemon.abilities.length; i++){
                text += "- " + this.pokemon.abilities[i].ability.name + "\n"
            }
            return text;
        },
        // Animation when showing the card
        openingAnimation() {
            console.log("in opening")
            this.opening = true;
            setTimeout(() => {
                this.opening = false
            }, 400)
        },
        // Animation when hiding the card
        closingAnimation() {
            this.closing = true;
            setTimeout(() => {
                this.closing = false
            }, 400)
        },
        getAnimationClass(){
            if (this.opening) {return "boing";}
            else if (this.closing) {return "fadeout"}
            return "";
        }
    }
}
</script>


<style scoped>

.card-container {
    @apply z-10;
    @apply relative;
    height: 100%;
    width: fit-content;
    display: inline-block;
    aspect-ratio: initial;
}
.char-img, .background {
    @apply absolute;
    left: 50%;
    transform: translate(-50%, -50%);
} 
.card-img {
    @apply z-20;
    height: 100%;
    aspect-ratio: initial;
}
.char-img {
    @apply -z-10;
    height: 37%;
    top: 29%;
    aspect-ratio: initial;
}
.background {
    @apply -z-20;
    background: radial-gradient(grey, white);
    top: 30%;
    height: 40%;
    width: 100%;
}
h1 {
    @apply absolute z-30 text-center w-full;
    @apply font-bold;
    font-size: 3.5vh;
    top: 3%;
}

p {
    @apply absolute z-30;
    @apply text-left;
    font-size: 2.8vh;
    left: 50%;
    top: 51%;
    width: 80%;
    white-space: pre-wrap;
    transform: translate(-50%, 0);
}

.boing {
    animation: boing 0.4s ease-in-out;
    transform-origin: center center;
}
.fadeout {
    animation: fadeout 0.4s ease-in-out;
    transform-origin: center center;
}

@keyframes boing {
    0% {
        opacity: 0;
        transform: scale(0.1) rotate(-90deg);
    }
    80% {
        transform: scale(1.2);
    }
    100% {
        opacity: 100;
        transform: scale(1) rotate(0);
    }
}
@keyframes fadeout {
    0% {
        opacity: 100%;
        transform: scale(1);
    }
    100% {
        opacity: 0%;
        transform: scale(0.5);
    }
}

</style>