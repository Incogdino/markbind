<template>
    <div :class="`col-md-${computedBlock}`" v-show="computeDisabled">
        <div class="article-container" :style="computedWidth">
            <div class="card-header-container">
                <img 
                    v-if="src" 
                    :src="src" 
                    width="100" 
                    height="100"
                    :alt="alt"
                    :width="computedWidth"
                    :height="computedHeight"
                    :loading="computedLoadType"
                    class="annotate-image"
                    @load.once="computeWidthAndHeight">
                <slot v-if="!src" name="header"></slot>
            </div>
            
            <div class="card-content-container">
                <slot></slot>                
            </div>

            <div v-if="hasTag" class="tag-container">
                    <p>Tags: {{ formatedTags }}</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        src: {
            type: String,
            default: null,
        },
        header: {
            type: String,
            default: '',
        },
        alt: {
            type: String,
            default: '',
        },
        tag: {
            type: String,
            default: '',
        },
        disabled: {
            type: Boolean,
            default: false,
        },
        disableCard: {
            type: Boolean,
            default: false,
        },
        keywords: {
            type: String,
            default: '',
        }
    },
    components: {
    },
    computed: {
        computedWidth() {
            const block = this.$parent.$props.blocks;
            return `${100 / block}%`;
        },
        computedBlock () {
            const block = this.$parent.$props.blocks;
            return Math.floor(12 / block);
        },
        computeDisabled() {
            return this.disabled || !this.disableCard;
        },
        computedTags() {
            return this.tag ? this.tag.split(",").map(tag => tag.trim()) : [];
        },
        computedKeywords() {
            return this.keywords ? this.keywords.split(",").map(keyword => keyword.trim()) : [];
        },
        formatedTags() {
            let tagList = this.tag.split(",");
            tagList = tagList.map(tag => tag.trim());

            return tagList.join(", ");
        },
        hasTag() {
            return !!this.tag;
        }
    },
    methods: {
    },
    mounted() {
        this.isMounted = true;
    },
}
</script>

<style scoped>
    .article-container {
        display: flex;
        flex-direction: column;
        justify-content: start;
        align-items: center;
        border-style: solid;
        border-color: rgb(228, 228, 228);
        border-width: 0.1px;
        height: 100%;
        padding: 10px;
        background-color: white;
        border-radius: 8px;
    }


    .card-content-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        margin: 0;
        padding: 10px;
    }

    .card-header-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        top: 10;
        margin: 0;
        padding: 0px;
    }


    .card-header-container > * {
        font-size: 100%;
        font-weight: bold;
    }

    .tag-container {
        margin-top: auto;
        width: 100%;
        height: auto;
        display: inline-block;
        white-space: nowrap;
    }

    .tag-container > p {
        font-size: 14px;
        color: rgba(128, 128, 128, 0.638);
        margin: 0;
    }
</style>
