<template>
    <span><span :style="style" :class="class">{{words}}</span>
        <span v-show="cursorDisplay" class="vue-typed-string-cursor"><slot></slot></span>
    </span>
</template>
<style>
    .vue-typed-string-cursor {
        opacity: 1;
        -webkit-animation: vue_typed_string_cursor_blink 0.7s infinite;
        -moz-animation: vue_typed_string_cursor_blink 0.7s infinite;
        animation: vue_typed_string_cursor_blink 0.7s infinite;
    }

    @keyframes vue_typed_string_cursor_blink {
        0% {
            opacity: 1;
        }
        50% {
            opacity: 0;
        }
        100% {
            opacity: 1;
        }
    }

    @-webkit-keyframes vue_typed_string_cursor_blink {
        0% {
            opacity: 1;
        }
        50% {
            opacity: 0;
        }
        100% {
            opacity: 1;
        }
    }

    @-moz-keyframes vue_typed_string_cursor_blink {
        0% {
            opacity: 1;
        }
        50% {
            opacity: 0;
        }
        100% {
            opacity: 1;
        }
    }

</style>

/*!
* vue-loader v0.0.1 (https://github.com/johnnyGoo/vue-loader)
* Author: Johnny chen
*
* Copyright 2013-2015 Johnny chen
*/
<script>

    if (!window.Smart) {
        throw 'VueTypedStrings required smart.js'
    }
    var Smart = window.Smart;
    var Sound = Smart.Sound;
    var Event = Smart.Event;

    // 注册
    export default {
        // 声明 props
        props: {
            playing: {
                type: Boolean,
                default: false
            }, loop: {
                type: Boolean,
                default: true
            }, speed: {
                type: Number,
                default: 200
            }, strings: {
                type: Array,
                default: function () {
                    return [];
                }
            }, style: {
                type: String,
                default: ''
            }, class: {
                type: String,
                default: ''
            }, cursor: {
                type: Number,
                default: 1//cursor 0不显示光标 1 正常 2 stoped 后隐藏光标 3 输入不显示stoped后显示
            }
        },
        data: function () {
            return {
                music: null,
                playing: false,
                format: [],
                words: '',
                currentString: 0,
                currentCount: 0,
                direct: 1,
                stoped: false,
                iv: 0
            }
        },
        watch: {
            playing: function (nv, ov) {

                if (nv) {
                    this.doplay();
                } else {
                    clearTimeout(this.iv)
                }
            }
        },

        methods: {
            doplay: function () {
                if (this.stoped) {
                    this.stoped = false;
                    this.direct = -1;
                }
                this.enterFrame.call(this);
                this.$emit('played');

            }

            , enterFrame: function () {
                var me = this;
                var string = this.strings[this.currentString];
                this.currentCount += this.direct;

                me.words = string.words.substr(0, this.currentCount);

                var nextTime = string.speed;

                if (this.direct < 0) {
                    nextTime = nextTime / 3
                }

                if (me.words == string.words) {
                    if (this.strings.length > this.currentString + 1 || this.loop) {
                        this.direct = -1;
                        nextTime = string.wait || 1000
                    } else {
                        this.playing = false;
                        this.stoped = true;
                        this.$emit('stoped');
                    }
                } else if (this.currentCount == 0 && this.direct == -1) {
                    if (this.strings.length > this.currentString + 1) {
                        this.currentString++;
                    } else {
                        this.currentString = 0;
                    }
                    this.direct = 1;

                }

                function next() {
                    me.enterFrame.call(me);
                }

                if (this.playing) {
                    this.iv = setTimeout(next, nextTime)
                }


            }
        },
        computed: {
            cursorDisplay: function () {
                if (this.cursor == 0) {
                    return false

                }else if(this.cursor == 1) {
                    return true
                }else if(this.cursor == 2) {
                    return !this.stoped;
                }else if(this.cursor == 3) {
                    return this.stoped;
                }

            }
        },
        ready: function () {
            console.log(this.style)
            if (this.playing) {
                this.doplay();
            }

        }


    }


</script>