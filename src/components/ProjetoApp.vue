<template>

    <div class="templateGeral"> 
      
        <div class="painel scores">
             
            <div class="score">
                <h1>Jogador</h1>
                <div class="life-bar">
                    <div class="life"
                    :class="{danger: playerLife < 20 }"
                    :style="{width: playerLife + '%'}"></div>
                </div>
                <div>{{ playerLife }}%</div>
            </div>

            <div class="score">
                <h1>Monstro</h1>
                <div class="life-bar">
                    <div class="life"
                    :class="{danger: monsterLife < 20 }"
                    :style="{width: monsterLife + '%'}"></div>
                </div>
                <div>{{ monsterLife }}%</div>
            </div>
        </div>


        <div v-if="hasResult" class="painel results">
            <div v-if="monsterLife == 0" class="win"> Voce ganhou !!!</div>
            <div v-else class="lose"> Voce perdeu !!!</div>

        </div>

        <div class="painel buttons">
            <div v-if="!running">
                <button @click="startGame" class="btn start">Iniciar Jogo</button>
            </div>
            <div v-else>
                <button @click="atk(false)" class="btn atk">Ataque</button>    
                <button @click="atk(true)" class="btn atkEsp">Especial</button>
                <button @click="healAndHurt" class="btn heal">Curar</button>
                <button @click="stopGame" class="btn giveUp">Desistir</button>
            </div>
        </div>

        <div v-if="logs.length" class="painel logs">
            <ul>
                <li v-for="log in logs" :key="log" :class="log.cls" class="log">{{ log.text }}</li>
            </ul>


        </div>
        

      


    </div>
   

</template>

<script>
    export default {
        data(){
            return{
                running: false,
                playerLife: 100,
                monsterLife: 100,
                logs: []

            }

        },
        methods:{
            startGame(){
                this.running = true
                this.playerLife = 100,
                this.monsterLife = 100,
                this.logs = []
            },
            stopGame(){
                this.running = false
            },
            atk(especial){
                this.hurt('monsterLife', 5, 10, especial, 'Jogador', 'Monstro', 'player') 
                if(this.monsterLife > 0){
                    this.hurt('playerLife', 7, 12, false, 'Monstro', 'Jogador', 'monster')  
                }

            },
            healAndHurt(){
                this.heal(10, 15)
                this.hurt('playerLife', 7, 12, false, 'Monstro', 'Jogador', 'monster')
            },


            heal(min,max){
                const heal = this.getRandom(min, max)
                this.playerLife = Math.min(this.playerLife + heal, 100)
                this.registerLog(`Jogador ganhou força de ${heal}.`, 'player')
            },

            getRandom(min, max) {
                const byteArray = new Uint8Array(1);
                window.crypto.getRandomValues(byteArray);
                const range = max - min + 1;
                const randomValue = (byteArray[0] % range) + min;
                console.log(randomValue);
                return randomValue;
            },


            hurt(atr, min, max, especial, source, target, cls){
                const plus = especial ? 5 : 0
                const hurt = this.getRandom(min + plus, max + plus)
                this[atr] = Math.max(this[atr] - hurt, 0)
                this.registerLog(`${source} atingiu ${target} com ${hurt}.`, cls)
            },
            registerLog(text, cls){
                this.logs.unshift({ text , cls })
            }

        },
        computed:{
            hasResult(){
                return this.playerLife == 0 || this.monsterLife == 0
            }

        },
        watch:{
            hasResult(value){
                if(value) this.running = false
            }

        }

    }   
</script>

<style>
    .templateGeral{
        font-family: 'Montserrat', sans-serif;
        display: flex;
        flex-direction: column;
    }

    .painel{
        margin: 10px;
        padding: 20px;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.20);
        background-color: rgba(67, 74, 196, 0.705);
    }

   .scores{
        display: flex;
   }

   .score{
        flex: 1;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
   }
    
   .score h1{
        font-weight: 300;
        font-size: 1.6rem;
        color: black;
   }

    .life-bar{
          width: 80%;
          height: 20px;
          background-color: white;
          border-radius: 5px;
          border: 1px solid rgb(184, 147, 147);
          display: flex;
        }
    
    .life-bar .life{
        display: flex;
        justify-content: center;
        height: 100%;
        background-color: green;

    }
       
    .life-bar .life.danger{
        background-color: red;
    }
    
    .win{
        font-size: 2rem;
        color: green;
        font-weight: 300;
    }

    .lose{
        font-size: 2rem;
        color: red;
        font-weight: 300;
    }

    .buttons{
        display: flex;
        justify-content: space-around;

    }


    .btn{
        padding: 5px 10px;
        margin: 0px 10px;
        border-radius: 5px;
        text-transform: uppercase;
        font-size: 1.1rem;
        
       
    }

    .start{
        background-color: rgb(0, 0, 0);
        color: white;
    }


    .atk{
        background-color: red;
        color: white;
    }

    .atkEsp{
        background-color: rgb(172, 190, 11);
        color: white;

    }

    .heal{
        background-color: green;
        color: white;

    }

    .giveUp{
        background-color: black;
        color: white;

    }

    .logs ul{
        display: flex;
        flex-direction: column;
        list-style: none;
        padding: 0px;
        margin: 0px;
    }
    
    .logs ul li{
        display: flex;
        justify-content: center;
        margin: 4px 0px;
        padding: 3px 0px;
        font-weight: 600;
        font-size: 1.1rem;
        text-transform: uppercase;
        border-radius: 3px;
    }

    .player{
        background-color: rgb(66, 64, 210);
        color: white;
    }

    .monster{
        background-color: rgb(210, 64, 64);
        color: white;
    }

</style>