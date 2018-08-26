<template>
    <div :id="'easy-autocomplete-'+_uid">
        <input type="text" v-bind:class="inputClass" v-model="i" @keyup="keyUp" @keyup.up="selector('up')" @keyup.down="selector('down')" @keyup.enter="showList = false" @click="showList = true"/>
        <ul class="list" v-show="showList">
           <li class="item" v-for="(item, index) in list" v-bind:key="index"  @click="select(index); showList = false" v-bind:class="selectedIndex === index ? 'selected' : false">
               <slot v-bind:item="item"></slot>
           </li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: "easy-autocomplete",
        props: {
            list: Array,
            input: '',
            inputClass: ''
        },
        data: function () {
            return {
                i: '',
                selectedIndex: null,
                showList: false
            }
        },
        mounted: function () {
            document.addEventListener("click", (evt) => {
                const flyoutElement = document.getElementById('easy-autocomplete-'+this._uid);
                let targetElement = evt.target;

                do {
                    if (targetElement == flyoutElement) {
                        return;
                    }
                    targetElement = targetElement.parentNode;
                } while (targetElement);

                this.showList = false;
            });
        },
        methods: {
            select(index){
                this.selected = this.list[index];
                this.i = this.selected[this.input]
                this.selectedIndex = index;
                this.$emit('selected', this.selected);
                setTimeout(()=>{
                    document.getElementById('easy-autocomplete-'+this._uid).getElementsByClassName('selected')[0].scrollIntoView({ behavior: 'smooth' });
                },50);

            },
            keyUp(event){
                this.showList = true;
                if (event.code !== 'ArrowLeft' && event.code !== 'ArrowRight' && event.code !== 'ArrowUp' && event.code !== 'ArrowDown'){
                    console.log('here')
                    this.$emit('keyup')
                }
            },
            selector(direction){
                if (!this.list[this.selectedIndex]){
                    this.selectedIndex = null;
                }

                if (direction === 'up'){
                    (this.selectedIndex === null || this.selectedIndex === 0) ? this.selectedIndex = this.list.length - 1 : this.selectedIndex -= 1;
                    this.select(this.selectedIndex);

                }

                if (direction === 'down'){
                    (this.selectedIndex === null || this.selectedIndex === this.list.length - 1) ? this.selectedIndex = 0 : this.selectedIndex += 1;
                    this.select(this.selectedIndex);
                }
            }
        }
    }
</script>

<style scoped>

    ul {
        padding: 0px;
        border-right: 1px solid #e8e8e8;
        border-bottom: 1px solid #e8e8e8;
        border-left: 1px solid #e8e8e8;
        max-height: 300px;
        overflow-y: scroll;
    }

    ul::-webkit-scrollbar {
        width: 2px;
    }

    ul::-webkit-scrollbar-thumb {
        background-color: darkgrey;
        outline: 1px solid slategrey;
    }

    li {
        cursor: pointer;
        list-style-type: none;
        padding: 10px;
        text-align: left;
    }

    .selected {
        background: #f5f5f5;;
    }
</style>