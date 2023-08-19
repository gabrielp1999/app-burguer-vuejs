<template>
    <div>
        <Message :msg="msg" v-show="msg" />
        <div>
            <form id="burguer-form" @submit="createBurguer">
                <div class="input-container">
                    <label for="name">Nome cliente:</label>
                    <input type="text" id="name" name="name" v-model="name" placeholder="Digite o seu nome">
                </div>
                <div class="input-container">
                    <label for="bread">Escolha o pão:</label>
                   <select id="bread" v-model="breadSelected">
                        <option value="">Selecione seu pão</option>
                        <option v-for="bread in breads"  :value="bread.tipo" :key="bread.id">{{ bread.tipo }}</option>
                   </select>
                </div>
                <div class="input-container">
                    <label for="meat">Escolha a carne do seu burguer:</label>
                   <select id="meat" v-model="meatSelected">
                        <option value="">Selecione sua carne</option>
                        <option v-for="meat in meats"  :value="meat.tipo" :key="meat.id">{{ meat.tipo }}</option>
                   </select>
                </div>
                <div id="opcionais-container" class="input-container">
                    <label  id="opcionais-title" for="options">Escolha os opcionais:</label>
                    <div class="checkbox-container" v-for="option in optionsData" :key="option.id">
                        <input type="checkbox" name="options" v-model="options" :value="option.tipo">
                        <span>{{ option.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                  <input type="submit" class="submit-btn" value="Criar meu burguer"/>
                </div>
            </form>
        </div>
    </div>
</template>

<script>
    import axios from "axios";
    import Message from "./Message.vue"

    const URLBASE = "http://localhost:3000";

    export default {
        name: "BurguerForm",
        data(){
            return {
                breads: null,
                meats: null,
                optionsData: null,
                breadSelected: null,
                name: null,
                meatSelected: null,
                options: [],
                msg: null
            }
        },
        components: {
            Message
        },
        methods: {
            async getIngredients(){
                const { data } = await axios.get(`${URLBASE}/ingredientes`);
                console.log(data);

                this.breads = data.paes
                this.meats = data.carnes
                this.optionsData = data.opcionais
            },

            async createBurguer(e){
                e.preventDefault();

                if(!this.validateFields()){
                    this.msg = "Preencha os campos";
                    return;
                }

                const payload = {
                    nome: this.name,
                    carne: this.meatSelected,
                    pao: this.breadSelected,
                    opicionais: Array.from(this.options),
                    status: "Solicitado",
                }

                const { data } = await axios.post(`${URLBASE}/burgers`, payload);

                if(!data) return;
                
                this.msg = `Pedido do N ${data.id} realizado com sucesso!`;
                setTimeout(() => this.msg = "", 3000);

                this.clearFields();
            },

            validateFields(){
                let isValid = true;

                if(!this.name || !this.meatSelected || !this.breadSelected){
                    isValid = false;
                }

                return isValid;
            },

            clearFields(){
                this.name = null,
                this.meatSelected = null;
                this.breadSelected = null;
                this.options = [];
            }

        },
        mounted(){
            this.getIngredients();
        }
    }
</script>

<style scoped>

    #burguer-form {
        max-width: 400px;
        margin: 0 auto;   
    }
    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #opcionais-container {
        flex-direction: row;
        flex-wrap: wrap;
    }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
  }

  .checkbox-container span,
  .checkbox-container input {
    width: auto;
  }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
  }

  .submit-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }

</style>