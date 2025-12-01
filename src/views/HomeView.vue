<template>
  <header>
    <img src="https://images.wsj.net/im-65599456?size=1.5" alt="Restaurant Picture" title="Restaurant">
    <h1>Welcome to BestBurger Online</h1>
  </header>
  <main>
    <section id="burgerselection">
      <h2>Choose your burger</h2>
      <p>This is where you select your burger</p>
      <div class="burgeroptions">
        <Burger 
          v-for="burger in burgers" 
          v-bind:key="burger.name"
          v-bind:burger="burger"
          v-on:orderedBurgers="addToOrder($event)"
        />
      </div>
    </section>
    <section id="deliveryinfo">
      <h2>Delivery information</h2>
      <p>Enter the necessary information for delivery below</p>
      <p>
        <label for="full name">Full name</label><br>
        <input type="text" id="fullname" v-model="fullname" required="required" placeholder="First- and lastname">
      </p>
      <p>
        <label for="email">E-mail</label><br>
        <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
      </p>
      <p>
        <label for="payment">Payment Method</label><br>
        <select id="payment" v-model="payment">
          <option selected="selected">Swish</option>
          <option>Bankkort</option>
          <option>Klarna</option>
          <option>Faktura</option>
        </select>
      </p>
      <p>
        <label for="gender">Gender</label><br>

        <input type="radio" id="female" v-model="gender" value="Female">
        <label for="female">Female</label><br>

        <input type="radio" id="male" v-model="gender" value="Male">
        <label for="male">Male</label><br>

        <input type="radio" id="nogender" v-model="gender" value="Do not wish to provide" checked="checked">
        <label for="nogender">Do not wish to provide</label><br>
      </p>
      <p>Please click on a delivery point on the map below:</p>
      <div id="mapContainer">
        <div id="map" v-on:click="setLocation">
          <div class="target" v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
            T
          </div>
        </div>
      </div>
    </section>
  </main>
  <button type="button" v-on:click="sendOrder">
    <img src="https://kramtex.se/wp-content/uploads/2021/03/foodora_logo4.png" alt="Send button" title="Send order" style="width: 30px">
    Send Order
  </button>
  <footer>
    <hr>
  </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import menu from '../assets/menu.json'
import io from 'socket.io-client'

const socket = io("localhost:3000");

function MenuItem (name, img_url, kCal, ingredients) {
  this.name = name;
  this.img_url = img_url;
  this.kCal = kCal;
  this.ingredients = ingredients;
}

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      fullname: '',
      email: '',
      payment: 'Swish',
      gender: 'Do not wish to provide',
      orderedBurgers: {},
      location: {x: 0, y:0}
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.location = { x: event.clientX - 10 - offset.x,
                        y: event.clientY - 10 - offset.y};
      },

    sendOrder: function () {
      console.log('CUSTOMER ORDER');
      console.log('Name:', this.fullname);
      console.log('Email:', this.email);
      console.log('Payment:', this.payment);
      console.log('Gender:', this.gender);
      console.log('Ordered Burgers: ', this.orderedBurgers);
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y },
                                orderItems: this.orderedBurgers,
                                customerInfo: { name: this.fullname, email: this.email,
                                                payment: this.payment,
                                                gender: this.gender}
                              }
                 );
    },

    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    }
  }
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
  body {
      font-family: "Times New Roman", serif;
      font-size: 12pt; /*såhär kommenterar man*/
      .ingredients {
          font-weight: bold;
      }
      #burgerselection {
          background-color: black;
          color: white;
      }
      button:hover {
          background-color: lightgreen;
          cursor: pointer;
      }
  }

  section {
      margin: 25px;
      padding: 15px;
      border: 2px dashed;
  }

  button {
      margin: 25px;
  }

  .burgeroptions {
      display: grid;
      grid-template-columns: 33% 33% 33%;
      grid-gap: 10px;
  }

  #mapContainer {
    width: 100%;
    height: 60vh;
    overflow: scroll;
  }

  #map {
    position: relative;
    background: url("/img/polacks.jpg");
    background-size: cover;
    width: 1920px;
    height: 1078px;
  }

  .target {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

  header {
      margin: 15px;
      padding: 15px;
      height: 200px;
      overflow: hidden;
      position: relative;
  }

  header img {
      width: 100%;
      opacity: 0.6;
      height: auto;
  }

  header h1 {
      font-family: 'Agbalumo';
      position: absolute;
      top: 50px;
      left: 30px;
      padding: 20px;
  }
</style>