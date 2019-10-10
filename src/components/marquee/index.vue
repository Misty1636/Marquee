<template>
    <div
        ref="container"
        class="wrap"
        :style="wrapStyle"
        @mouseover="isPaused = true"
        @mouseleave="isPaused = false"
    >
        <div
            ref="mainbox"
            :class="['marquee',`marquee-animate${direction}`, { paused : isPaused }]"
            :style="`left: ${containerWidth}px; top: ${containerHeight}px;  animationDuration: ${currentSpeed}s`"
            @transitionend="startChangeStyle"
            v-html="filterData"
        >
        </div>
    </div>
</template>

<script>
export default {
    props: {
        data: {
            type: Array,
            required: true,
        },
        speed: {
            type: Number,
            default: 100,
        },
        direction: {
            type: String,
            default: 'Left',
        },
    },
    data() {
        return {
            totalWidth: 0,
            totalHeight: 0,
            containerWidth: 0,
            containerHeight: 0,
            isPaused: false,
            currentSpeed: 0,
            preDirection: 'x',
        };
    },
    computed: {
        wrapStyle() {
            let wrapClass = '';
            if (this.direction === 'Left' || this.direction === 'Right') {
                wrapClass = 'padding: 10px 0;';
            }

            if (this.direction === 'Top' || this.direction === 'Bottom') {
                wrapClass = 'height: 350px;';
            }

            return wrapClass;
        },
        filterData() {
            let joinStyle = '<br><br>';
            if (this.direction === 'Left' || this.direction === 'Right') {
                joinStyle = '&nbsp;&nbsp;&nbsp;&nbsp';
            }
            return this.data.map(item => item.content).join(joinStyle);
        },
    },
    watch: {
        speed() {
            this.getTotalStyle();
            this.getDirection();
            this.getKeyframes();
        },
        direction() {
            let currentDirection = '';

            if (this.direction === 'Left' || this.direction === 'Right') {
                currentDirection = 'x';
            }

            if (this.direction === 'Top' || this.direction === 'Bottom') {
                currentDirection = 'y';
            }

            if (this.preDirection === currentDirection) {
                this.getTotalStyle();
                this.getDirection();
                this.getKeyframes();
            } else {
                this.preDirection = currentDirection;
            }
        },
    },
    mounted() {
        this.getTotalStyle();
        this.getDirection();
        this.getKeyframes();

        window.onresize = () => {
            if (this.direction === 'Left' || this.direction === 'Right') {
                this.getDirection();
            }

            if (this.direction === 'Top' || this.direction === 'Bottom') {
                this.getTotalStyle();
                this.getDirection();
                this.getKeyframes();
            }
        };
    },
    methods: {
        startChangeStyle() {
            this.getTotalStyle();
            this.getDirection();
            this.getKeyframes();
        },
        getTotalStyle() {
            this.totalWidth = 0;
            this.totalHeight = 0;
            if (this.direction === 'Left' || this.direction === 'Right') {
                this.totalWidth = this.$refs.mainbox.scrollWidth;
                // console.log('lr', this.totalWidth, this.totalHeight);
            }

            if (this.direction === 'Top' || this.direction === 'Bottom') {
                this.totalHeight = this.$refs.mainbox.offsetHeight;
                // console.log('tp', this.totalWidth, this.totalHeight);
            }
            // const ary = Object.keys(this.$refs);
            // ary.forEach((item) => {
            //     if (item.includes('marquee')) {
            //         this.totalWidth += this.$refs[item][0].getBoundingClientRect().width;
            //         this.totalHeight += this.$refs[item][0].getBoundingClientRect().height;
            //     }
            // });
        },
        getDirection() {
            const dir = this.direction;
            const box = this.$refs.container;

            if (dir === 'Left' || dir === 'Right') {
                this.containerWidth = dir === 'Left' ? box.offsetWidth : -this.totalWidth;
                this.containerHeight = 0;
                this.currentSpeed = Math.round((this.totalWidth / this.speed) * 100) / 100;
            }
            if (dir === 'Bottom' || dir === 'Top') {
                this.containerHeight = dir === 'Top' ? box.offsetHeight : -this.totalHeight;
                this.totalWidth = box.offsetWidth;
                this.containerWidth = 0;
                this.currentSpeed = Math.round(((this.totalHeight + box.offsetHeight) / this.speed) * 100) / 100;
            }
        },
        getKeyframes() {
            const frame = document.styleSheets;
            const ary = Object.keys(frame);

            ary.forEach((item) => {
                const csslist = Object.keys(frame[item].cssRules);
                csslist.forEach((list) => {
                    const currentList = frame[item].cssRules[list];

                    if (currentList.name && currentList.name.includes('animateTop')) {
                        if (typeof currentList.deleteRule === 'function') {
                            currentList.deleteRule(0);
                        } else {
                            currentList.removeRule(0);
                        }

                        if (typeof currentList.appendRule === 'function') {
                            currentList.appendRule(`100% { top: ${-this.totalHeight}px; }`);
                        } else {
                            currentList.insertRule(`100% { top: ${-this.totalHeight}px; }`);
                        }
                    }

                    if (currentList.name && currentList.name.includes('animateBottom')) {
                        if (typeof currentList.deleteRule === 'function') {
                            currentList.deleteRule(0);
                        } else {
                            currentList.removeRule(0);
                        }

                        if (typeof currentList.appendRule === 'function') {
                            currentList.appendRule(`100% { top: ${this.$refs.container.offsetHeight}px; }`);
                        } else {
                            currentList.insertRule(`100% { top: ${this.$refs.container.offsetHeight}px; }`);
                        }
                    }

                    if (currentList.name && currentList.name.includes('animateRight')) {
                        if (typeof currentList.deleteRule === 'function') {
                            currentList.deleteRule(0);
                        } else {
                            currentList.removeRule(0);
                        }

                        if (typeof currentList.appendRule === 'function') {
                            currentList.appendRule(`100% { left: ${this.$refs.container.offsetWidth}px; }`);
                        } else {
                            currentList.insertRule(`100% { left: ${this.$refs.container.offsetWidth}px; }`);
                        }
                    }


                    if (currentList.name && currentList.name.includes('animateLeft')) {
                        if (typeof currentList.deleteRule === 'function') {
                            currentList.deleteRule(0);
                        } else {
                            currentList.removeRule(0);
                        }

                        if (typeof currentList.appendRule === 'function') {
                            currentList.appendRule(`100% { left: ${-this.totalWidth}px; }`);
                        } else {
                            currentList.insertRule(`100% { left: ${-this.totalWidth}px; }`);
                        }
                    }
                });
            });
        },
    },
};
</script>

