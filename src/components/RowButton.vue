<template>
    <v-layout row fill-height align-space-around>
        <v-flex xs3 :xs6="button === '0'" v-for="(button, index) in buttons" :key="index" :class="color(index)">
            <v-container fill-height pa-2>
                <v-btn block style="height: 100%; width: 100%; min-width: 0;" @click="buttonWasClicked(button)">{{button}}</v-btn>
            </v-container>
        </v-flex>
    </v-layout>
</template>

<script>
    export default {
        props: {
            buttons: {
                type: Array,
                required: true,
                default: function () {
                    return []
                }
            },
            buttonsTypes: {
                type: Array,
                required: true,
                default: function () {
                    return [0, 0, 0, 1]
                }
            },
            operations_color: {
                type: String,
                required: true,
                default: 'orange'
            },
            numbers_color: {
                type: String,
                required: true,
                default: 'grey'
            },
            disabled_color: {
                type: String,
                required: true,
                default: 'grey darken-4'
            }
        },
        name: "RowButton",
        methods: {
            buttonWasClicked: function (name) {
                switch (name) {
                    case 'C':
                        this.$emit('clear-clicked', name);
                        break;
                    case '+/-':
                        this.$emit('plus-minus-clicked', name);
                        break;
                    case '%':
                        this.$emit('percent-clicked', name);
                        break;
                    case '/':
                    case '*':
                    case '+':
                    case '-':
                        this.$emit('op-clicked', name);
                        break;
                    case '=':
                        this.$emit('equal-clicked', name);
                        break;
                    case '.':
                        this.$emit('dot-clicked', name);
                        break;
                    default:
                        this.$emit('number-clicked', name);
                        break;
                }
            },
            color: function (index) {
                switch (this.buttonsTypes[index]) {
                    case 0:
                        return this.numbers_color;
                    case 1:
                        return this.operations_color;
                    case -1:
                        return this.disabled_color;
                }
            }
        }
    }
</script>

<style scoped>

</style>