<template>
    <header>
        <!-- <h1>{{ mode }} Mode</h1> -->
        <h1>Todo app</h1>
        <ToggleSection :mode="mode" @toggle="$emit('toggle')"/>
    </header>
</template>

<script>
import ToggleSection from './ToggleSection'

export default {
    props: ['mode'],
    components: {
        ToggleSection
    }
}
</script>

<style>
    header{
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0px 25px;
        height: 60px;
        background: #e6e6e6;
        transition: background 0.5s ease-in-out;
        box-shadow: 0 0 5px black;
    }

    .dark header {
        background: #152028;
    }

    header h1{
        font-size: 32px;
        font-weight: 600;
    }
</style>
