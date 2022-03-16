<template>
  <div>
    <v-container>
      <v-card>
        <v-card-title>
          Currency Converter
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col>
              <v-card>
                <v-card-title>
                  Amount i have
                </v-card-title>
                <v-card-text>
                  <CurrencyToggle :currences="currences" v-model="from.currency"></CurrencyToggle>
                  <v-text-field @keypress="valueValidation"  @input="changeFrom" v-model="from.val"></v-text-field>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col>
              <v-card>
                <v-card-title>
                  I need
                </v-card-title>
                <v-card-text>
                  <CurrencyToggle  :currences="currences" v-model="to.currency"></CurrencyToggle>
                  <v-text-field @keypress="valueValidation" @input="changeTo" v-model="to.val"></v-text-field>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>{{fee}}Rate: {{rate}}
        </v-card-actions>
      </v-card>
    </v-container>
  </div>
</template>
<script>
import axios from 'axios';
import CurrencyToggle from "./CurrencyToggle";

export default {
  components:{
    CurrencyToggle
  },
  data: () => ({
    rate:0.5,
    isFrom:true,
    api_key:'5f7e1fe5173c58aceb9ef61b64e2cc259a4539c2a57013598f501f9988e0df36',
    from:{
     currency:'USD',
      val:1.000,
    },
    to:{
      currency:'BTC',
      val:1.000,
    },
    currences:[
      {
        icon:'mdi-currency-usd',
        val:'USD',
        name:'USD'
      },
      {
        icon:'mdi-currency-eur',
        val:'EUR',
        name:'EUR'
      },
      {
        icon:'mdi-currency-btc',
        val:'BTC',
        name:'BTC'
      },
      {
        icon:'mdi-ethereum',
        val:'ETH',
        name:'ETH'
      }
    ]
  }),
  watch: {
    'to.currency': function (){
          this.changeFrom();
    },
    'from.currency': function (){
      this.changeFrom();
    },
  },
  mounted() {
    var that =this;
    window.matchMedia('(prefers-color-scheme: dark)').addListener(function (e) {
      that.checkTheme();
      console.log(e);
    });
    that.checkTheme();
    this.changeFrom();
  },
  methods:{
    valueValidation($event) {
      let keyCode = ($event.keyCode ? $event.keyCode : $event.which);
      if ((keyCode < 48 || keyCode > 57) && (keyCode !== 46 || this.price.indexOf('.') != -1)) { // 46 is dot
        $event.preventDefault();
      }
      if(this.price!=null && this.price.indexOf(".")>-1 && (this.price.split('.')[1].length > 1)){
        $event.preventDefault();
      }
    },
    getRate(from, to){
      {
        var that=this;
        return new Promise((resolve, reject) => {
          axios.get('https://min-api.cryptocompare.com/data/price?fsym='+from+'&tsyms='+to+'&api_key='+that.api_key)
              .then(response => {
               that.rate=response.data[to]*(1-that.fee);
            resolve(response)
          }).catch(error => {
            reject(error)
          })
        })
      }
    },
    checkTheme(){
      if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
        this.$vuetify.theme.dark=true;
      }else{
        this.$vuetify.theme.dark=false;
      }
    },
    changeFrom(){
      if (this.from.val) {
        this.getRate(this.from.currency , this.to.currency).then(() => {
          this.to.val = this.from.val * this.rate;
        })
      }
    },
    changeTo(){
      if (this.to.val) {
        this.getRate(this.from.currency , this.to.currency).then(() => {
          this.from.val=this.to.val/this.rate;
        })
      }
    }
  },
  computed:{
    fee(){
      let fee=0.01
      if (this.identicalCurrencies){
        fee=0;
      }
      return fee;
    },
    identicalCurrencies(){
      let identical =false;
      if (this.to.currency==this.from.currency){
        identical=true
      }
      return identical;
    }
  }
};
</script>