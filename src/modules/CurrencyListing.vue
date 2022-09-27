<template>
    <div class="testapp-currency-table-component">
        <div class="testapp-currency-table-header">
            <div class="d-flex justify-content-between">
                <h1>{{ text.title }}</h1>
                <button class="testapp-btn testapp-btn--icon" @click="$parent.loadAction('CurrencyCreate')">
                    <i class="bi bi-plus"></i>
                    {{ text.addCurrencyButton }}
                </button>
            </div>
            <div class="row">
                <div class="col">
                    <label for="search" class="testapp-searchbar">
                        <i>
                            <img src="../assets/icons/search.svg" alt="Search">
                        </i>
                        <input type="text" 
                            name="search" 
                            v-model="query" 
                            :placeholder="text.searchPlaceholder"
                        />
                    </label>
                </div>
            </div>
        </div>
        <table class="w-100">
            <thead>
                <tr>
                    <th>{{ text.currencyName }}</th>
                    <th>{{ text.currencyCode }}</th>
                    <th>{{ text.currencySymbol }}</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(currency, i) in filteredCurrencies" 
                    :key="currency" 
                    :id="currency.id"
                    :class="i === selectedIndex ? 'testapp-currency--selected' : 'testapp-currency'"
                >
                    <td v-for="(property, j) in currency" 
                        :key="property"
                        :class="(j !== 'id' ? 'testapp-col-value' : 'testapp-col-delete')"
                        @click="j !== 'id' ? $parent.loadAction('CurrencyUpdate', i) : $parent.updateList('delete', currency, i)"
                    >
                        <div v-if="j !== 'id'">{{ property }}</div>
                        <div v-else>
                            <button class="testapp-btn testapp-btn--ghost">
                                <img src="../assets/icons/bin.svg">
                            </button>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>    
        <div v-if="!filteredCurrencies.length" class="testapp-no-results">
            {{ query === '' ? text.noCurrencies : text.noResults }}
        </div>
    </div>
</template>

<script>
    export default {
        name: 'CurrencyListing',

        data() {
            return {
                text: {
                    title: 'Currencies',
                    currencyName: 'Currency Name',
                    currencyCode: 'Currency Code',
                    currencySymbol: 'Currency Symbol',
                    addCurrencyButton: 'Add Currency',
                    searchPlaceholder: 'Search',
                    noCurrencies: 'No currencies added',
                    noResults: 'No currencies found'
                },
                query: '',
            }
        },

        props: ['currencies' , 'selectedIndex'],

        computed: {
            filteredCurrencies() {
                const enteredQuery = this.query.toLowerCase().trim();

                if (enteredQuery) {

                    return this.currencies.filter(curr => {
                        return curr.name.toLowerCase().indexOf(enteredQuery) > -1 ||
                            curr.code.toLowerCase().indexOf(enteredQuery) > -1 ||
                            curr.symbol.toLowerCase().indexOf(enteredQuery) > -1 ||
                            toString(curr.id).toLowerCase().indexOf(enteredQuery) > -1
                    })
                } else {
                    return this.currencies;
                }
            }
        },
    }
</script>

<style scoped lang="scss">
    @import "../styles/variables";
    @import "../styles/module-currencyListing";
</style>