<template>
    <div class="home" style="margin: 20px 0 ;">
        <!--<HelloWorld msg="Welcome to Your Vue.js App" />-->

        <div class="block">
            <el-carousel width="100%">
                <el-carousel-item v-for="item in imgUrl" :key="item">
                    <img :src="item" alt="" style="width:100%" />
                </el-carousel-item>
            </el-carousel>
        </div>

        <div class="home_box" style="margin-bottom:30px">
            <div class="box_title amlhc">
                <span class="title">{{ currentInfo.LotteryName }}</span>
                <span class="nextTime"
                    >第{{ currentInfo.Issue }}期截止時間：{{
                        currentInfo.EndTime
                    }}</span
                >
            </div>
            <div style="padding:50px 0">
                <el-row class="mt-5">
                    <el-col :span="24">
                        <!-- <FlipClock></FlipClock> -->
                        <flip-countdown
                            :deadline="flip_endTime"
                            :showDays="true"
                            @timeElapsed="timeElapsedHandler"
                        ></flip-countdown>
                        <div style="margin-top:30px">
                            <el-button type="warning">開獎驗證</el-button>
                            <el-button type="danger">直播</el-button>
                        </div>
                        <br />
                        <span
                            style="font-size:18px;cursor:pointer"
                            @click="$router.push('/post')"
                            >開獎歷史查詢></span
                        >
                    </el-col>
                </el-row>

                <el-row class="mt-5">
                    <el-col :span="24">
                        <span style="font-weight:bold"
                            >{{ currentInfo.LotteryName }} 第<span
                                style="font-weight:200;color:red"
                            >
                                {{ currentInfo.LastIssue }} </span
                            >期</span
                        >

                        <div class="num">
                            <span>
                                <span
                                    class="ball"
                                    v-for="(e, index) in draw_num"
                                    :key="index"
                                    :class="
                                        e
                                            .split(',')
                                            .map((e) => color_list[e % 6])
                                    "
                                >
                                    <span class="balls">{{ e }}</span>
                                    <span class="shengxiao">{{
                                        data_list[e % 12]
                                    }}</span>
                                </span>
                            </span>
                        </div>
                    </el-col>
                </el-row>
            </div>
        </div>

        <el-table :data="historyOpenInfo" style="width: 100%">
            <el-table-column prop="issue" label="期號" width="100">
                <template slot-scope="e">
                    第<span style="color:red">{{ e.row.Issue }}</span
                    >期
                </template>
            </el-table-column>

            <el-table-column label="中獎號碼">
                <template slot-scope="e">
                    <div class="num_sm">
                        <span>
                            <span
                                class="ball"
                                v-for="(q, index) in e.row.OpenCode.split(',')"
                                :key="index"
                                :class="
                                    q.split(',').map((e) => color_list[e % 6])
                                "
                            >
                                <span class="balls">{{ q }}</span>
                                <span class="shengxiao">{{
                                    data_list[q % 12]
                                }}</span>
                            </span>
                        </span>
                    </div>
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>

<script>
import FlipCountdown from "vue2-flip-countdown";

