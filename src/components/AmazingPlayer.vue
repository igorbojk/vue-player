<template>
    <div
        class="amazing-player"
        :class="{'disabled': !isLoaded}">
        <div class="header">
            <div class="song-image">
                <div
                    class="play-button"
                    @click="playToggle">
                    <img :src="require('../assets/icons/streamline-icon-entertainment-control-button-play@.svg')" alt="play-btn">
                </div>
            </div>
            <div class="info">
                <div class="logo">
                    <img :src="require('../assets/img/vurbl_horiz-white-logo@2x.png')" alt="play-btn">
                </div>
                <div class="description">
                    Interview with THe Rock & Oprah Winfrey talking about Life & Hollywood
                </div>
                <div class="creator">
                    <div class="creator-avatar">

                    </div>
                    <div class="creator-name">
                        Audio Creator / Station...
                    </div>
                    <img
                        class="creator-show-more-btn"
                        :src="require('../assets/icons/np_arrow-down_2022881_F2F2F2.svg')"
                        alt="show-more-btn">
                </div>
            </div>
        </div>
        <div class="controls">
            <div
                class="controls-button"
                @click="back">
                <span>15</span>
            </div>
            <div
                class="controls-play"
                @click="playToggle">
                <img :src="require('../assets/icons/play.svg')" alt="play-btn">
            </div>
            <div
                class="controls-button"
                @click="forward">
                <span>15</span>
            </div>
        </div>
        <div class="progress">
            <div class="time-info">
                {{ currentTime }}
            </div>
            <div
                class="progress-time"
                @click="goTo">
                <div
                    class="progress-time-fill"
                    :style="{ width: progressPercentage }"></div>
                <div
                    class="progress-time-point"
                    :style="{ left: progressPercentage }"
                ></div>
            </div>
            <div class="time-info">
                {{  duration }}
            </div>
        </div>

        <video ref="playerRef" muted="muted" controls></video>
    </div>
</template>

<script>
import Hls from "hls.js";

export default {
    data() {
        return {
            hlsSource: 'https://media1.vurbl.com/audio/upload/1UymCsmBylG/1609460549/wings-127km4a_311522ad9f.m3u8',
            player: null,
            isPlaying: false,
            currentTimeSeconds: 0,
            durationSeconds: 0,
            isLoaded: false
        };
    },

    async mounted() {
        this.player = this.$refs.playerRef;

        const hlsVideoHandler = () => {
            if (Hls.isSupported()) {
                let hls = new Hls();
                hls.loadSource(this.hlsSource);
                hls.attachMedia(this.player);
                hls.on(Hls.Events.MEDIA_ATTACHED, async () => {
                    this.player.muted = false;
                });
            }
        };

        await hlsVideoHandler();

        this.player.addEventListener("loadeddata", this.setupInitialData);

        this.player.addEventListener("timeupdate", this.updateTimer);
    },

    beforeDestroy() {
        this.player.removeEventListener('timeupdate', this.updateTimer, false);
        this.player.removeEventListener('loadeddata', this.setupInitialData, false);
    },

    computed: {
        /**
         * @return {string}
         */
        currentTime() {
            return this.formatTime(this.currentTimeSeconds);
        },

        /**
         * @return {string}
         */
        duration() {
            return this.formatTime(this.durationSeconds);
        },

        /**
         * @return {string}
         */
        progressPercentage() {
            const time = Math.floor(this.currentTimeSeconds / parseInt(this.durationSeconds) * 100) || 0;
            return `${time}%`;
        }
    },

    methods: {
        async playToggle () {
            if (this.isPlaying) {
                await this.player.pause();
                this.isPlaying = false;
            } else {
                await this.player.play();
                this.isPlaying = true;
            }
        },

        back () {
            this.player.currentTime -= 15
        },

        forward () {
            this.player.currentTime += 15
        },

        /**
         * @param {number} secs
         * @return {string}
         */
        formatTime(secs) {
            const minutes = Math.floor(secs / 60) || 0;
            let seconds = Math.floor(secs - minutes * 60) || 0;

            if (seconds < 10) {
                seconds = `0${seconds}`
            }

            return `${minutes}:${seconds}`;
        },

        goTo(e) {
            const el = e.target.getBoundingClientRect();
            const seekPos = (e.clientX - el.left) / el.width;

            this.player.currentTime = parseInt(this.player.duration * seekPos);
        },

        updateTimer() {
            this.currentTimeSeconds = this.player.currentTime;
        },
        
        setupInitialData() {
            this.durationSeconds = this.player.duration;
            this.currentTimeSeconds = this.player.currentTime;
            this.isLoaded = true;
        }
    }
}
</script>

