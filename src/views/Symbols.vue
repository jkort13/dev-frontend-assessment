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
                <div class="column is-one-quarter">
                    <div class="card">
                        <a class="button">Sort</a>
                        <input class="input" type="text" placeholder="Search...">
                    </div>
                </div>
                <div class="columns is-multiline">
                    <div v-for="company in companies" :key="company.symbol" class="company column is-one-third">

                        <div class="card hover-card">
                            <div class="card-content">
                                <div class="indicator-area">
                                    <span class="change-amount change-amount-gain">{{(company.changePercent*100).toFixed(3)}} %</span>
                                    <div class="indicator gain"></div>                                
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

                        <!--<h5 class="heading is-size-5">{{company.symbol}} : <small class="is-size-7">{{company.companyName}}</small></h5>
                                      <div>Open <money :value="company.open"></money></div>
                                      <div>Close <money :value="company.close"></money></div>
                                      <timestamp :value="company.openTime"></timestamp> - <timestamp :value="company.closeTime"></timestamp>-->
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import API from '../api/IEX';
export default {
    name: "Symbols",
    data() {
        return {
            loading: true,
            companies: [],
        };
    },
    beforeMount() {
        API.getComputerHardwareCompanies().then(response => {
            this.companies = response.data;
        }).finally(() => {
            this.loading = false;
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
    background-color: red;
}

.gain {
    background-color: green;
}

.hover-card:hover {
    box-shadow: 0 2px 3px rgb(0, 128, 0), 0 0 0 1px rgba(10, 10, 10, 0.1);
}

.change-amount {
    padding: 1rem;
    font-weight: bold;
    font-size: 16pt;
}

.change-amount-gain {
    color: green;
}

.change-amount-loss {
    color: red;
}

</style>
