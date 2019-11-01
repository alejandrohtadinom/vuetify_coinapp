<template>
    <v-app>
        <v-app-bar
            app
            absolute
            color="indigo"
            dark
            shrink-on-scroll
            prominent
            src="https://picsum.photos/1920/1080?random"
            fade-img-on-scroll
            scroll-target="#data-table"
        >
            <template v-slot:img="{ props }">
                <v-img
                    v-bind="props"
                    gradient="to top right, rgba(55,236,186,.7), rgba(25,32,72,.7)"
                ></v-img>
            </template>
            <v-toolbar-title class="text-uppercase">
                <span class="font-weight-light">Coin</span>
                <span>App</span>
            </v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
                <v-icon>mdi-magnify</v-icon>
            </v-btn>
        </v-app-bar>

        <v-content>
            <v-container>
                <template>
                    <v-card id="data-table">
                        <v-card-title>
                            Coins
                            <v-spacer></v-spacer>
                            <v-text-field
                                v-model="search"
                                append-icon="mdi-magnify"
                                label="Search"
                                single-line
                                hide-details
                            ></v-text-field>
                        </v-card-title>
                        <v-simple-table fixed-header>
                            <template v-slot:default>
                                <thead>
                                    <tr>
                                        <th class="text-left">Symbol</th>
                                        <th class="text-left">Name</th>
                                        <th class="text-left">change</th>
                                        <th class="text-left">Price</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="item in filteredList" :key="item.cmc_rank">
                                        <td>{{ item.symbol }}</td>
                                        <td>{{ item.name }}</td>
                                        <td>{{ item.quote.USD.percent_change_24h }}</td>
                                        <td>{{ item.quote.USD.price }}</td>
                                    </tr>
                                </tbody>
                            </template>
                        </v-simple-table>
                    </v-card>
                </template>
            </v-container>
        </v-content>
    </v-app>
</template>
<script>
 import axios from 'axios';

 export default {
     name: 'App',
     components: {
     },
     data: () => ({
         message: '',
         data: {},
         coins: [],
         exchanges: '',
         search: ''
     }),
     computed: {
         filteredList() {
             return this.coins.filter(coin => {
                 if (coin.name.toLowerCase().match(this.search.toLowerCase()) != '') {
                     return coin.name.toLowerCase().match(this.search.toLowerCase())
                 } else {
                     return coin.symbol.toLowerCase().match(this.search.toLowerCase())
                 }
             })
         }
     },
     methods: {
         fetchData() {
             axios({
                 url: `${'https://cors-anywhere.herokuapp.com/'}https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest`,
                 method: 'get',
                 headers: {
                     'X-CMC_PRO_API_KEY': '798b1dad-e067-4826-af98-36b7a3ca3fe5'
                 },
                 params: {
                     'start': '1',
                     'limit': '5000',
                     'convert': 'USD'
                 }
             })
                 .then(res => {
                     if (res.data.status.error_code == 0) {
                         this.data = res.data
                         this.message = 'Success'
                         this.coins = this.data.data
                     } else {
                         this.message = res.data.status.error_message
                     }
                 })
                 .catch(err => {
                     this.message = err
                 })
         }
     },
     created() {
         this.fetchData()
     }
 };
</script>
