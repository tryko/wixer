<template>
    <section class="location-map">
        <div class="content" :style="cmp.style">
            <!--map cmp-->
            <map-cmp :position="position"></map-cmp>
            <div class="address">
                <!--text area-->
                <h2>{{cmp.data.address.line1}}</h2>
                <h3>{{cmp.data.address.line2}}</h3>
                <h3>{{cmp.data.address.line3}}</h3>
                <md-button class="md-fab md-clean  md-mini color-picker-btn">
                    <md-icon>navigation</md-icon>
                    <md-tooltip md-direction="top">Take me there!</md-tooltip>
                </md-button>
            </div>
    
        </div>
    
        <!-- edit button-->
        <md-button class="md-fab edit-btn md-mini md-warn" @click="enterEditMode" v-if="isEditable">
            <md-icon>edit</md-icon>
        </md-button>
        <transition name="fade">
            <div v-if="isEditMode" class="edit-console">
                <map-toolbar :cmp="cmp" @update="updateCmp"></map-toolbar>
                <txt-toolbar :cmp="cmp" @update="updateCmp"></txt-toolbar>
                <general-edit :cmp="cmp" :isFirst="isFirst" :isLast="isLast" @delete="deleteCmp" @move="moveCmp" @update="updateCmp"></general-edit>
    
            </div>
        </transition>
    </section>
</template>

<script>
import MapCmp from './MapCmp'
import MapToolbar from '../toolbars/MapToolbar'
import TxtToolbar from '../toolbars/TxtToolbar'
import GeneralEdit from '../toolbars/generalEditToolbar'

const ZOOM_CLOSE = 18;

export default {
    name: 'LocationMap',
    props: ['cmp', 'isFirst', 'isLast', 'isEditable'],
    components: {
        TxtToolbar,
        GeneralEdit,
        MapCmp,
        MapToolbar
    },
    data() {
        return {
            isEditMode: false,
            color: "",

        }
    },
    computed: {
        cmpToEdit() {
            return JSON.parse(JSON.stringify(this.cmp))
        },
        position() {
            return this.cmp.data.position
        },
    },
    methods: {
        moveCmp(isUp) {
            this.$store.dispatch({ type: "moveCmp", cmp: this.cmpToEdit, isUp });
        },
        deleteCmp() {
            this.isEditMode = false;
            this.$store.dispatch({ type: "deleteCmp", cmp: this.cmpToEdit });
        },
        enterEditMode() {
            this.isEditMode = !this.isEditMode
        },
        updateCmp(updatedCmp) {
            this.$store.dispatch({ type: "updateCmp", cmp: updatedCmp });
        },
        updateColor: function (event) {
            this.color = event.color;
        },
    }
}
</script>


<style scoped>
.location-map {
    transition: all .5s;
    position: relative;
    margin-top: 5px;
    transition: all .5s;
}

.content {
    height: 240px;
    display: flex;
    align-items: center;
}

.address {
    margin: 0 auto;
    text-align: center;
}

p {
    margin: 0;
    padding: 0;
}

.edit-btn {
    position: absolute;
    left: 85%;
    top: 40%;
    opacity: 0.2;
    transition: all .5s;
}

.edit-btn:hover {
    opacity: 1;
    cursor: pointer;
}

.edit-console {
    display: flex;
    flex-flow: row wrap;
    justify-content: center;
    align-content: space-between;
    /*background: lightgray;*/
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity .5s
}

.fade-enter,
.fade-leave-to {
    opacity: 0
}
</style>
