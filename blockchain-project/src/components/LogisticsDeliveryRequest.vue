<template>
  <div id="logisticsDeliveryRequest">
    <h3 class="header">Bid for Delivery</h3>
    <table>
        <thead>
          <tr>
              <th>Order</th>
              <th>Item</th>
              <th>Quantity</th>
              <th>Seller</th>
              <th>Seller Contact</th>
              <th>Reserve amount</th>
              <th>Place bid</th>
          </tr>
        </thead>

        <tbody>

          <tr v-for="(logReq, log) in logReqs">
            <td>{{logReq.Id}}</td>
            <td>{{prodNames[log]}}</td>
            <td>{{orderQtys[log]}}</td>
            <td>{{sellerNames[log]}}</td>
            <td>{{sellerContacts[log]}}</td>
            <td>$ {{logReq.desiredPrice}}</td>
            <!-- <td><button class="btn waves-effect waves-light" :disabled='isDisabled(logReq.state)' onclick="">Bid</button></td> -->
            <td><router-link class="btn waves-effect waves-light" :disabled='isDisabled(logReq.state)'
              v-bind:to="{ name: 'logisticsPlaceBid', params: { logReq_id: logReq.Id }}" tag="button">Bid</router-link></td>
          </tr>

        </tbody>
      </table>
  </div>

  </div>
</template>
</template>
<script>
import firebase from 'firebase';
import axios from 'axios';
export default {
  name: 'LogisticsDeliveryRequest',
  data () {
    return {
      logReqs: {},
      orderQtys: [],
      products: [],
      prodNames: [],
      sellers:[],
      sellerNames:[],
      sellerContacts:[]
    }
  },
  methods:{
    isDisabled(state){
      if (state !== "OPEN"){
        return true;
      }
    },
    getOrders(){
      for(var i=0; i<this.logReqs.length; i++){
        this.orderQtys.push(this.logReqs[i].quantity);
        this.products.push(this.logReqs[i].product);
      }
    },

    getProductNames(){
      for(var k=0; k<this.products.length; k++){
        axios.get('http://localhost:3000/' + firebase.auth().currentUser.email + '/product/' + this.products[k].substring(42))
        .then((response) => {
          this.prodNames.push(response.data.results.name);
          this.sellers.push(response.data.results.seller);
        })
        .catch(error => {
          console.log(error);
        })
      }
    },
    getSellerNames(){
      for(var k=0; k<this.sellers.length; k++){
        //console.log(this.sellers[k].substring(41))
        axios.get('http://localhost:3000/' + firebase.auth().currentUser.email + '/seller/' + this.sellers[k].substring(41))
        .then((response) => {
          //console.log(response.data.results);
          this.sellerNames.push(response.data.results.name);
          this.sellerContacts.push(response.data.results.contactNum)
        })
        .catch(error => {
          console.log(error);
        })
      }

    }
  },
  mounted(){
    var self = this;
    axios.get('http://localhost:3000/' + firebase.auth().currentUser.email + '/logisticsrequest')
    .then((response) => {
      console.log(response.data.results);
      this.logReqs = response.data.results;
    })
    .then((response) => {
      this.getOrders();
      //this.getOrderFields();
      //this.getProductNames();
    })
    .then((response) => {
      setTimeout(function() {
        self.getProductNames();
      }, 1000);
    })
    .then((response) => {
      setTimeout(function() {
        self.getSellerNames();
      }, 2000);
    })
    .catch(error => {
      console.log(error);
    })

  }
}
</script>
