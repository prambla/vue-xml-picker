<script>
import XMLComponent from "./XMLComponent.vue"

var parser = new DOMParser();

export default {
    model: {
        prop: 'path',
        event: 'change'
    },
    props: ['xmlStr', 'path'],
    components: {
        XMLComponent
    },
    data() {
        return {
            xmlDoc: XMLDocument,
            rootName: ''
        }
    },
    created() {
        this.xmlDoc = parser.parseFromString(this.xmlStr, "text/xml");
        this.rootName = this.xmlDoc.firstElementChild.nodeName;
    },
    methods: {
        receivePath(finalPath) {
            this.$emit('change', finalPath);
        }
    }
}
</script>

<template>
    <div id="xml-display">
        <span class="titles">XML: </span>
        <pre class="xml-display"><XMLComponent @click="receivePath" :node="xmlDoc.firstChild" :name="rootName" :selectedPath="path"></XMLComponent></pre>
    </div>
</template>

<style scoped>
.xml-display {
    border-radius: 2mm;
    padding: 0.5em;
    padding-left: 1em;
    border-color: darkgray;
    background-color: rgb(71, 72, 73);
    max-width: 75vw;
}

div#xml-display {
    margin-top: 1%;
}

</style>