<style lang="scss" scoped>
.wrap {
    background: #343A40;
    cursor: pointer;
    overflow: hidden;
}

.marquee-content {
    white-space: nowrap;
    position: relative;
}

.marquee {
    position: relative;
    color: #FFF;
    will-change: transform;

    &.paused {
        animation-play-state: paused;
    }

    &-animateLeft,
    &-animateRight,
    &-animateTop,
    &-animateBottom {
        transition: padding 0.002s;
        animation-timing-function: linear;
        animation-iteration-count: infinite;
    }

    &-animateLeft,
    &-animateRight {
        padding-right: 0.01px;
        white-space: nowrap;
    }

    &-animateTop,
    &-animateBottom {
        line-height: 1.2;
    }

    &-animateLeft {
        animation-name: animateLeft;
    }

    &-animateRight {
        animation-name: animateRight;
    }

    &-animateTop {
        animation-name: animateTop;
    }

    &-animateBottom {
        animation-name: animateBottom;
    }
}

@keyframes animateLeft {
    to {
        left: -100%;
    }
}

@keyframes animateRight {
    to {
        left: 100%;
    }
}

@keyframes animateTop {
    to {
        top: -100%;
    }
}

@keyframes animateBottom {
    to {
        top: 100%;
    }
}
</style>
