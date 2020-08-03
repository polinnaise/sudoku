<template>
  <div
    id="app"
    class="sky"
  >
  <div class="stars">
    <Menu
      v-if='start'
      @reset='start = false'
    />
    <Sudoku
      v-if='start'
      :diff='diff'
      :showDetails='showDetails'
      :currentGame='currentGame'
    />
    <div
      v-else
      class="back"
    >
      <h1>Sudoku</h1>
      <div>
      <button
        @click='currentGame = false; start=true'
        class="neon"
      >
      New Game
      </button>
      <button
        class="con"
        v-if='hasCurrentGame'
        @click='currentGame = true; start = true'
      >
      Continue
      </button>
      </div>  
    </div>
    </div>
  </div>
</template>

<script>
import Sudoku from "./components/Sudoku.vue";
import Menu from "./components/Menu.vue";

export default {
	name: "App",
	data() {
		return {
			diff: 4,
			start: false,
			showDetails: true,
			currentGame: false,
			hasCurrentGame: false,
		};
	},
	components: {
		Sudoku,
		Menu,
	},
	created() {
		if (localStorage.currentGame !== undefined) {
			this.hasCurrentGame = true;
		}
	},
};
</script>

<style>
@font-face {
  font-family: 'Neuropol';
  src: url("./font/neuropol.ttf") format("truetype");
}
html, body {
  margin: 0;
  background-color: #0a2342;
}
#app, .back, .timer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-family: 'Neuropol';
  letter-spacing: 3px;
  font-size: 18px;
  color: white;
}
.sky {
  position:absolute;
  width:100%;
  height: 100%;
  background: #4B79A1;
  background: -webkit-linear-gradient(to top, #0a2342, black);
  background: linear-gradient(to top, #0a2342, black);
  background: -olinear-gradient(to top, #0a2342, black); 
}
.stars {
  top:0;
  left:0;
  right:0;
  bottom:0;
  width:100%;
  height:100%;
  display:block;
  position:absolute;
  background:url(http://www.script-tutorials.com/demos/360/images/stars.png) repeat top center;
  z-index:0;
}
h1 {
  margin: 10% 0 0;
  font-weight: 300;
  font-size:7em;
  color: rgba(120, 0, 50, .5);
  animation: text-flicker 2s linear forwards;
}
.neon, .con {
  width: 200px;
  height: 50px;
  padding: 10px 10px;
  border-radius: 8px;
  display: inline-block;
  margin-right: 10px;
  margin-top: 20%;
  text-shadow: unset;
}
.neon:hover, .con:hover {
  animation: border-flicker 2s linear forwards;
  text-shadow: 0 0 5px #00E0FF;
}
button, button.continue  {
  all: unset;
  background: linear-gradient(var(--background-colour));
  text-align: center;
  position: relative;
  border-radius: 8px;
  border: var(--border-width) solid var(--border-colour);
  transform: rotateX(15deg);
  animation: rotate ease-out 6s infinite alternate;
  border: 2px solid #00E0FF;
  text-shadow: 0 0 5px #00E0FF;
  color: #00E0FF;
  padding: 15px 15px;
  cursor: pointer;
}
@keyframes border-flicker {
  10% {
    border: 3px solid rgb(0, 180, 230);
    box-shadow: 0 0 15px -1px rgb(0, 180, 230), 
    0 0 12px -1px rgb(0, 180, 230) inset;
  }
  15% {
    border: 3px solid rgb(0, 180, 230);
    box-shadow: 0 0 15px -1px rgb(0, 180, 230), 
    0 0 12px -1px rgb(0, 180, 230) inset;
  }
}
@keyframes rotate {
  0% {
    transform: rotateY(-40deg);
  }
  100% {
    transform: rotateY(40deg);
  }
}
@keyframes text-flicker {
  2% { 
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  3% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  6% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  9% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  11% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  14% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  18% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  32% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  33% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  37% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  39% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  43% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  46% {
    color: rgba(120, 0, 50, .5);
    text-shadow: none;
  }
  47% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
  100% {
    color: rgb(230, 0, 120);
    text-shadow: 0 0 15px rgb(230, 0, 120);
  }
}
@media screen and (max-width: 550px) {
  .sky {
    background-color: #0a2342;
  }
  h1 {
    font-size: 40px;
  }
  .neon, .con {
    width: 150px;
    display: inherit;
    margin-top:30%;
  }
  .menu {
    margin-left:10% !important;
  }
	.sudoku {
		justify-content: flex-start;
		margin-top: 10%;
	}
	.digits button {
		font-size: 15px;
	}
	.cell {
		height: 2em;
		width: 2em;
	}
  .row,
  .digits {
     /* Ugly quick fix */
    display: -webkit-box; 
    width:60%;
  }
}
</style>