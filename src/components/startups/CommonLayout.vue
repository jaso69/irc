<template>
    <div
        :class="{
            'kiwi-startup-common--closing': closing,
            'kiwi-startup-common--no-bg': !backgroundImage,
        }" :style="styleCommon" class="kiwi-startup-common"
    >
        <div class="kiwi-startup-common-section kiwi-startup-common-section-connection">
            <slot name="connection" />
        </div>
        <div :style="styleInfo"
             class="kiwi-startup-common-section kiwi-startup-common-section-info"
        >
            <img src="../../../static/img/banner2.jpg" alt="">
        </div>
    <!-- <div class="kiwi-fontawesome-preload">
        <i class="fa fa-spinner" aria-hidden="true" />
    </div> -->
    </div>
</template>

<script>
'kiwi public';

export default {
    data() {
        return {
            closing: false,
        };
    },
    computed: {
        styleCommon() {
            let style = {};
            let options = this.$state.settings.startupOptions;

            if (options.infoBackground && this.$state.ui.app_width <= 850) {
                style['background-image'] = `url("${options.infoBackground}")`;
            }
            return style;
        },
        styleInfo() {
            let style = {};
            let options = this.$state.settings.startupOptions;

            if (options.infoBackground && this.$state.ui.app_width > 850) {
                style['background-image'] = `url("${options.infoBackground}")`;
            }
            return style;
        },
        backgroundImage() {
            return this.$state.settings.startupOptions.infoBackground || '';
        },
        infoContent() {
            return this.$state.settings.startupOptions.infoContent || '';
        },
    },
    methods: {
        close() {
            this.closing = true;
            let startApp = (event) => {
                this.$el.removeEventListener('transitionend', startApp);
                this.$state.persistence.watchStateForChanges();
                // Hacky to be using $parent but this component should only be used in a sepcific
                // scope within startup screens
                this.$parent.$emit('start');
            };

            this.$el.addEventListener('transitionend', startApp, false);
        },
    },
};
</script>

<style>

.kiwi-startup-common {
    height: 100%;
    text-align: center;
    display: flex;
}

.kiwi-startup-common-section {
    padding: 1em;
    box-sizing: border-box;
    height: 100%;
    overflow-x: hidden;
    overflow-y: auto;

    /* transition the 2 sections out when the page closes. right+left defaults */
    transition: transform 0.4s;
}

.kiwi-startup-common-section-connection {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 40%;
    max-width: 500px;
}

/** Right side */
.kiwi-startup-common-section-info {
    color: #fff;
    display: flex;
    flex: 1;
    align-items: center;
    justify-content: center;
}

.kiwi-startup-common-section-info-content {
    background: rgba(255, 255, 255, 0.74);
    margin: 2em;
    color: #1b1b1b;
    font-size: 1.5em;
    padding: 2em;
    border-radius: 5px;
    line-height: 1.6em;
}

.kiwi-fontawesome-preload {
    position: absolute;
    top: 0;
    left: -50px;
    width: 1px;
    height: 1px;
}

@media (min-width: 850px) {
    /** Closing - the wiping away of the screen **/
    .kiwi-startup-common--closing .kiwi-startup-common-section-connection {
        transform: translateX(-100%);
    }

    .kiwi-startup-common--closing .kiwi-startup-common-section-info {
        transform: translateX(100%);
    }
}

/** Smaller screen... **/
@media (max-width: 850px) {
    .kiwi-startup-common {
        font-size: 0.9em;
        display: block;
        overflow-y: auto;
    }

    .kiwi-startup-common-section {
        width: 100%;
        min-height: auto;
        max-width: none;
        height: auto;
        align-items: flex-start;
    }

    /** Closing - the wiping away of the screen **/
    .kiwi-startup-common--closing {
        transition: transform 0.3s;
        transform: translateY(100%);
    }

    .kiwi-startup-common-section-connection {
        padding-top: 2em;
    }

    .kiwi-startup-common-section-connection > * {
        max-width: 450px;
    }
}

/** Background / border switching between screen sizes **/
/* stylelint-disable  no-duplicate-selectors */
.kiwi-startup-common {
    background-size: 0;
    background-position: bottom;
}

.kiwi-startup-common-section-info {
    background-size: cover;
    background-position: bottom;
    border-left: 5px solid #42b992;
}

.kiwi-startup-common--no-bg .kiwi-startup-common-section-info {
    background-color: rgb(51, 51, 51);
}

/* stylint-enable */

@media (max-width: 850px) {
    /* Apply some flex so that the info panel fills the rest of the bottom screen */
    .kiwi-startup-common {
        background-size: cover;
    }

    .kiwi-startup-common-section-info {
        background-size: 0;
        border-left: none;
        flex: 1 0;
        display: block;
    }

    .kiwi-startup-common--no-bg .kiwi-startup-common-section-info {
        border-top: 5px solid #42b992;
    }
}

@media (max-width: 500px) {
    .kiwi-startup-common-section-info-content {
        margin: 1em;
    }
}
</style>
