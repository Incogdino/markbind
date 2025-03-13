<template>
    <div class="grid-container">
        <span v-if="isSearchable" class="search-bar">
            <template>
                <input
                    v-model="value"
                    type="text"
                    class="form-control"
                    :placeholder="placeholder"
                    @input="update"
                />
            </template>
        </span>
        <div class="container">
            <div class="row justify-content-starts gy-3">
                <slot></slot> 
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: {
        blocks: {
            type: String,
            default: '2',
        },
        placeholder: {
            type: String,
            default: 'Search',
        }
    },
    computed: {
    },
    methods: {
        update() {
            const regexes = this.value.split(' ')
                .filter(searchKeyword => searchKeyword !== '')
                .map(searchKeyword => searchKeyword.replace(/[.*+?^${}()|[\]\\]/g, '\\$&'))
                .map(searchKeyword => new RegExp(searchKeyword, 'ig'));

            this.$children.forEach((child) => {
                if (child.$props.disabled) {
                    return;
                }

                if(this.value == '' && !child.$props.disabled) {
                    child.$props.disableCard = false;
                    return;
                }

                const tags = child.computedTags;
                const keywords = child.computedKeywords;   
                const searchTarget = tags.join(' ') + keywords.join(' ');
                
                for (const regex of regexes) {
                    if (!searchTarget.match(regex)) {
                        child.$props.disableCard = true;
                        break;
                    }
                }
            });
        }
    },
    data() {
        return {
            isSearchable: false,
            value: '',
        };
        
    },
    mounted() {
        this.isMounted = true;

        this.isSearchable = this.$slots.searchable !== undefined && this.$slots.searchable[0].text !== "false";
    },
}

</script>

<style scoped>
.search-bar {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 0;
    padding: 5px;
}

.row {
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}

.grid-container {
    background-color: rgb(231, 231, 231);
    border-radius: 1%/2%;
    padding: 20px;
}

.container {
    display: flex;
    justify-content: center;
    align-items: center;
}

.form-control {
    min-width: 12.7em;
    max-width: 25.4em; /* twice of min-width, to accommodate a range of lengths */
}
</style>
