<template>
    <div class="testapp-currency-action testapp-currency-create">
        <div class="testapp-currency-action-header d-flex align-items-center w-100">
            <h2 class="d-flex align-items-center justify-content-start">

                <!-- Close component -->
                <button class="testapp-btn testapp-btn--ghost" @click="$emit('actionLoad', '')"><i class="bi bi-x"></i></button>
                {{ moduleTitle }}
            </h2>

            <!-- Clear field info -->
            <button class="testapp-btn testapp-btn--alt" @click="clearFields">{{ text.cancelButton }}</button>

            <!-- Save currency - if fields are valid -->
            <button class="testapp-btn testapp-currency-save" @click="createCurrency(newCurrency)">{{ text.saveButton }}</button>
        </div>

        <form>

            <!-- Loop through fields and render relevant info -->
            <div v-for="(field, index) in fieldValidity" 
                :key="field" 
                class="mb-3" 
                :fieldname="index">
                <label :for="index" class="mb-1">
                    {{ text.fieldLabel }} {{ index }}
                </label>
                <input type="text" 
                    :name="index" 
                    v-model="newCurrency[index]"
                    :class="field === false ? 'testapp-error' : ''"
                    :placeholder="text.fieldPlaceholders[index]"
                >

                <!-- Render if field is invalid -->
                <div v-if="field === false" class="testapp-message">
                    {{ text.errorMessage }} {{index}}
                </div>
            </div>
        </form>
    </div>
</template>

<script>
    export default {
        name: 'CurrencyCreate',

        props: ['currencies', 'fieldValidity', 'text'],

        methods: {
            createCurrency(currencyObj) { 

                // if not the first currency - assign largest ID + 1
                this.currencies.length ? 
                currencyObj.id = this.maxId + 1 : 
                    currencyObj.id = 0;

                // Check if fields are valid
                if (this.$parent.checkFieldValidity(currencyObj, this.currencies, 'add')) {
                    this.$emit('listUpdate', 'create', currencyObj);
                }
            },

            clearFields() {
                Object.keys(this.newCurrency).forEach(field => {
                    this.newCurrency[field] = '';
                });
            }
        },
        
        watch: {

            // Set data to be preserved in local storage
            newCurrency(val) {localStorage.newCurrency = JSON.stringify(val);},
            maxId(val) {localStorage.maxId = JSON.stringify(val);console.log(localStorage.maxId)}
        },

        mounted() {

            // Load data preserved in local storage
            if (localStorage.newCurrency) {this.newCurrency = JSON.parse(localStorage.newCurrency);}
            if (localStorage.maxId) {this.maxId = parseInt(localStorage.maxId);}
        },

        data() {
            return {
                moduleTitle: 'Add Currency',
                newCurrency: {
                    name: '',
                    code: '',
                    symbol: '',
                    id: ''
                }, 
                maxId: Math.max(...this.currencies.map(object => object.id)),
            }
        }
    }
</script>

<style scoped lang="scss">
    @import "../styles/variables";
    @import "../styles/module-currencyAction";
</style>
