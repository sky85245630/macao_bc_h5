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
            <el-button size="small" type="danger" round>澳門六合彩</el-button>
        </div>

        <div class="mt30">
            <el-carousel height="423px">
                <el-carousel-item v-for="item in imgUrl" :key="item">
                    <img :src="item" alt="" style="width:100%" />
                </el-carousel-item>
            </el-carousel>
        </div>

        <div class="mt30">
            <el-row>
                <el-col :span="10" style="text-align:left;line-height: 2;"
                    >開獎視頻</el-col
                >

                <el-col :span="14"
                    ><el-input
                        size="small"
                        v-model="input"
                        placeholder="请输入期號"
                        style="width: 120px;margin-right:10px"
                    ></el-input
                    ><el-button size="small" type="primary"
                        >搜尋</el-button
                    ></el-col
                >
            </el-row>

            <el-row class="mt20" style="text-align:left"
                ><el-col :span="24"
                    ><el-button
                        size="small"
                        round
                        :type="year == 0 ? 'danger' : ''"
                        @click="year = 0"
                        >今年</el-button
                    >

                    <el-button
                        size="small"
                        :type="year == 1 ? 'danger' : ''"
                        round
                        @click="year = 1"
                        >去年</el-button
                    ></el-col
                ></el-row
            >
            <el-row>
                <el-col
                    class="mt50 liveElPadding"
                    :span="12"
                    v-for="e in 8"
                    :key="e"
                >
                    <img class="liveWidth" src="@/assets/logo.png" alt="" />
                    <div class="mt20">
                        澳門六合彩 第<span class="live_red"> 2021206 </span>期
                        開獎視頻
                    </div>
                </el-col>
            </el-row>
            <el-pagination
                class="mt50"
                background
                layout="prev, pager, next"
                :total="1000"
            >
            </el-pagination>
        </div>
    </div>
</template>

<script>
export default {
    name: "Post",
    components: {},
    data() {
        return {
            imgUrl: [
                "https://mcjccdn-qq.goluosi.com/macaujc/pc/img/swiper1.jpg",
            ],
            input: "",
            year: 0,
            tableData: [
                {
                    issue: "2021206",
                    openTime: "2021-07-25 21:34:05",
                    openCode: "43,41,33,42,05,13,21",
                    videoUrl: "",
                },
            ],
            color: {
                red: "01,02,07,08,12,13,18,19,23,24,29,30,34,35,40,45,46",
                green: "05,06,11,16,17,21,22,27,28,32,33,38,39,43,44,49",
                blue: "03,04,09,10,14,15,20,25,26,31,36,37,41,42,47,48",
            },
            animal: [
                { mouse: "02,14,26,38" },
                { ox: "01,13,25,37,49" },
                { tiger: "12,24,36,48" },
                { rabbit: "11,23,35,47" },
                { dragon: "10,22,34,46" },
                { snake: "09,21,33,45" },
                { house: "08,20,32,44" },
                { sheep: "07,19,31,43" },
                { monkey: "06,18,30,42" },
                { chicken: "05,17,29,41" },
                { dog: "04,16,28,40" },
                { pig: "03,15,27,39" },
            ],
            draw_num: "43,41,33,42,05,13,21",
        };
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
