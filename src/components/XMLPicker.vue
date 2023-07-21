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
            lvl: 0,
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
        <XMLComponent class="xml-display" @click="receivePath" :node="xmlDoc.firstChild" :name="rootName" :level="lvl"></XMLComponent>
    </div>
</template>

<style scoped>
.xml-display {
    border-radius: 2mm;
    padding: 5px;
    border-color: darkgray;
    background-color: rgb(71, 72, 73);
}

div#xml-display {
    margin-top: 1%;
}

</style>