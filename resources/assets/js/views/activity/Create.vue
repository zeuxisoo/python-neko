<template>
    <div id="activity-create">
        <shake-error-panel>
            <div class="panel-heading">Create Activity</div>
            <div class="panel-body">
                <form class="form-horizontal">
                    <div class="form-group">
                        <div class="col-sm-12">
                            <select class="form-control" id="label" v-model="label">
                                <option v-for="label in labels" v-bind:value="label.id">{{ label.name }}</option>
                            </select>
                        </div>
                    </div>
                    <div class="form-group">
                        <div class="col-sm-12">
                            <input class="form-control" id="activity_at" placeholder="YYYY-mm-dd HH:ii:ss" v-model="activity_at">
                        </div>
                    </div>
                    <div class="form-group">
                        <div class="col-sm-12">
                            <textarea class="form-control" id="remark" placeholder="Remark" rows="5" v-model="remark"></textarea>
                        </div>
                    </div>
                    <div class="form-group">
                        <div class="col-sm-12">
                            <button type="button" class="btn btn-default" v-on:click="create">Create</button>
                        </div>
                    </div>
                </form>
            </div>
        </shake-error-panel>
    </div>
</template>

<style>
</style>

<script>
import ShakeErrorPanel from '../../components/ShakeErrorPanel'
import MessageHelper from '../../helpers/message'
import moment from 'moment'

export default {
    name: 'activity-create',

    mixins: [ShakeErrorPanel.mixin],

    components: {
        "shake-error-panel": ShakeErrorPanel.component
    },

    data() {
        return {
            label      : 1,
            labels     : [],
            activity_at: moment().format('YYYY-MM-DD HH:mm:ss'),
            remark     : ""
        }
    },

    created() {
        this.fetchLabels()
    },

    watch: {
        $route() {
            this.fetchLabels()
        }
    },

    methods: {
        create() {
            if (this.label === "") {
                this.shakeError("Please select label")
            }else if (this.activity_at === "") {
                this.shakeError("Please enter activity date and time")
            }else{
                this.$api.activity.create({
                    label_id   : this.label,
                    activity_at: this.activity_at,
                    remark     : this.remark
                }).then(response => {
                    MessageHelper.success('The activity created')

                    this.activity_at = moment().format('YYYY-MM-DD HH:mm:ss')
                }).catch(error => {
                    if (error.response) {
                        let data   = error.response.data
                        let errors = data.errors

                        if (errors) {
                            Object.keys(errors).forEach((key) => {
                                MessageHelper.error(errors[key].shift())
                                return
                            })
                        }else{
                            let message = 'message' in data ? data.message : 'Unknown errors'

                            MessageHelper.error(message)

                            return
                        }
                    }

                    console.log("Activity.Create.vue", error)
                })
            }
        },

        fetchLabels() {
            this.$api.activity
            .allLabel()
            .then(response => {
                let data       = response.data
                let labels     = data.data

                this.labels     = labels
            }).catch(error => {
                MessageHelper.error('Cannot fetch activity label list')
            })
        }
    }

}
</script>
