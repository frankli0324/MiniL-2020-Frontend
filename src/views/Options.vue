<template>
    <div class="container">
        <HeadBar></HeadBar>
        <div class="scoreboard-container">
            <!-- 时钟 -->
            <div class="time">Current Time: {{ timeString }}</div>
            <div class="time" style="font-size: 50px">Ranks</div>
            <div class="time"></div>
            <!-- 排行榜 -->
            <div class="rank-container tip" v-if="scoreboard != ''">
                <div
                    v-for="(value, key) in scoreboard"
                    :key="key"
                    :class="['rank-item', key === 0 ? 'one' : '', key === 1 ? 'two' : '', key === 2 ? 'three' : '']"
                >
                    <div class="rank-num">#{{ key + 1 }}</div>
                    <router-link :to="'/team/' + value.id" class="link">{{ value.name }}</router-link>
                    <div class="score-num">{{ value.score }}</div>
                </div>

                <!-- 加载图标 -->
                <div v-if="scoreboard === ''" class="loading">
                    <font-awesome-icon icon="spinner" spin />
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import HeadBar from '../components/HeadBar.vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faSpinner } from '@fortawesome/free-solid-svg-icons';
import ctfd from '../ctfd';

library.add(faSpinner);

export default {
    components: {
        HeadBar
    },
    data() {
        return {
            //时钟字符串
            timeString: '00:00:00',
            //计时器id
            timer_count: '',
            //计时器id
            timer_scoreboard: '',
            //分值榜
            scoreboard: '',
            //排行榜
            rankboard: ''
        };
    },
    methods: {
        countTime() {
            let time = new Date();
            let hour = time.getHours();
            hour = hour < 10 ? '0' + hour : hour;

            let min = time.getMinutes();
            min = min < 10 ? '0' + min : min;

            let sec = time.getSeconds();
            sec = sec < 10 ? '0' + sec : sec;

            this.timeString = hour + ':' + min + ':' + sec;
        },
        showChange(name) {
            this.show = name;
        },
        getScoreboard() {
            ctfd.get('/scoreboard/top/100')
                .then((resp) => resp.json())
                .then((resp) => {
                    console.log(resp);
                    this.scoreboard = [];
                    for (var i in resp.data) {
                        var user = resp.data[i];
                        var score = 0;
                        for (var s in user.solves) score += user.solves[s].value;
                        this.scoreboard.push({
                            id: user.id,
                            name: user.name,
                            score: score
                        });
                    }
                })
                .catch((error) => console.log(error));
        }
    },
    created() {
        this.countTime();
        this.getScoreboard();
        this.timer_count = setInterval(this.countTime, 200);
        this.timer_scoreboard = setInterval(this.getScoreboard, 5000);
    },
    beforeDestroy() {
        clearInterval(this.timer_count);
        clearInterval(this.timer_scoreboard);
    }
};
</script>

<style scoped>
.scoreboard-container {
    height: calc(100% - 50px);
    max-height: 95%;
    width: 100%;
    background: transparent;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    border-top: 1px solid rgba(255, 255, 255, 0.05);
}
.time {
    height: 30px;
    line-height: 30px;
    width: 600px;
    margin-top: 25px;
    font-size: 20px;
    font-weight: bold;
}
.tag-container {
    height: 50px;
    width: 600px;
    display: flex;
    justify-content: center;
    align-items: center;
}
.tag-container div {
    height: 50px;
    margin: 0 20px;
    line-height: 50px;
    text-align: center;
    box-sizing: border-box;
    cursor: pointer;
}
.tag-container div:hover {
    border-bottom: 2px solid #0078d6;
}
.tag-container .active {
    border-bottom: 2px solid #0078d6;
}
.rank-container {
    height: 430px;
    width: 800px;
    margin: 20px 0;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    overflow: auto;
}
.rank-item {
    min-height: 40px;
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 16px;
    font-weight: bold;
}
.rank-num,
.score-num {
    height: 100%;
    width: 100px;
    line-height: 40px;
    text-align: center;
}
.score-num {
    width: 200px;
}
.link,
.team-name {
    height: 100%;
    line-height: 40px;
    width: 500px;
    overflow-y: auto;
}
.team-name::-webkit-scrollbar {
    height: 0;
    width: 0;
}
.one {
    font-size: 28px !important;
}
.two {
    font-size: 24px !important;
}
.three {
    font-size: 20px !important;
}
.link,
.link:link,
.link:visited,
.link:hover,
.link:active {
    text-decoration: none;
    color: white;
}

.loading {
    height: 100%;
    width: 100%;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: -20px;
}
</style>