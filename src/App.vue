<script>
let id;

export default {
    mounted() {
        this.loadExistingTapas();
    },
    data() {
        return {
            tapaStorageKey: 'tapas',
            tapas: []
        }
    },
    methods: {
        loadExistingTapas() {
            id = localStorage.length;

            if(!localStorage.getItem(this.tapaStorageKey)) {
                id = 0;
                return;
            }

            this.tapas = JSON.parse(localStorage.getItem(this.tapaStorageKey));
            
            // console.log(this.tapas);
            // ^ Used for testing

            id = this.tapas.length;
        },
        addTapasEvent(e) {
            e.preventDefault();

            const tapasInput = this.$refs['tapas-input'].value.trim();
            // ^ Takes the input, trims it of any starting and/or ending empty characters

            if(this.isEmpty(tapasInput)) return;

            this.tapas.push({
                id: id,
                text: tapasInput,
                checked: false
            });

            localStorage.setItem(this.tapaStorageKey, JSON.stringify(this.tapas));

            id++;
        },
        isEmpty(str) {
            if(str.length === 0) return true;

            for(let i=0; i < str.length; i++) {
                if(str[i] !== ' ') return false;
            }

            return true;
        }, 
        tapaListClickEvent(e) {
            const eventTarget = e.target;
            
            if(eventTarget.nodeName !== 'LABEL') return;

            const index = eventTarget.getAttribute('for').split('-')[1];
            // ^ Getting the for attribute on the label element which contains the index
            // for the tapa in the label

            this.tapas[index].checked = !this.tapas[index].checked;
            localStorage.setItem(this.tapaStorageKey, JSON.stringify(this.tapas));

            this.tapas[index].checked = !this.tapas[index].checked;
            // ^ This is done 2 times because v-modal updated the value after this function
            // finishes, so in order to not get things mixed up I change it to the value
            // it will have and return it back to it's previous value, and then v-modal
            // will update it to the value it should have once more
        },
        tapaIdMaker(id) {
            return 'tapa-'+ id;
        }
    }
}
</script>

