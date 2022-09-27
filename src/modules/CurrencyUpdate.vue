<template>
    <div class="testapp-currency-action testapp-currency-edit">
        <div class="testapp-currency-action-header d-flex align-items-center w-100">
            <h2 class="d-flex align-items-center justify-content-start">

                <!-- Close component -->
                <button class="testapp-btn testapp-btn--ghost" @click="$emit('actionLoad', '')"><i class="bi bi-x"></i></button>
                {{ moduleTitle }}
            </h2>

            <!-- Reset field info -->
            <button class="testapp-btn testapp-btn--alt" @click="resetFields">{{ text.cancelButton }}</button>

            <!-- Save currency - if fields are valid -->
            <button class="testapp-btn testapp-currency-save" @click="updateCurrency(updatedCurrency)">{{ text.saveButton }}</button>
        </div>

        <form>

            <!-- Loop through fields and render relevant info -->
            <div v-for="(field, index) in fieldValidity" 
                :key="field" 
                class="mb-3" 
                :fieldname="index">
                <label :for="index" class="mb-1">
                    {{ text.fieldLabel }} {{index}}
                </label>
                <input type="text" 
                    :name="index" 
                    v-model="updatedCurrency[index]"
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
    name: 'CurrencyUpdate',

    props: ['currencies', 'selectedIndex', 'ogCurrency', 'fieldValidity', 'text'],

    methods: {
        updateCurrency(updatedCurrencyObj) {

            // Check if fields are valid
            if (this.$parent.checkFieldValidity(updatedCurrencyObj, this.currencies, 'edit')) {
                this.$emit('listUpdate', 'update', updatedCurrencyObj);
            }
        },

        resetFields() {
            Object.keys(this.updatedCurrency).forEach(field => {
                this.updatedCurrency[field] = this.ogCurrency[field];
            });
        }
    },

    watch: {

        // Set data to be preserved in local storage
        updatedCurrency(val) {localStorage.updatedCurrency = JSON.stringify(val); console.log(localStorage.updatedCurrency);}
    },

    mounted() {

        // Load data preserved in local storage
        if (localStorage.updatedCurrency) {this.updatedCurrency = JSON.parse(localStorage.updatedCurrency);}
    },

    data() {
        return {
            moduleTitle: 'Edit Currency',

            updatedCurrency: {
                name: this.ogCurrency.name,
                code: this.ogCurrency.code,
                symbol: this.ogCurrency.symbol,
                id: this.ogCurrency.id
            }
        }
    },
}
</script>

<style scoped lang="scss">
    @import "../styles/variables";
    @import "../styles/module-currencyAction";
</style>
