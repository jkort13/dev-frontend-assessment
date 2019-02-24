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
                    <div class="columns is-centered">
                        <div class="column is-two-thirds">
                            <div class="card">
                                <div class="card-content">
                                    <div class="content">
                                        <div class="columns">
                                            <div class="column">
                                                <p>Search:</p>
                                                <input v-model="searchInput" class="input" type="text" placeholder="Search...">
                                            </div>
                                            <div class="column">
                                                <p>Primary Exchange:</p>
                                                <div class="select">
                                                    <select v-model="primaryExchangeFilter">
                                                        <option value="">All Exchanges</option>
                                                        <option v-for="exchange in exchanges" :key="exchange">{{exchange}}</option>
                                                    </select>
                                                </div>
                                            </div>
                                            <div class="column">
                                                <p>Sort by:</p>
                                                <div class="select">
                                                    <select v-model="sortOrder">
                                                        <option value="desc" selected>Gains</option>
                                                        <option value="asc">Losses</option>
                                                    </select>
                                                </div>
                                            </div>
                                            <div class="column">
                                                <p>&nbsp;</p>
                                                <div>
                                                    <button v-on:click='togglePercent' class="button is-info">Toggle Percents</button>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                                            
                    <div class="columns is-multiline">
                        <div v-for="company in filteredCompanies" :key="company.symbol" class="company column is-one-third">
                            <div class="card company-card">
                                <div class="card-content">
                                    <div v-if="company.change > 0" class="indicator-area">
                                        <span v-if="showPercent === true" class="change-amount change-amount-gain">{{(company.changePercent*100).toFixed(3)}} %</span>
                                        <span v-if="showPercent === false" class="change-amount change-amount-gain">+{{company.change}} </span>
                                        <div class="indicator gain"></div>
                                    </div>

                                    <div v-if="company.change < 0" class="indicator-area">
                                        <span v-if="showPercent === true" class="change-amount change-amount-loss">{{(company.changePercent*100).toFixed(3)}} %</span>
                                        <span v-if="showPercent === false" class="change-amount change-amount-loss">{{company.change}} </span>
                                        <div class="indicator loss"></div>
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
import _ from 'lodash';
import uniq from 'lodash/uniq'

export default {
    name : "Symbols",
    data() {
        return {
            loading : true,
            companies : [],
            showPercent : true,
            searchInput : "",
            primaryExchangeFilter : "",
            sortOrder : "desc"
        };
    },
    methods : {
        togglePercent : function() {
            this.showPercent = !this.showPercent
        }
    },
    computed: {
        orderedCompanies() {
            if (this.sortOrder === 'asc' && this.showPercent === false)
                return _.orderBy(this.companies, "change", ['asc'])
            else if (this.sortOrder === 'desc' && this.showPercent === false)
                return _.orderBy(this.companies, "change", ['desc'])
            else if (this.sortOrder === 'asc' && this.showPercent === true)
                return _.orderBy(this.companies, "changePercent", ['asc'])
            else if (this.sortOrder === 'desc' && this.showPercent === true)
                return _.orderBy(this.companies, "changePercent", ['desc'])
        },
        filteredCompanies() {
            return this.orderedCompanies.filter(company => {
                return (company.symbol.toLowerCase().includes(this.searchInput.toLowerCase()) ||
                        company.companyName.toLowerCase().includes(this.searchInput.toLowerCase())) &&
                        company.primaryExchange.toLowerCase().includes(this.primaryExchangeFilter.toLowerCase())
            })
        },
        exchanges() {
            return uniq(this.companies.map( p => p.primaryExchange))
        },

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
    background-color: #ce2b2b;
}

.gain {
    background-color: #008456;
}

.even {
    background-color: grey;
}

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

.company-card {
    min-height: 200px;
}
</style>