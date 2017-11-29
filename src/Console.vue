<template>
    <div>
        <div>{{query}}</div>
        <div class="output" :class = "{'is-warning': warning}"> {{result}}</div>
    </div>
</template>

<script>
export default {
    props: ['query', 'result','warning']

}
</script>

<style lang="scss">

.is-warning {
    background-color: #FFF5F5;
    color: red;
}

.output {
    border-bottom: 1px solid black;
}
   
</style>