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
        <span class="titles">Path: </span>
        <pre class="pathValue">{{ PATH }}</pre>
        <span class="titles">XML: </span>
        <XMLComponent @click="receivePath" :node="xmlDoc.firstChild" :name="rootName" :level="lvl"></XMLComponent>
    </div>
</template>

<style scoped>
.titles {
    color: black;
    font-size: 20px;
}
.pathValue{
    color: black;
    font-size: 18px;
    border-color: darkgrey;
    background-color: whitesmoke;    
}

</style>