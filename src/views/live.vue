<template>
    <div class="home" style="margin: 20px 0 ;">
        <el-row class="open-notice">
            <el-col :span="24" style="padding: 0 20px;"
                ><h4 style="text-align: left;    margin: 5px;">
                    <span style="color: #a68452;">即時開獎驗證：</span><br />
                    開獎現場直播，同步播報中央電視臺1套視頻，開獎過程100%公開公正！
                </h4></el-col
            >
        </el-row>

        <div class="mt20" style="text-align: left;">
            <el-button size="small" type="danger" round>{{
                currentInfo.LotteryName
            }}</el-button>
        </div>

        <div class="mt30">
            <video
                id="my-video"
                class="video-js vjs-default-skin"
                controls
                muted
                preload="none"
            >
                <source
                    src="https://bbb.jutianx.com/kkk/bbb.m3u8"
                    type="application/x-mpegURL"
                />
            </video>
        </div>

        <div class="mt30">
            <el-row>
                <el-col :span="8" style="text-align:left;line-height: 2;"
                    >開獎視頻</el-col
                >

                <el-col :span="16">
                    <el-input
                        size="small"
                        v-model="search"
                        placeholder="请输入期號"
                        style="width: 100px;margin-right:10px"
                    >
                    </el-input>
                    <el-button size="small" type="primary" @click="toSearch">
                        搜尋
                    </el-button>
                    <el-button size="small" type="warning" @click="reset">
                        重置
                    </el-button>
                </el-col>
            </el-row>

            <el-row class="mt20" style="text-align:left"
                ><el-col :span="24"
                    ><el-button
                        size="small"
                        round
                        :type="year == 0 ? 'danger' : ''"
                        @click="getHistoryOpenInfo(0)"
                        >今年</el-button
                    >

                    <el-button
                        size="small"
                        :type="year == 1 ? 'danger' : ''"
                        round
                        @click="getHistoryOpenInfo(1)"
                        >去年</el-button
                    ></el-col
                ></el-row
            >
            <el-row>
                <el-col
                    class="mt50 liveElPadding"
                    :span="12"
                    v-for="(e, index) in historyOpenInfo"
                    :key="index"
                >
                    <img
                        class="liveWidth"
                        src="@/assets/logo.png"
                        alt=""
                        @click="toPlay(e)"
                    />
                    <div class="mt20">
                        {{ currentInfo.LotteryName }} 第<span class="live_red">
                            {{ e.Issue }} </span
                        >期 開獎視頻
                    </div>
                </el-col>
            </el-row>

            <el-dialog
                :title="
                    currentInfo.LotteryName + ' 第' + toPlayData.Issue + '期'
                "
                center
                :visible.sync="dialogVisible"
                width="100%"
                class="home_left"
            >
                <el-row style="text-align: center;">
                    <el-col :span="24">
                        <span class="fz16"
                            >開獎時間：{{ toPlayData.OpenTime }}</span
                        >
                    </el-col>
                </el-row>
                <br />
                <el-row style="text-align: center;">
                    <el-col :span="24">
                        <span class="toPlay_kj fz16">開獎號碼：</span>
                        <div class="num_sm" style="margin-top: 10px;">
                            <span>
                                <span
                                    class="ball"
                                    v-for="(q,
                                    index) in toPlayData.OpenCode.split(',')"
                                    :key="index"
                                    :class="
                                        q
                                            .split(',')
                                            .map((e) => color_list[e % 6])
                                    "
                                >
                                    <span class="balls">{{ q }}</span>
                                    <span class="shengxiao">{{
                                        data_list[q % 12]
                                    }}</span>
                                </span>
                            </span>
                        </div>
                    </el-col>
                </el-row>

                <el-row>
                    <el-col :span="24" class="mt30">
                        <video
                            width="100%"
                            height="100%"
                            controls
                            :src="require('@/video/2021067.mp4')"
                        ></video>
                    </el-col>
                </el-row>
            </el-dialog>
        </div>
    </div>
</template>