export default {
    name: "Home",
    components: {
        FlipCountdown,
    },
    watch: {
        "$store.state.kj_day": function(e) {
            // plan1.0
            // console.log("statttt", e);
            if (e) {
                // console.log("监听到kj_day");
                this.getCurrentInfo();
            } else {
                this.msg = "监听不到";
            }
        },
    },
    data() {
        return {
            video: "@/video/2021063.mp4",
            flip_endTime: "2021-8-14 17:25:00",
            imgUrl: [
                "https://mcjccdn-qq.goluosi.com/macaujc/pc/img/swiper1.jpg",
                "https://mcjccdn-qq.goluosi.com/macaujc/pc/img/swiper3.jpg",
                "https://mcjccdn-qq.goluosi.com/macaujc/pc/img/swiper4.jpg",
            ],
            list: [
                "鼠",
                "牛",
                "虎",
                "兔",
                "龙",
                "蛇",
                "马",
                "羊",
                "猴",
                "鸡",
                "狗",
                "猪",
            ],
            data_list: [],
            color_list: ["red", "red", "green", "green", "blue", "blue"],
            draw_num: "",
            currentInfo: {},
            issueOpenInfo: {},
            historyOpenInfo: [],
            dialogVisible: false,
            toPlayData: {
                Issue: "2021067",
                LotteryId: 2032,
                OpenCode: "44,13,26,34,35,14,12",
                OpenTime: "2021-08-07 21:30:00",
                Pet: "牛",
                VideoUrl: "/video/2021067.mp4",
            },
        };
    },
    methods: {
        getCurrentInfo() {
            // console.log("env", process.env.VUE_APP_BASE_DOMAIN);
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/CurrentInfo`;
            // var url = "http://localhost:81/api/CurrentInfo";
            // var url = "http://localhost:81/api/is_kj_day";
            //一開始沒有抓到state資料會預設no
            let data = { is_hk: this.$store.state.kj_day };
            // if(!this.$store.state.kj_day){
            // console.log(typeof this.$store.state.kj_day);
            // }
            // console.log("store", this.$store.state.kj_day);
            this.axios.post(url, data).then((res) => {
                let { data } = res.data;
                this.currentInfo = data;
            });
            // console.log("this.$store.state.kj_day", this.$store.state.kj_day);
            this.getIssueOpenInfo();
            this.getHistoryOpenInfo();
        },
        getIssueOpenInfo() {
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/IssueOpenInfo`;
            let data = {
                lottery: this.currentInfo.LotteryId,
                is_hk: this.$store.state.kj_day,
            };
            // console.log("getIssueOpenInfo", data);
            this.axios.post(url, data).then((res) => {
                let { data } = res.data;
                this.issueOpenInfo = data;
                this.initData();
            });
        },
        getHistoryOpenInfo() {
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/HistoryOpenInfo`;
            let data = {
                lottery: this.currentInfo.LotteryId,
                issueNum: 6,
                is_hk: this.$store.state.kj_day,
            };
            // console.log("getHistoryOpenInfo", data);
            this.axios.post(url, data).then((res) => {
                let { data } = res.data;
                this.historyOpenInfo = data;
            });
        },

        timeElapsedHandler() {},
        initData() {
            this.draw_num = this.issueOpenInfo.OpenCode.split(",");
            let petQQ = this.currentInfo.Pet;
            let index = this.list.indexOf(petQQ) - 1;
            let front = this.list.slice(index);
            let end = this.list.slice(0, index);
            this.data_list = front.concat(end);
        },
        toPlay(e) {
            console.log("e", e);
            this.dialogVisible = true;
            this.toPlayData = e.row;
            // this.video = "@" + e.row.VideoUrl;

            // :src="require(`@${toPlayData.VideoUrl}`)"

            // :src="require('@/video/2021067.mp4')"
        },
    },
    created() {},
    mounted() {
        // this.initData();
        this.getCurrentInfo();
        // this.getIssueOpenInfo();
    },
};
</script>

<style>
.flip-clock__slot {
    display: none !important;
}

.flip-card__top,
.flip-card__bottom,
.flip-card__back-bottom,
.flip-card__back::before,
.flip-card__back::after {
    color: #ffffff !important;
}

.flip-card__top-4digits,
.flip-card__bottom-4digits,
.flip-card__back-bottom-4digits,
.flip-card__back-4digits::before,
.flip-card__back-4digits::after {
    color: #ffffff !important;
}

.flip-card__bottom,
.flip-card__back-bottom,
.flip-card__bottom-4digits,
.flip-card__back-bottom-4digits {
    color: #ffffff !important;
}

.home_box {
    margin-top: 20px;
    box-shadow: 0 1px 4px #c8c8c8;
    border: 1px solid #dadada;
    border-radius: 4px;
    background-color: #fff;
}

.box_title .nextTime {
    font-size: 14px;
    float: right;
}

.el-carousel__container {
    position: relative;
    height: 130px !important;
}

.box_title .title {
    font-size: 20px;
    float: left;
}
.box_title {
    height: 40px;
    line-height: 40px;
    padding: 0 20px;
}
.amlhc {
    background-color: #ffc9c9;
    border: 1px solid #d80011;
    color: #d80011;
}

.num {
    height: 60px;
    margin-top: 40px;
}

.num > span {
    height: 60px;
    width: 100%;
    display: inline-block;
}

.num_sm > span {
    height: 40px;
    width: 100%;
    display: inline-block;
}

.num .balls {
    display: inline-block;
    height: 40px;
    width: 40px;
    text-align: center;
    line-height: 40px;
    background-position: -6px -5px;
    background-size: 50px;
    background-repeat: no-repeat;
    font-size: 16px;
    font-weight: 600;
}

.num_sm .balls {
    display: inline-block;
    height: 35px;
    width: 35px;
    text-align: center;
    line-height: 35px;
    background-size: 30px;
    background-repeat: no-repeat;
    font-size: 12px;
    font-weight: 600;
    color: #000;
}

.num .ball {
    margin-right: 0px;
    position: relative;
    text-align: center;
    display: inline-block;
    height: 40px;
    width: 40px;
}

.num_sm .ball {
    display: inline-block;
    width: 35px;
    height: 35px;
    position: relative;
}

.num .ball .shengxiao {
    color: #515151;
    font-size: 15px;
    position: absolute;
    top: 40px;
    left: 13px;
}

.num_sm .ball .shengxiao {
    background-color: transparent;
    color: #414141;
    position: relative;
    left: 9px;
    top: 0px;
    font-size: 13px;
}
.num .blue {
    background-image: url("../assets/blue.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.num .red {
    background-image: url("../assets/red.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.num .green {
    background-image: url("../assets/green.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.num_sm .blue {
    background-image: url("../assets/blue_sm.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.num_sm .red {
    background-image: url("../assets/red_sm.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.num_sm .green {
    background-image: url("../assets/green_sm.png");
    background-size: contain;
    background-repeat: no-repeat;
}

.el-table th {
    background-color: #eee;
    color: black;
}

.el-table .cell {
    padding-right: 0px !important;
}
</style>
