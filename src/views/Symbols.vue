<template>
    <div>
        <div class="primary-heading-con">
            <div class="heading">
                <div class="title">Symbols/Tickers</div>
            </div>
        </div>

        <div class="page-content-con">
            <loading v-if="loading"></loading>
            <div v-else>
                <div class="container">
                    <div class="column">
                        <!--TODO: Make sticky-->
                        <button v-on:click='togglePercent' class="button is-info is-large">$ or %</button>
                        <div class="column">
                            <input v-model="searchInput" class="input" type="text" placeholder="Search...">
                            <!-- <p>Filter is: {{ filtered }}</p> -->
                        </div>
                    </div>

                    <div class="columns is-multiline">
                        <div v-for="company in companies" :key="company.symbol" class="company column is-one-third">
                            <div class="card">
                                <div class="card-content">
                                    <div v-if="company.change > 0" class="indicator-area">
                                        <span v-if="showPercent === true" class="change-amount change-amount-gain">{{(company.changePercent*100).toFixed(3)}} %</span>
                                        <span v-if="showPercent === false" class="change-amount change-amount-gain">+{{company.change}} </span>
                                        <div class="indicator gain"></div>
                                    </div>

                                    <div v-if="company.change < 0" class="indicator-area">
                                        <span v-if="showPercent === true" class="change-amount change-amount-gain">{{(company.changePercent*100).toFixed(3)}} %</span>
                                        <span v-if="showPercent === false" class="change-amount change-amount-gain">+{{company.change}} </span>
                                        <div class="indicator gain"></div>
                                    </div> 

                                    <div v-if="company.change === 0" class="indicator-area">
                                        <span v-if="showPercent === true" class="change-amount change-amount-even">{{(company.changePercent*100).toFixed(3)}} %</span>
                                        <span v-if="showPercent === false" class="change-amount change-amount-even">{{company.change.toFixed(2)}}</span>
                                        <div class="indicator even"></div>
                                    </div>

                                    <p class="title">
                                        {{company.symbol}}
                                    </p>
                                    <p class="subtitle">
                                        {{company.companyName}}
                                    </p>
                                    <div class="columns">
                                        <div class="column">
                                            <div>Open
                                                <money :value="company.open"></money>
                                            </div>
                                            <div>Close
                                                <money :value="company.close"></money>
                                            </div>
                                            <timestamp :value="company.openTime"></timestamp> -
                                            <timestamp :value="company.closeTime"></timestamp>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import API from '../api/IEX';
export default {
    name : "Symbols",
    data() {
        return {
            loading : true,
            companies : [],
            showPercent : true,
            searchInput: ""
        };
    },
    methods : {
        togglePercent : function() {
            this.showPercent = !this.showPercent
        }
    },
    beforeMount() {
        API.getComputerHardwareCompanies().then(response => {
            this.companies = response.data;
        }).finally(() => {
            this.loading = false;
            // this.showPercent = true;
        });
    },
}
</script>

<style lang="scss" scoped>
@import '../assets/css/_theme';
.company {
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid $white-ter;
}

.indicator-area {
    position: absolute;
    top: 15px;
    right: 15px;
}

.indicator {
    display: inline-block;
    height: 15px;
    width: 15px;
    border-radius: 15px;
}

.loss {
    background-color: #ce2b2b;
}

.gain {
    background-color: #008456;
}

.even {
    background-color: grey;
}

// .hover-card-gain:hover {
//     box-shadow: 0 2px 3px rgb(0, 128, 0), 0 0 0 1px rgba(10, 10, 10, 0.1);
// }
// .hover-card-loss:hover {
//     box-shadow: 0 2px 3px red, 0 0 0 1px rgba(10, 10, 10, 0.1);
// }
// .hover-card-even:hover {
//     box-shadow: 0 2px 3px grey, 0 0 0 1px rgba(10, 10, 10, 0.1);
// }
.change-amount {
    padding: 1rem;
    font-weight: bold;
    font-size: 16pt;
}

.change-amount-gain {
    color: #008456;
}

.change-amount-loss {
    color: #ce2b2b;
}

.change-amount-even {
    color: grey;
}
</style>