<template>
   <!--
      Fish SVG Cred:
      https://thenounproject.com/search/?q=fish&i=589236
    -->

   <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" x="0px" y="0px" viewBox="0 0 512 512" enable-background="new 0 0 512 512" xml:space="preserve"><g><path d="M495.9,425.3H16.1c-5.2,0-10.1,2.9-12.5,7.6c-2.4,4.7-2.1,10.3,0.9,14.6l39,56.4c2.6,3.8,7,6.1,11.6,6.1h401.7   c4.6,0,9-2.3,11.6-6.1l39-56.4c3-4.3,3.3-9.9,0.9-14.6C506,428.2,501.1,425.3,495.9,425.3z M449.4,481.8H62.6L43,453.6H469   L449.4,481.8z"/><path d="M158.3,122c7.8,0,14.1-6.3,14.1-14.1V43.4c0-7.8-6.3-14.1-14.1-14.1c-7.8,0-14.1,6.3-14.1,14.1v64.5   C144.2,115.7,150.5,122,158.3,122z"/><path d="M245.1,94.7c7.8,0,14.1-6.3,14.1-14.1V16.1c0-7.8-6.3-14.1-14.1-14.1C237.3,2,231,8.3,231,16.1v64.5   C231,88.4,237.3,94.7,245.1,94.7z"/><path d="M331.9,122c7.8,0,14.1-6.3,14.1-14.1V43.4c0-7.8-6.3-14.1-14.1-14.1s-14.1,6.3-14.1,14.1v64.5   C317.8,115.7,324.1,122,331.9,122z"/><path d="M9.6,385.2c5.3,2.8,11.8,1.9,16.2-2.2l50.6-47.7c56.7,46.5,126.6,71.9,198.3,71.9c0,0,0,0,0,0   c87.5,0,169.7-36.6,231.4-103.2c5-5.4,5-13.8,0-19.2c-61.8-66.5-144-103.2-231.4-103.2c-72,0-142.2,25.6-199,72.5l-50-47.1   c-4.4-4.1-10.9-5-16.2-2.2c-5.3,2.8-8.3,8.7-7.4,14.6l11.6,75L2.2,370.6C1.3,376.5,4.2,382.4,9.6,385.2z M380.9,230.8   c34.9,14.3,67.2,35.7,95.3,63.6c-10.1,10-20.8,19.2-31.9,27.5c-22.4-3.3-29.6-8.8-30.7-9.7c-4-5.7-11.8-7.7-18.1-4.4   c-6.9,3.6-9.5,12.2-5.9,19.1c1.9,3.5,7.3,10.3,22.4,16c-10.1,5.7-20.5,10.7-31.1,15.1C352.4,320.2,352.4,268.6,380.9,230.8z    M36.3,255.6l29.4,27.7c5.3,5,13.6,5.1,19.1,0.3c53.2-47.6,120.7-73.7,190-73.7c26.9,0,53.2,3.9,78.5,11.3   c-29.3,44.6-29.3,102,0,146.6c-25.3,7.4-51.6,11.3-78.5,11.3c-69,0-136.3-26-189.4-73.2c-2.7-2.4-13.4-6.3-19.1,0.3l-30.1,28.3   l5.7-40C42.2,293,36.3,255.6,36.3,255.6z"/><circle cx="398.8" cy="273.8" r="14.1"/></g></svg>

   <div class="container">
        <h2>Local Tapas</h2>

        <ul class="tapas-list" @click="tapaListClickEvent">
            <li class="tapas-label" v-if="tapas.length === 0">Loading Tapas...</li>
            <li v-else v-for="tapa in tapas" :key="tapa.id">
                <input class="tapas-checkbox" type="checkbox" :name="tapaIdMaker(tapa.id)" :id="tapaIdMaker(tapa.id)" v-model="tapa.checked">
                <label class="tapas-label" :for="tapaIdMaker(tapa.id)">{{tapa.text}}</label>
            </li>
        </ul>

        <form class="tapas-form" @submit="addTapasEvent">
            <input type="text" name="tapas-input" id="tapas-input" ref="tapas-input">
            <button class="submit-tapas" type="submit">+ Add Item</button>
        </form>
   </div>
</template>

<style scoped>
    svg {
        fill: var(--svg-character-color);
        background: var(--svg-character-bg-color);
        padding: 2.2rem;
        border-radius: 50%;
        width: 13rem;
        margin-bottom: 3.5rem;
    }

    .container {
        background-color: var(--container-bg-color);
        padding: 2rem;
        box-shadow: var(--container-box-shadow);
    }

    .container > h2 {
        text-transform: uppercase;
        text-align: center;
        font-size: 1.5rem;
    }

    .tapas-list {
        list-style: none;
        margin-bottom: 1rem;
        font-size: 1.1rem;
        isolation: isolate;
    }

    .tapas-list > li {
        display: flex;
        position: relative;
    }

    .tapas-form {
        display: flex;
        justify-content: center;
        align-content: center;
    }

    #tapas-input {
        margin-right: .4rem;
        font-size: 1rem;
        padding: .5rem .3rem .5rem .3rem;
    }

    .submit-tapas {
        font-size: .9rem;
        padding: .7rem .3rem .7rem .3rem;
        cursor: pointer;
        font-weight: bold;
        background-color: var(--submit-tapas-bg-color);
        outline: none;
        border: none;
    }

    .submit-tapas:hover {
        outline: 2px solid black;
    }

    .tapas-label {
        min-width: 100%;
        min-height: 100%;
        padding-top: 1.2rem;
        padding-bottom: .5rem;
        border-bottom: var(--tapas-list-item);
        font-weight: bold;
        cursor: pointer;
    }

    .tapas-checkbox {
        position: absolute;
        visibility: hidden;
        z-index: -1;
    }

    .tapas-checkbox + .tapas-label::before {
        content: '🍴';
        margin-right: .5rem;
    }

    .tapas-checkbox:checked + .tapas-label::before {
        content: '🌮';
        margin-right: .5rem;
    }
</style>