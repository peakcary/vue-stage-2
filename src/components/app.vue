<template>
<div class='message'>
    {{msg}}
</div>
</template>

<script>
export default {
    data() {
        return {
            msg: 'Hello from vue-compoents'
        }
    }
}
</script>
<style>
.message {
    color: red;
    font-size: 36px;
    font-weight: bold;
}
</style>
