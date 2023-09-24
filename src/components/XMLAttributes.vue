<script>

export default {
    emits: ['click'],
    props: {
        attributesStr: String,
        nAttributes: Number,
        selectedAttribute: String
    },
    data() {
        return {
            attributes: []
        }
    },
    methods: {
        extractAttributes() {
            let att = this.attributesStr.split('=');
            for (let i = 0; i < this.nAttributes*2; i+=2) {
                this.attributes[i/2] = { name: att[i], value: att[i+1], selectionClass: 'not-selected'};
            }
        },
        clicked(name) {
            this.$emit('click', '@' + name);
        }
    },
    created() {
        if (this.nAttributes > 0) {
            this.extractAttributes();
        }
    },
    watch: {
        selectedAttribute: function() {
            for (let i = 0; i < this.nAttributes; i++) { this.attributes[i].selectionClass = 'not-selected'; }
            if (this.selectedAttribute !== '') {
                let index = this.attributes.findIndex(att => {return att.name === this.selectedAttribute});
                let aux = this.attributes.slice();
                aux[index].selectionClass = 'selected';
                this.attributes = aux.slice();
            }
        }
    }
}
</script>

<template>
    <span><span v-for="attribute in attributes" :key="attribute.name"><span :class="attribute.selectionClass" @click="clicked(attribute.name)"><span class="att-name">{{ attribute.name }}</span><span class="simbols">=</span><span class="att-value">{{ attribute.value }}</span></span></span></span>
</template>

<style scoped>
.att-name {
    color: var(--attribute-name-text-color);
}
.att-value {
    color: var(--attribute-value-text-color);
}
.not-selected {
    cursor: pointer;
    margin-left: 0.5em;
}
.selected {
    cursor: pointer;
    margin-left: 0.5em;
    border-radius: 1mm;
    background-color: var(--color-background);
}

</style>