<template>
    <div id="burger-table" v-if="burgers">
      <div>
        <Message :msg="msg" v-show="msg" />
        <div id="burger-table-heading">
          <div class="order-id">N:</div>
          <div>Cliente:</div>
          <div>Pão:</div>
          <div>Carne:</div>
          <div>Opcionais:</div>
          <div>Ações:</div>
        </div>
      </div>
      <div id="burger-table-rows">
        <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
          <div class="order-number">{{ burger.id }}</div>
          <div>{{ burger.nome }}</div>
          <div>{{ burger.pao }}</div>
          <div>{{ burger.carne }}</div>
          <div>
            <ul>
              <li v-for="(opcional, index) in burger.opicionais" :key="index">{{ opcional }}</li>
            </ul>
          </div>
          <div>
            <select name="status" class="status" @change="updateBurger($event, burger.id)">
              <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="burger.status == s.tipo">
                {{ s.tipo }}
              </option>
            </select>
            <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <h2>Não há pedidos no momento!</h2>
    </div>
  </template>
  <script>
    import axios from "axios";
import Message from "./Message.vue";
    const URLBASE = "http://localhost:3000";

    export default {
    name: "Dashboard",
    data() {
        return {
            burgers: null,
            burger_id: null,
            status: [],
            msg: null
        };
    },
    methods: {
        async getPedidos() {
            const { data } = await axios.get(`${URLBASE}/burgers`);
            this.burgers = data;
            console.log(data);
            // Resgata os status de pedidos
            this.getStatus();
        },
        async getStatus() {
            const { data } = await axios.get(`${URLBASE}/status`);
            this.status = data;
        },
        async deleteBurger(id) {
            const { data } = await axios.delete(`${URLBASE}/burgers/${id}`);

            if(!data) return;
              
              this.msg = `Pedido cancelado com sucesso!`;
              setTimeout(() => this.msg = "", 3000);
            this.getPedidos();
        },
        async updateBurger(event, id) {
            const option = event.target.value;
            const payload = {
              status: option
            };
            console.log(option);
            const { data } = await axios.patch(`${URLBASE}/burgers/${id}`, payload);

            if(!data) return;
              
            this.msg = `Pedido do N ${data.id} atualizado para ${data.status}!`;
            setTimeout(() => this.msg = "", 3000);
        }
    },
    mounted() {
        this.getPedidos();
    },
    components: { Message }
}
  </script>
  
  <style scoped>
    #burger-table {
      max-width: 1200px;
      margin: 0 auto;
    }
  
    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
      display: flex;
      flex-wrap: wrap;
    }
  
    #burger-table-heading {
      font-weight: bold;
      padding: 12px;
      border-bottom: 3px solid #333;
    }
  
    .burger-table-row {
      width: 100%;
      padding: 12px;
      border-bottom: 1px solid #CCC;
    }
  
    #burger-table-heading div,
    .burger-table-row div {
      width: 19%;
    }
  
    #burger-table-heading .order-id,
    .burger-table-row .order-number {
      width: 5%;
    }
  
    select {
      padding: 12px 6px;
      margin-right: 12px;
    }
  
    .delete-btn {
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
    
    .delete-btn:hover {
      background-color: transparent;
      color: #222;
    }
    
  </style>