<script>
import videojs from "video.js";
import "videojs-contrib-hls";
// Vue.prototype.$video = Video;

export default {
    name: "Live",
    components: {},
    watch: {
        "$store.state.kj_day": function(e) {
            // plan1.0
            // console.log("statttt", e);
            if (e) {
                console.log("监听到kj_day");
                this.getCurrentInfo();
            } else {
                this.msg = "监听不到";
            }
        },
    },
    data() {
        return {
            search: "",
            year: 0,
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
        initVideo() {
            videojs(
                "my-video",
                {
                    bigPlayButton: false,
                    textTrackDisplay: false,
                    posterImage: true,
                    errorDisplay: false,
                    controlBar: true,
                    hls: {
                        withCredentials: true,
                    },
                },
                function() {
                    this.play();
                }
            );
        },
        destroyVideo() {
            videojs(
                "my-video",
                {
                    bigPlayButton: false,
                    textTrackDisplay: false,
                    posterImage: true,
                    errorDisplay: false,
                    controlBar: true,
                    hls: {
                        withCredentials: true,
                    },
                },
                function() {
                    this.dispose();
                }
            );
        },
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
        getHistoryOpenInfo(e) {
            let today = new Date();
            // 今年
            if (e == 0) {
                this.year = 0;
                today = today.toISOString().substring(0, 10);
            }
            //去年
            if (e == 1) {
                this.year = 1;
                today = today.toISOString().substring(0, 4) - 1 + "-12-31";
            }
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/HistoryOpenInfoList`;
            let data = {
                lottery: this.currentInfo.LotteryId,
                issueNum: today,
                is_hk: this.$store.state.kj_day,
            };
            // console.log("getHistoryOpenInfo", data);
            this.axios.post(url, data).then((res) => {
                let { data } = res.data;
                this.historyOpenInfo = data;
                this.searchHistoryOpenInfo = data;
            });
        },
        timeElapsedHandler() {},
        initData() {
            // console.log("initData", this.issueOpenInfo);
            this.draw_num = this.issueOpenInfo.OpenCode.split(",");
            let petQQ = this.currentInfo.Pet;
            let index = this.list.indexOf(petQQ) - 1;
            let front = this.list.slice(index);
            let end = this.list.slice(0, index);
            this.data_list = front.concat(end);
        },
        toSearch() {
            // this.getHistoryOpenInfo();
            this.historyOpenInfo = this.searchHistoryOpenInfo.filter((e) => {
                return e.Issue.includes(this.search);
            });
            // console.log("search", this.historyOpenInfo.search(this.search));
        },
        reset() {
            this.historyOpenInfo = this.searchHistoryOpenInfo;
            this.search = "";
        },
        toPlay(e) {
            console.log("e", e);
            this.dialogVisible = true;
            this.toPlayData = e;
            // this.video = "@" + e.row.VideoUrl;

            // :src="require(`@${toPlayData.VideoUrl}`)"

            // :src="require('@/video/2021067.mp4')"
        },
    },
    mounted() {
        this.getCurrentInfo();
        this.initVideo();
    },
    beforeDestroy() {
        this.destroyVideo();
    },
};
</script>

<style scoped>
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

.el-table th {
    background-color: #eee;
    color: black;
}

.live_red {
    color: red;
}
.mt20 {
    margin-top: 20px;
}

.mt30 {
    margin-top: 30px;
}

.mt50 {
    margin-top: 50px;
}
.open-notice {
    background-color: #fff6e4;
    border-radius: 2px;
    margin: 0 auto;
    width: 95%;
    overflow: hidden;
    padding: 0.1rem 0.15rem;
}

.el-pagination {
    white-space: nowrap;
    padding: 3px 5px !important;
    color: #303133;
    margin: 10px 0px !important;
    font-weight: 700;
}

.liveWidth {
    width: 100%;
}

.liveElPadding {
    padding: 10px;
}
</style>

<style>
.video-js[tabindex="-1"] {
    outline: none;
    width: 100%;
}

.el-dialog__body {
    text-align: initial;
    padding: 25px 10px 10px !important;
}
</style>
