<template>
    <input class="c-input" :value="modelValue" @input="inputValue" type="text"/>
</template>

<script>
export default {
    name: 'c-input',
    props: {
        modelValue: [String, Number]
    },
    methods: {
        inputValue(e) {
            this.$emit('update:modelValue', e.target.value);
        }
    }
}
</script>

<style scoped>
    .c-input {
        border-top: 3px solid #ed1a34;
        border-bottom: 1px solid #e8e9eb;
        border-right: 1px solid #e8e9eb;
        border-left: 1px solid #e8e9eb;
        color: #111214;
        padding: 5px 10px;
        width: 320px;
        height: 56px;
        cursor: pointer;
        border-bottom-left-radius: 3px;
        border-bottom-right-radius: 3px;
    }

    .c-input:focus {
        outline: none;
    }
</style>