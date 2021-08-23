<template>
    <div class="home" style="margin: 20px 0 ;">
        <h1 style="text-align: left;">開獎公告</h1>
        <div style="text-align: left;">
            <el-button
                size="small"
                round
                :type="type == 0 ? 'danger' : ''"
                @click="type = 0"
                >{{ currentInfo.LotteryName }}</el-button
            >

            <el-button
                size="small"
                :type="type == 1 ? 'danger' : ''"
                round
                @click="type = 1"
                >接口調用</el-button
            >
        </div>

        <el-row class="mt20">
            <el-col :span="8" style="text-align:left;line-height: 2;"
                >開獎視頻</el-col
            >

            <el-col :span="16"
                ><el-input
                    size="small"
                    v-model="search"
                    placeholder="请输入期號"
                    style="width: 100px;margin-right:10px"
                ></el-input
                ><el-button size="small" type="primary" @click="toSearch"
                    >搜尋</el-button
                >

                <el-button size="small" type="warning" @click="reset"
                    >重置</el-button
                >
            </el-col>
        </el-row>

        <div v-show="type == 0">
            <div class="post_box">
                <div>
                    <el-row class="mt20">
                        <el-col :span="24">
                            <span style="font-weight:bold"
                                >{{ currentInfo.LotteryName }} 第<span
                                    style="font-weight:200;color:red"
                                >
                                    {{ currentInfo.LastIssue }} </span
                                >期</span
                            >

                            <div
                                class="num "
                                style="margin-top:0px;padding: 30px 0;border-bottom: 1px solid #eee;"
                            >
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

                    <el-row class="mt20">
                        <div class="box_title" style="display: contents;">
                            <span>{{ currentInfo.Issue }} 下期截止時間：</span
                            ><br /><span style="color:red">{{
                                currentInfo.EndTime
                            }}</span>
                        </div>

                        <el-col :span="24" class="mt20">
                            <!-- <FlipClock></FlipClock> -->
                            <flip-countdown
                                :deadline="flip_endTime"
                                :showDays="true"
                                @timeElapsed="timeElapsedHandler"
                            ></flip-countdown>
                        </el-col>
                    </el-row>
                </div>
            </div>

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

            <el-table :data="historyOpenInfo" class="mt20" style="width: 100%">
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
                                    v-for="(q, index) in e.row.OpenCode.split(
                                        ','
                                    )"
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
                    </template>
                </el-table-column>
            </el-table>
        </div>

        <div v-show="type == 1" style="text-align:left;">
            <br />
            本網站提供查詢接口，供查詢開獎數據。 <br />
            <br />
            地址:http://api.bjjfnet.com/data/opencode/:id<br />
            <br />
            方式: GET <br />
            <br />返回: JSON <br />
            <br />
            <br />調用示例 澳門六合彩:<br />
            <br />
            http://api.bjjfnet.com/data/opencode/2032 返回示例
            查詢澳門六合彩開獎數據 <br />
            <br />
            <br />{ "code": 0, "message": "Success", "data": [ { "issue":
            "2020070", "openCode": "19,16,06,49,21,07,11", "openTime":
            "2020-03-10 21:34:30" }, ... ] }<br /><br />
            <br />
            返回字段說明 <br />
            <br />
            1、code: 消息編碼, 0 代表成功； <br />
            <br />
            2、message: 消息描述； <br />
            <br />
            3、data：具體開獎數據；issue：期號；openCode：開獎號碼；openTime：開獎時間。
            <br />
            <br />
        </div>
    </div>
</template>

<script>
import FlipCountdown from "vue2-flip-countdown";

export default {
    name: "Post",
    components: { FlipCountdown },
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
            // 澳門六合彩與接口調用
            type: 0,
            //今年去年
            year: 0,
            //搜尋按鈕
            search: "",
            pagesize: 10, //设置每页显示条目个数为10
            currentPage: 1, //设置当前页默认为1
            filterAutomobileInfs: [], //分页前的数据
            dialogVisible: false,
            // tableData: [
            //     {
            //         issue: "2021206",
            //         openTime: "2021-07-25 21:34:05",
            //         openCode: "43,41,33,42,05,13,21",
            //         videoUrl: "",
            //     },
            // ],
            flip_endTime: "2021-8-14 17:25:00",
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
            searchHistoryOpenInfo: [],
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
            this.toPlayData = e.row;
            // this.video = "@" + e.row.VideoUrl;

            // :src="require(`@${toPlayData.VideoUrl}`)"

            // :src="require('@/video/2021067.mp4')"
        },
    },
    mounted() {
        // this.initData();
        this.getCurrentInfo();
        // this.getIssueOpenInfo();
    },
};
</script>
<style>
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

.el-pagination.is-background .btn-next,
.el-pagination.is-background .btn-prev,
.el-pagination.is-background .el-pager li {
    margin: 0 2px !important;
    background-color: #f4f4f5;
    color: #606266;
    min-width: 30px;
    border-radius: 2px;
}

.el-pagination {
    white-space: nowrap;
    padding: 3px 5px !important;
    color: #303133;
    margin: 10px 0px !important;
    font-weight: 700;
}

.mt20 {
    margin-top: 20px;
}

.post_box {
    margin: 10px 0;
    padding: 20px 0;
    border-top: 1px solid #ddd;
    border-bottom: 1px solid #ddd;
}
</style>
