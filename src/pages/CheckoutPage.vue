<script >

import axios from 'axios';
import {router} from '../js/router'
import { store }from '../js/store'

export default {


  components:{

    
    

  },

  data(){

    return {

    store,

    dbErrors : '',

    flag:false

    }

  },

  computed:{
    
  

  },

  methods:{

    returnId(){

      if(Array.from(this.store.content.cart).length >0){

        this.store.content.cart.forEach(item => {

         this.store.content.restaurantId = item.restaurant_id

        })

        return this.store.content.restaurantId

      }else{


        return this.store.content.restaurantId
        
      }

      

    },

    braintree(){
      let button = document.querySelector('#submit-button');

      braintree.dropin.create({
        authorization: 'sandbox_8hfxqwnd_96dqcdztsrmx69jf',
        container: '#dropin-container'

      },
      (createErr, instance )=> {
      button.addEventListener('click',(e)=> {
        e.preventDefault()
        e.stopPropagation()

        instance.requestPaymentMethod((requestPaymentMethodErr, payload )=> {
          if (payload) {

           return this.flag=true

          }
          else{
            
            return this.flag=false
            
          }

    });
  });
}
);
},
  checkOrder(){
    if(this.flag===true){
      let data={ 
            cart_total_price: JSON.parse(localStorage.getItem('total_price')),
            cart_products: JSON.parse(localStorage.getItem('cart')),

            customer_name: this.store.content.customerInfo.name,
            customer_last_name: this.store.content.customerInfo.last_name,
            customer_address: this.store.content.customerInfo.address,
            customer_phone_number: this.store.content.customerInfo.phone_number,
            customer_email: this.store.content.customerInfo.email,
            restaurant_id: JSON.parse(localStorage.getItem('cart'))[0].restaurant_id,
          }

          console.log(data)

      axios.post('http://127.0.0.1:8000/api/orders/', data,{
        headers:{'Content-Type':'multipart/form-data' }
      }
      )

      .then(response =>{
        // console.log(response.data)
        if( response.data.orders=='ok'){

          return router.push({name : 'OrderSubmit'})
        //   console.log('funziona')

        }

      })

      .catch(err=>{

        if(err){
          
          console.log(err.response.data.errors)
          return this.dbErrors=err.response.data.errors;

        }

      })

    }
    },

    confirmedCheck(){

      this.store.content.paid = true

    }

  },

  mounted(){

    this.braintree()

   

  }

}
  
</script>

<template>

<div class="container-fluid payment-box py-3 custom-shadow ">

  <router-link :to="{name : 'restaurant-menu' , params : { id : returnId()}}">

    <button class="btn btn-primary m-3" @click="returnId()">

      indietro

    </button>

  </router-link>
 
  <div class="row  justify-content-center mt-50px " >

    <div class="col-lg-6 col-md-6 col-sm-12">

      <div v-if="dbErrors" class="alert alert-danger">

        <div v-for="Singleerror in dbErrors" >

          {{Singleerror.toString()}}
          
        </div>

      </div>
      
      <form @submit.prevent="checkOrder()" class="form-control">



        <div class="form-floating mb-2">

          <input class="form-control" id="name" type="text" name="name" placeholder="client name" v-model="store.content.customerInfo.name" required>
          
          <label class="form-label" for="name">Name</label>
          
        </div>

        <div class="form-floating mb-2">

          <input class="form-control" id="last-name" type="text" name="last_name" placeholder="client last name" v-model="store.content.customerInfo.last_name" required>
          
          <label class="form-label" for="last-name">Last Name</label>
          
        </div>

        <div class="form-floating mb-2">

          <input class="form-control" id="address" type="text" name="address" placeholder="client address" v-model="store.content.customerInfo.address" required>
          
          <label class="form-label" for="address">Address</label>
          
        </div>

        <div class="form-floating mb-2">

          <input class="form-control" id="phone-number" type="tel" name="phone_number" placeholder="clientphonenumber" v-model="store.content.customerInfo.phone_number" required>

          <label class="form-label" for="phone-number">Phone</label>
          
        </div>


        <div class="form-floating mb-2">

          <input class="form-control" id="email" type="email" name="email" placeholder="client email" v-model="store.content.customerInfo.email" required>

          <label class="form-label" for="email">Email</label>
          
        </div>



        

        <div class="mb-2">
          <div id="dropin-container"></div>
          <button id="submit-button" class="btn btn-success">Request payment method</button>
          
        </div>


        
        <button  @click="confirmedCheck()" :disabled="flag===false" class="btn btn-primary" type="submit">Invia</button>

      </form>
        
      

      
    </div>

  </div>




</div>





  




</template>

<style lang="scss" scoped>

@use '../scss/style.scss';

.mt-50px{

  margin-top: 3.125rem;

}

.payment-box {
  background-image: url('../../public/img/deliveboo client simple 2.jpg');

    background-size: cover;

    background-position: bottom;

    min-height: calc(100vh - 7.1875rem);

    
}

</style>