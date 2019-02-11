<template>
    <v-app>
        <v-content>
                <v-container fill-height fluid ma-0 pa-3>
                    <v-layout row fill-height justify-center>
                        <v-flex xs12 sm8 md6 lg4 xl3>
                            <v-card class="grey elevation-7">
                                <div class="black">
                                    <v-card-text class="white--text text-xs-right"
                                                 style="font-size: 20px">
                                        {{text}}
                                    </v-card-text>
                                </div>
                                <row-button v-for="(buttonRow, index) in buttonRows"
                                            :key="index"
                                            :buttons="buttonRow"
                                            :buttons-types="buttonRowsTypes[index]"
                                            operations_color="orange"
                                            numbers_color="grey"
                                            disabled_color="grey darken-4"
                                            @clear-clicked="hasCurrentNumber ? clear($event) : null"
                                            @number-clicked="number_append($event)"
                                            @op-clicked="hasCurrentNumber && !hasOperatorDefined ? operation($event) : null"
                                            @equal-clicked="hasBothNumbers ? equal() : null"
                                            @percent-clicked="hasCurrentNumber && !hasOperatorDefined ? percent() : null"
                                            @plus-minus-clicked="hasCurrentNumber ? sign() : null"
                                            @dot-clicked="!hasDotOnCurrentNumber ? dot() : null"
                                ></row-button>
                            </v-card>
                        </v-flex>
                    </v-layout>
                </v-container>
        </v-content>
    </v-app>
</template>

<script>

    import RowButton from "./components/RowButton";

    export default {
        name: 'App',
        components: {RowButton},
        data() {
            return {
                text: '0',
                a: false,
                meta: {
                    numbers: {
                        '0': '',
                        '1': ''
                    },
                    operation: {
                        execute: null,
                        name: ''
                    }
                },
                numberIndex: '0',
                buttonRows: [
                    ['C', '+/-', '%', '/'],
                    ['1', '2', '3', '*'],
                    ['4', '5', '6', '+'],
                    ['7', '8', '9', '-'],
                    ['0', '.', '=']
                ]
            }
        },
        methods: {
            // update_numbers(index) {
            //   switch (index) {
            //       case '0':
            //           this.meta.numbers = {'0': }
            //   }
            // },
            meta_to_text() {
                if (this.numberIndex === 0) return this.meta.numbers['0'];
                return this.meta.numbers['0'] + this.meta.operation.name + this.meta.numbers['1']
            },
            clear: function () {
                this.text = '0';
                this.meta.numbers = {'0': '', '1': ''};
                this.meta.operation.execute = null;
                this.meta.operation.name = '';
                this.numberIndex = '0';
            },
            number_append: function (name) {
                if (this.text === '0') {
                    this.text = name;
                    this.meta.numbers[this.numberIndex] += name
                } else {
                    this.text += name;
                    this.meta.numbers[this.numberIndex] += name
                }
            },
            operation: function (op) {
                switch (op) {
                    case '+':
                        this.meta.operation.execute = (x, y) => x + y;
                        break;
                    case '-':
                        this.meta.operation.execute = (x, y) => x - y;
                        break;
                    case '*':
                        this.meta.operation.execute = (x, y) => x * y;
                        break;
                    case '/':
                        this.meta.operation.execute = (x, y) => x / y;
                        break;
                    default:
                        this.meta.operation.execute = null;
                }

                this.meta.operation.name = op;
                this.text += op;
                this.numberIndex = '1';
            },
            equal: function () {
                this.text = this.meta.operation.execute(
                    parseFloat(this.meta.numbers['0']),
                    parseFloat(this.meta.numbers['1'])
                );

                this.text = this.text.toString();

                this.meta.operation.execute = null;
                this.meta.operation.name = '';
                this.numberIndex = '0';

                this.meta.numbers = {'0': this.text, '1': ''};
            },
            percent: function () {
                this.text = parseFloat(this.meta.numbers['0']) / 100;
            },
            sign: function () {
                if (this.meta.numbers[this.numberIndex][0] === '-') {
                    this.meta.numbers[this.numberIndex] = this.meta.numbers[this.numberIndex].substring(1);
                    this.text = this.meta_to_text()
                } else {
                    this.meta.numbers[this.numberIndex] = '-' + this.meta.numbers[this.numberIndex];
                    this.text = this.meta_to_text()
                }
            },
            dot: function () {
                this.meta.numbers[this.numberIndex] += '.';
                this.text += '.';
            }
        }
        ,
        computed: {
            buttonRowsTypes() {
                return [
                    [this.hasCurrentNumber ? 1 : -1, this.hasCurrentNumber ? 1 : -1, this.hasCurrentNumber && !this.hasOperatorDefined ? 1 : -1, this.hasCurrentNumber && !this.hasOperatorDefined ? 1 : -1],
                    [0, 0, 0, this.hasCurrentNumber && !this.hasOperatorDefined ? 1 : -1],
                    [0, 0, 0, this.hasCurrentNumber && !this.hasOperatorDefined ? 1 : -1],
                    [0, 0, 0, this.hasCurrentNumber && !this.hasOperatorDefined ? 1 : -1],
                    [0, this.hasDotOnCurrentNumber ? -1 : 1, this.hasBothNumbers ? 1 : -1],
                ]
            }
            ,
            currentNumber() {
                return this.meta.numbers[this.numberIndex]
            }
            ,
            hasCurrentNumber() {
                return this.currentNumber !== ''
            },
            hasOperatorDefined() {
                return this.meta.operation.name !== ''
            }
            ,
            hasBothNumbers() {
                return this.meta.numbers['0'] !== '' && this.meta.numbers['1'] !== ''
            }
            ,
            hasDotOnCurrentNumber() {
                if (!this.hasCurrentNumber) return true;
                return this.currentNumber.indexOf('.') !== -1
            }
        }
    }
</script>
