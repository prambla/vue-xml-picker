<script>
import XMLComponent from "./XMLComponent.vue"
import XMLAttributes from "./XMLAttributes.vue"

export default {
    name: "XMLComponent",
    props: {
        // xml node javascript object
        node: Node,
        // this component name on a path, name is the prop that indicates the value that this xml node will have if referenced trough XPath.
        name: String, 
        // level of identation this node is located.
        level: Number,
        // selected path on the app, this is used to highlight the element referenced by the path
        selectedPath: String
    },
    components: {
        XMLAttributes
    },
    data() {
        return {
            // child nodes of this xml node
            childComponents: [],
            // string of the atributes an its value of this node
            attributes: '',
            // number of attributes this node has
            numOfAttributes: 0,
            // this indicates the child format, if there's more than one child its value will be 'list', if only has one child will be 'element' else will be 'text' which indicates that child it's the value of this node
            childType: '',
            // indicates the class of the text, can take this two values: 'text-value' and 'text-value-selected'
            textValueClass: 'text-value',
            // indicates if the element is highlighted or not (with values 'highlight' and 'not-highlight')
            selected: 'not-highlight',
            // used to indicates XMLAttribute that one of my attribute has been selected on path
            selectedAttribute: '',
            // indicates the selected path to transfer to the childs
            childSelectedPath: '',
            folded: false
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
        computeSelectedPath() {
            if (this.selectedPath === this.name) {
                // i'm the selected element
                /* WHAT TO DO
                * Paint my backgorund (change selected value)
                * if (this.childType === 'text') Unpaint my text background (change its class somehow)
                * if (this.childType === 'list') Tell children to unpaint (childSelectedPath = 'not selected' or '')
                * if (numOfAttributes !== 0) Unpaint all attributes
                */
                this.selected = 'highlight';
                if (this.childType === 'text') this.textValueClass = 'text-value';
                if (this.childType === 'list') this.childSelectedPath = 'Not selected';
                if (this.numOfAttributes !== 0) this.selectedAttribute = '';

            } else if (this.selectedPath.startsWith(this.name + '[text()]')) {
                // my text is the selected element
                /* WHAT TO DO
                * Paint my text background
                * Unpaint my background (in case I was painted previously)
                * if (numOfAttributes !== 0) Unpaint all attributes 
                */
                if (this.childType === 'text') this.textValueClass = 'text-value-selected';
                this.selected = 'not-highlight';
                if (this.numOfAttributes !== 0) this.selectedAttribute = '';

            } else if (this.selectedPath.startsWith(this.name + '/@')) {
                // one of my attributes is the selected element
                /* WHAT TO DO
                * Tell XMLattribute to paint the attribute named after 'this.name/@________' on the selectedPath 
                * Unpaint my background (in case I was painted previously)
                * if (this.childType === 'text') Unpaint my text background (change its class somehow)
                * if (this.childType === 'list') Tell children to unpaint (childSelectedPath = 'not selected' or '')
                */
                this.selectedAttribute = this.selectedPath.substring(this.selectedPath.search("/@")+2, this.selectedPath.length);
                this.selected = 'not-highlight';
                if (this.childType === 'text') this.textValueClass = 'text-value';
                if (this.childType === 'list') this.childSelectedPath = 'Not selected';
            
            } else if (this.selectedPath.startsWith(this.name)) {
                // i'm on the path but not selected
                /* WHAT TO DO
                * Assign the remaining path (without my name on it) to childSelectedPath
                * Unpaint my background (in case I was painted previously)
                * if (numOfAttributes !== 0) Unpaint all attributes
                CLARIFICATION: childType has to be 'list' if I'm here, and a XML element can't be 'list' and have text
                */
                this.childSelectedPath = this.selectedPath.substring(this.selectedPath.search("/")+1, this.selectedPath.length);
                this.selected = 'not-highlight';
                if (this.numOfAttributes !== 0) this.selectedAttribute = '';

            } else {
                // i'm not on the path
                /* WHAT TO DO
                * Unpaint my backgorund (change selected value)
                * if (this.childType === 'text') Unpaint my text background (change its class somehow)
                * if (this.childType === 'list') Tell children to unpaint (childSelectedPath = 'not selected' or '')
                * if (numOfAttributes !== 0) Unpaint all attributes
                */
                this.selected = 'not-highlight';
                if (this.childType === 'text') this.textValueClass = 'text-value';
                if (this.childType === 'list') this.childSelectedPath = 'Not selected';
                if (this.numOfAttributes !== 0) this.selectedAttribute = '';
            
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
        
        if (this.node.hasAttributes()) {
            this.computeAtrributes();
        }
    },
    watch: {
        selectedPath: function() {
            this.computeSelectedPath();
        } 
    }
}
</script>

<template>
    <div>
        <div :class="selected">
            <span v-if="!folded" @click="folded = !folded" class="folding"><svg width="1em" height="1em" viewBox="0 0 24 24" class="caret" data-v-d071a178=""><path fill="whitesmoke" d="m11.998 17l7-8h-14z"></path></svg></span>
            <span v-else @click="folded = !folded" class="folding"><svg width="1em" height="1em" viewBox="0 0 24 24" class="caret" data-v-d071a178=""><path fill="whitesmoke" d="m9 19l8-7l-8-7z"></path></svg></span>
            <span class="simbols">&lt</span><span @click="clicked('element')" class="elementName">{{ node.nodeName }}</span><XMLAttributes @click="receiveEmit" :attributesStr="attributes" :nAttributes="numOfAttributes" :selectedAttribute="selectedAttribute"></XMLAttributes><span class="simbols">&gt</span>
            <div :style="folded ? 'display: none;' : 'display: block;'">
                <div v-if="childType=='list'">
                    <div v-for="child in childComponents" :key="child.id">
                        <XMLComponent class="child" @click="receiveEmit" :node="child.node" :name="child.id" :selectedPath="childSelectedPath"></XMLComponent>
                    </div>
                </div>
                <div v-else-if="childType=='text'">
                    <span></span><span @click="clicked('text')" :class="textValueClass">{{ node.firstChild.nodeValue }}</span>
                </div>
                <div v-else>
                    <XMLComponent class="child" @click="receiveEmit" :node="node.firstChild" :name="node.firstChild.nodeName" :selectedPath="childSelectedPath"></XMLComponent>
                </div>
                <span class="simbols">&lt/</span><span class="elementNameClose">{{ node.nodeName }}</span><span class="simbols">&gt</span>
            </div>
        </div>
    </div>
</template>

<style scoped>
.highlight {
    background-color: grey;
    border-radius: 1mm;
    padding-left: 1em;
    padding-right: 15em;
    margin-right: 15em;
    box-sizing: border-box;
}

.not-highlight {
    background-color: transparent;
    padding-left: 0.5em;
    box-sizing: border-box;
}
.folding {
    margin-left: -1em;
}
.child {
    margin-left: 2em;
}
.text-value {
    color: whitesmoke;
    cursor: pointer;
    margin-left: 2em;
    padding-left: 0.5em;
}

.text-value-selected {
    color: whitesmoke;
    cursor: pointer;
    margin-left: 2em;
    padding-left: 0.5em;
    padding-right: 0.5em;
    background-color: grey;
    border-radius: 1mm;
}

.elementName {
    color: rgb(81, 255, 0);
    cursor: pointer;
}
.elementNameClose {
    color: rgb(81, 255, 0);
}
</style>