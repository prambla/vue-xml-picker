<script>
import XMLPicker from './components/XMLPicker.vue';

export default {
    components: {
        XMLPicker
    },
    data() {
        return {
            path: 'Prem el component per visualitzar-ne el PATH'
        }
    }
}
</script>

<template>
    <div id="app">
        <div id="path">
            <span class="titles">Path: </span>
            <pre class="pathValue">{{ path }}</pre>
        </div>
        <div id="xml-picker">
            <XMLPicker v-model="path" xmlStr="<bookstore location='Barcelona' owner='Pepito'><book><title>Everyday Italian</title><author>Giada De Laurentiis</author><year>2005</year><price>30.00</price></book><book><title>Harry Potter</title><author>J K. Rowling</author><year>2005</year><price>29.99</price></book><book><title>Learning XML</title><author>Erik T. Ray</author><year>2003</year><price>39.95</price></book></bookstore>"></XMLPicker>
        </div>
    </div>
</template>

<style>
.titles {
    color: var(--text-color);
    font-size: 20px;
}
.pathValue{
    color: var(--text-color);
    font-size: 18px;
    border-color: var(--color-border);
    border-radius: 2mm;
    padding: 0.4em;
    margin-right: 10em;
    background-color: var(--color-background);
    max-width: 50vw;
}
</style>