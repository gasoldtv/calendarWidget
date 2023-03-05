<template>
    <button>
        <slot></slot>
    </button>
</template>

<script>
export default {
    name: 'c-button',
}

</script>

<style scoped>
    button {
        width: 320px;
        min-height: 56px;
        background-color: #ed1a34;
        border: 1px solid #ed1a34;
        color: #fff;
        border-radius: 3px;
        outline: none;
        cursor: pointer;
    }

    button:hover {
        border: 1px solid #ed1a34;
        color: #ed1a34;
        background-color: #fff;
    }
</style>