<script>

export default {
    emits: ['click'],
    props: {
        attributesStr: String,
        nAttributes: Number
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
                this.attributes[i] = { name: att[i], value: att[i+1]};
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
    }
}
</script>

<template>
    <span><span v-for="attribute in attributes" :key="attribute.name"><span @click="clicked(attribute.name)" class="att-name"> {{ attribute.name }}</span><span class="text">=</span><span class="att-value">{{ attribute.value }}</span></span></span>
</template>

<style scoped>
.att-name {
    color: orangered;
}
.att-value {
    color: green;
}
.text {
    color: gray;
}
</style>