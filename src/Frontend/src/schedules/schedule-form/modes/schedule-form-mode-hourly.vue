<template>
    <div>
        <div class="form-group">
            <label>{{ $t('Hours') }}</label>
            <div class="row">
                <div class="col-md-3"
                    v-for="hour in [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]">
                    <div class="checkbox"
                        style="margin-top: 0">
                        <label>
                            <input type="checkbox"
                                :value="hour"
                                v-model="hours"
                                @change="updateTimeExpression()">
                            <span v-html="prepend0IfLessThan10(hour) + ':' + prepend0IfLessThan10(minute)"></span>
                        </label>
                    </div>
                </div>
            </div>
        </div>
        <div class="form-group">
            <label>{{ $t('Minute') }}</label>
            <input type="number"
                class="form-control"
                step="5"
                min="0"
                max="55"
                v-model="minute"
                @change="updateTimeExpression()">
        </div>
    </div>
</template>

<script type="text/babel">
    import {roundTo5} from "../schedule-helpers";
    import {mapState} from "vuex";
    export default {
        name: 'schedule-form-mode-hourly',
        data() {
            return {
                hours: [],
                minute: 0,
            };
        },
        methods: {
            prepend0IfLessThan10(value) {
                return value < 10 ? '0' + value : value;
            },
            updateTimeExpression() {
                this.minute = roundTo5(this.minute);
                if (this.hours.length) {
                    this.hours = this.hours.sort((a, b) => a - b);
                    let cronExpression = (this.minute || 0) + ' ' + this.hours.join(',') + ' * * *';
                    this.$store.dispatch('updateTimeExpression', cronExpression);
                } else {
                    this.$store.dispatch('updateTimeExpression', '');
                }
            }
        },
        mounted() {
            let current = this.timeExpression.match(/^([012345]+) ([0-9,]+)/);
            if (current) {
                this.minute = current[1];
                this.hours = current[2].split(',');
            }
            this.updateTimeExpression();
        },
        computed: mapState(['timeExpression']),
    };
</script>
