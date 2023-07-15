<script>
import XMLComponent from "./XMLComponent.vue"
import XMLAttributes from "./XMLAttributes.vue"
const TAB_SIZE = 4;

export default {
    name: "XMLComponent",
    props: {
        node: Node,
        name: String,
        level: Number // tab level 
    },
    components: {
        XMLAttributes
    },
    data() {
        return {
            childComponents: [],
            spaces: '',
            attributes: '',
            childType: '',
            numOfAttributes: 0
        }
    },
    emits: ['click'],
    methods: {
        computeAtrributes() {
            let att = this.node.attributes;
            this.numOfAttributes = att.length;
            for (let i = 0; i < att.length; i++) {
                if (i != 0) this.attributes += '=';
                this.attributes += att[i].name + '="' + att[i].value + '"';
            }
        },
        computeSpaces() {
            for (let i = 0; i < this.level*TAB_SIZE; i++) {this.spaces += ' ';};
        },
        calculateIds() {
            let N = this.node.childNodes.length;
            let childNodes = this.node.childNodes;
            let items = [];

            for (let n = 0; n < N; n++) {
                if(typeof items[childNodes[n].nodeName] === 'undefined') {
				    items[childNodes[n].nodeName] = { count: 1, index: 0 };
			    } else {
				    items[childNodes[n].nodeName].count++;
			    }
            }

            for (let n = 0; n < N; n++) {
                let strId;
                if (items[childNodes[n].nodeName].count == 1) {
                    strId = childNodes[n].nodeName;
                } else {
				    strId = childNodes[n].nodeName + "[" + (++(items[childNodes[n].nodeName].index)) + "]";
			    }
                this.childComponents[n] = { id: strId, node: childNodes[n] };
            }
		},
        clicked(type) {
            // calcul del path en funcio del tipus que sóc
            let myPath;
            if (type === 'text') {
                myPath = this.name + '[text()]';
            }
            if (type === 'element') {
                myPath = this.name;
            }
            this.$emit('click', myPath);
        },
        receiveEmit(prevPath) {
            // el meu path més el previ i emit al pare
            let actualPath = this.name + "/" + prevPath;
            this.$emit('click', actualPath);
        }
    },
    created() {
        if(this.node.childNodes.length > 1) {
            this.calculateIds();
            this.childType = 'list';
        } else if (this.node.firstChild.nodeName === '#text') {
            this.childType = 'text';
        } else {
            this.childType = 'element';
        }

        this.computeSpaces();
        
        if (this.node.hasAttributes()) {
            this.computeAtrributes();
        }
    }
}
</script>

<template>
    <div>
        <pre><span class="angles">{{ spaces }}&lt</span><span @click="clicked('element')" class="elementName">{{ node.nodeName }}</span><XMLAttributes @click="receiveEmit" :attributesStr="attributes" :nAttributes="numOfAttributes"></XMLAttributes><span class="angles">&gt</span></pre>
        <div v-if="childType=='list'">
            <div v-for="child in childComponents" :key="child.id">
                <XMLComponent @click="receiveEmit" :node="child.node" :name="child.id" :level="level+1"></XMLComponent>
            </div>
        </div>
        <pre v-else-if="childType=='text'"><span>{{ spaces }}    </span><span @click="clicked('text')" class="text">{{ node.firstChild.nodeValue }}</span></pre>
        <div v-else>
            <XMLComponent @click="receiveEmit" :node="node.firstChild" :name="node.firstChild.nodeName" :level="level+1"></XMLComponent>
        </div>
        <pre><span class="angles">{{ spaces }}&lt/</span><span class="elementName">{{ node.nodeName }}</span><span class="angles">&gt</span></pre>
    </div>
</template>

<style scoped>
.text {
    color: whitesmoke;
}

.elementName {
    color: rgb(81, 255, 0);
}

.angles {
    color: rgb(231, 231, 231);
}

</style>