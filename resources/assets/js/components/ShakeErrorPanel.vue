<script>
import Vue from 'vue'
import MessageHelper from '../helpers/message'
import EventBus from '../helpers/event-bus'

const EventHub = new EventBus()

const component = Vue.extend({
    template: `
        <div class="panel panel-default" v-bind:class="{ 'shake': error, 'animated': error }">
            <slot></slot>
        </div>
    `,

    data() {
        return {
            error: false,
        }
    },

    created() {
        EventHub.on("shake", (message) => {
            MessageHelper.error(message)

            this.error = true;
            setTimeout(function() {
              this.error = false
            }.bind(this), 1000)
        })
    },

    beforeDestroy() {
        EventHub.off("shake")
    }
})

const mixin = {
    methods: {
        shakeError(message) {
            EventHub.emit("shake", message)
        }
    }
}

export default {
    component,
    mixin
}
</script>
