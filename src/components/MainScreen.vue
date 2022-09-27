<template>
    <div class="testapp-main-screen-container">

        <!-- Header -->
        <div class="testapp-header">
            <div class="testapp-wrapper">
                <div class="testapp-header-info">
                    <figure>
                        A
                    </figure>
                    <div class="testapp-header-text">
                        <h6>{{ text.headerBrandInfo.title }}</h6>
                        <strong>{{ text.headerBrandInfo.name }}</strong>
                    </div>
                </div>
                <div class="testapp-header-buttons">
                    <button class="testapp-btn testapp-btn--ghost testapp-notifications-button">
                        <img src="../assets/icons/bell.svg" alt="Notifications">
                    </button>

                    <!-- Acts as a "Log out" button -->
                    <button class="testapp-btn testapp-btn--ghost testapp-user-button" type="submit" @click="logOut">
                        <img src="../assets/icons/person.svg" alt="User">
                    </button>
                </div>
            </div>
        </div>
        <div class="testapp-outer-container testapp-wrapper d-flex">
            
            <!-- Navigation Sidebar -->
            <aside class="testapp-navigation-sidebar">
                <ul>
                    <li v-for="tab in text.sidebarTabs" :key="tab" :class="tab === 'currencies' ? 'testapp-tab--active' : 'testapp-tab'">
                        <a href="#">
                            <i class="d-flex justify-content-center align-items-center">
                                <img :src="require('../assets/icons/' + tab + '.svg')">
                            </i>
                            {{ tab }}
                        </a>
                    </li>
                </ul>
            </aside>

            <!-- Main Container -->
            <main class="testapp-currecy-table-container d-flex">
                <CurrencyListing 
                    :currencies="currencies"
                    :selectedIndex="selectedIndex"
                    :key="componentKey"
                />
            
                <!-- Actions Sidebar -->
                <component 
                    :is="loadedAction"
                    :key="componentKey"
                    :text="text"
                    :currencies="currencies"
                    :ogCurrency="currencies[selectedIndex]"
                    :fieldValidity="fieldsValid"
                    @actionLoad="loadAction"
                    @listUpdate="updateList"
                />
            </main>
        </div>
    </div>
</template>

<script>
    import CurrencyCreate from '@/modules/CurrencyCreate.vue';
    import CurrencyUpdate from '@/modules/CurrencyUpdate.vue';
    import CurrencyListing from '@/modules/CurrencyListing.vue';

    export default {
        name: 'MainScreen',

        components: {
            CurrencyCreate,
            CurrencyUpdate,
            CurrencyListing,
        },

        methods: {
            logOut() {

                // Clear local storage info
                window.localStorage.clear();

                // Return to login page
                this.$emit('pageLoad', 0);
            },

            updateList(action, currencyObj, index) {
                var selected = this.selectedIndex;

                // Create currency actions
                if (action === 'create') {
                    this.currencies = [...this.currencies, currencyObj];
                    this.componentKey = !this.componentKey;

                // Edit currency actions
                } else if (action === 'update') {
                    this.currencies[selected] = {
                        name: currencyObj.name,
                        code: currencyObj.code,
                        symbol: currencyObj.symbol,
                        id: currencyObj.id
                    }

                    this.componentKey = !this.componentKey;

                // Delete currency actions
                } else if (action === 'delete') {
                    // If deleting a currently-selected currency - close the 'edit' component and set selected index to none
                    index === selected ? 
                        (this.loadedAction = '', this.selectedIndex = '') :
                        this.selectedIndex = index;

                    const newState = [...this.currencies];
                    newState.splice(index, 1);

                    this.currencies = newState;
                }
            },

            checkFieldValidity(fieldsObj, currenciesObj, action) {
                let codeUnique = true;

                // Check if currency code is unique
                currenciesObj.map(curr => { 
                    if (
                        curr.code === fieldsObj.code &&
                        action === 'add'
                    ) {
                        codeUnique = false;
                    } else if (
                        action === 'edit' &&
                        curr.code === fieldsObj.code &&
                        fieldsObj.code !== currenciesObj[this.selectedIndex].code
                    ) {
                        codeUnique = false
                    }
                });

                // Test currency name field validity
                fieldsObj.name ? 
                    this.fieldsValid.name = true : 
                    this.fieldsValid.name = false;

                // Test currency code field validity
                fieldsObj.code && 
                fieldsObj.code.length === 3 && 
                codeUnique ? 
                    this.fieldsValid.code = true : 
                    this.fieldsValid.code = false;

                // Test currency symbol field validity
                fieldsObj.symbol ? 
                    this.fieldsValid.symbol = true : 
                    this.fieldsValid.symbol = false;

                if (!this.fieldsValid.name || !this.fieldsValid.code || !this.fieldsValid.symbol) {
                    return false;
                } else {
                    return true;
                }
            },

            loadAction(loadedAction, index) {
                // Set loaded action (add/edit/none)
                this.loadedAction = loadedAction;

                // Reset field validities
                this.fieldsValid = {
                    name: '',
                    code: '',
                    symbol: ''
                }

                // When selecting currency or closing the edit component - set index of selected currency
                if (
                    loadedAction === 'CurrencyUpdate' ||
                    loadedAction === ''
                ) {
                    this.selectedIndex = index;
                    this.componentKey = !this.componentKey;
                }
            },
        },

        watch: {
            // Set data to be preserved in local storage
            currencies: {
                deep: true,
                handler(val) {localStorage.currencies = JSON.stringify(val);}
            },
            loadedAction(val) {localStorage.loadedAction = val;},
            fieldsValid(val) {localStorage.fieldsValid = JSON.stringify(val);},
            selectedIndex(val) {localStorage.selectedIndex = JSON.stringify(val);}
        },

        mounted() {
            // Load data preserved in local storage
            if (localStorage.loadedAction) {this.loadedAction = localStorage.loadedAction;}
            if (localStorage.currencies) {this.currencies = JSON.parse(localStorage.currencies);}
            if (localStorage.fieldsValid) {this.fieldsValid = JSON.parse(localStorage.fieldsValid);}
            if (localStorage.selectedIndex) {this.selectedIndex = parseInt(localStorage.selectedIndex);}
        },

        data() {
            return {
                componentKey: false,
                loadedAction: '',
                currencies: [],
                selectedIndex: '',
                fieldsValid: {
                    name: '',
                    code: '',
                    symbol: ''
                },
                text: {
                    nameFieldPlaceholder: 'Add name',
                    codeFieldPlaceholder: 'e.g. USD',
                    symbolFieldPlaceholder: 'Add symbol',
                    errorMessage: 'Please enter currency ',
                    cancelButton: 'Cancel',
                    saveButton: 'Save',
                    fieldLabel: 'Currency ',
                    fieldPlaceholders: {
                        name: 'Add name',
                        code: 'e.g. USD',
                        symbol: 'Add symbol'
                    },
                    headerBrandInfo: {
                        title: 'Brand name',
                        name: 'All Stores'
                    },
                    sidebarTabs: ['analytics', 'offers', 'loyalty', 'currencies', 'dispatch', 'configurations'],
                }
            }
        },
    }
</script>

<style scoped lang="scss">
    @import "../styles/variables";
    @import "../styles/component-mainScreen";
</style>