<style scoped lang="scss">
    .amazing-player{
        font-family: Rubik;
        width: 344px;
        height: 205px;
        padding: 13px 7px 23px 12px;
        background-color: #200f42;
        &.disabled{
            opacity: 0.7;
            pointer-events: none;
        }
        video{
            display: none;
        }
        .header{
            display: flex;
            flex-wrap: nowrap;
            margin-bottom: 12px;
        }
        .song-image{
            flex: none;
            width: 80px;
            height: 80px;
            padding: 18px 20px 22px 19px;
            opacity: 0.81;
            display: flex;
            align-items: center;
            justify-content: center;
            background: red;
            background-image: linear-gradient(to top, rgba(9, 9, 9, 0), #161616);
            .play-button{
                width: 41px;
                height: 40px;
                padding: 11px 10px 12px 15px;
                background-color: #ffbd9e;
                border-radius: 50%;
                display: flex;
                align-items: center;
                justify-content: center;
                cursor: pointer;
                &:hover{
                    opacity: .9;
                }
                img{
                    width: 16px;
                }
            }
        }
        .info{
            display: flex;
            flex-direction: column;
            height: 80px;
            padding-left: 9px;
            .logo{
                margin-bottom: 9px;
                text-align: left;
                img{
                    height: 10px;
                }
            }
            .description{
                width: 231px;
                height: 48px;
                font-size: 12px;
                font-weight: normal;
                font-stretch: normal;
                font-style: normal;
                line-height: 1.33;
                letter-spacing: 0.25px;
                text-align: left;
                color: #ffffff;
                margin-bottom: 8px;
            }
            .creator{
                display: flex;
                flex-wrap: nowrap;
                &-avatar{
                    width: 16px;
                    height: 16px;
                    margin-right: 10px;
                    border: solid 1px #979797;
                    border-radius: 50%;
                }
                &-name{
                    width: 128px;
                    height: 13px;
                    font-size: 11px;
                    font-weight: 300;
                    font-stretch: normal;
                    font-style: normal;
                    line-height: normal;
                    letter-spacing: normal;
                    text-align: left;
                    color: #6e7683;
                }
                &-show-more-btn{
                    margin-left: 16px;
                }
            }
        }
        .controls{
            padding-left: 98px;
            display: flex;
            align-items: center;
            margin-bottom: 24px;
            &-button{
                width: 32px;
                height: 32px;
                display: flex;
                align-items: center;
                justify-content: center;
                color: white;
                cursor: pointer;
                &:hover{
                    opacity: .9;
                }
            }
            &-play{
                margin: 0 30px;
                width: 32px;
                height: 32px;
                cursor: pointer;
                &:hover{
                    opacity: .9;
                }
            }
        }
        .progress{
            display: flex;
            align-items: center;
            .time-info{
                width: 30px;
                font-size: 13px;
                font-weight: normal;
                font-stretch: normal;
                font-style: normal;
                line-height: normal;
                letter-spacing: 0.18px;
                color: #a2a2a2;
            }
            &-time{
                position: relative;
                flex: 1 1 auto;
                height: 2px;
                margin: 0 8px;
                background-color: #282828;
                &:hover{
                    cursor: pointer;
                }
                &-fill{
                    position: absolute;
                    bottom: 0;
                    height: 2px;
                    width: 50%;
                    background: #fca581;
                    pointer-events: none;
                }
                &-point{
                    position: absolute;
                    bottom: 0;
                    width: 14px;
                    height: 10px;
                    left: 50%;
                    background-color: #ffffff;
                    pointer-events: none;
                }
            }
        }
    }
</style>