<script>
import XMLComponent from "./XMLComponent.vue"

var parser = new DOMParser();

export default {
    components: {
        XMLComponent
    },
    data() {
        return {
            xmlDoc: XMLDocument,
            PATH: 'Prem el component per visualitzar-ne el PATH',
            lvl: 0,
            rootName: ''
        }
    },
    props: {
        xmlStr: String
    },
    created() {
        this.xmlDoc = parser.parseFromString(this.xmlStr, "text/xml");
        this.rootName = this.xmlDoc.firstElementChild.nodeName;
    },
    methods: {
        receivePath(finalPath) {
            this.PATH = finalPath;
        }
    }
}
</script>

<template>
    <div>
        <div id="path">
            <span class="titles">Path: </span>
            <pre class="pathValue">{{ PATH }}</pre>
        </div>
        <div id="xml">
            <span class="titles">XML: </span>
            <XMLComponent class="xml-display" @click="receivePath" :node="xmlDoc.firstChild" :name="rootName" :level="lvl"></XMLComponent>
        </div>
    </div>
</template>

<style scoped>
.titles {
    color: whitesmoke;
    font-size: 20px;
}
.pathValue{
    color: whitesmoke;
    font-size: 18px;
    border-color: darkgrey;
    border-radius: 2mm;
    padding: 3px;
    background-color: rgb(71, 72, 73);
}
.xml-display {
    border-radius: 2mm;
    padding: 5px;
    border-color: darkgray;
    background-color: rgb(71, 72, 73);
}

div#xml {
    margin-top: 1%;
}

</style>