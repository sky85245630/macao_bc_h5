<template>
    <div id="app">
        <el-col :span="24">
            <div id="nav">
                <el-row>
                    <el-col :span="12">
                        <div style="float: left;">
                            <img
                                alt="Vue logo"
                                src="@/assets/logo.png"
                                style="width: 100px;"
                            />
                        </div>
                    </el-col>
                    <el-col :span="12">
                        <div class="date">
                            {{ BJSeviceTime }}<br />
                            <el-button
                                v-if="kj_day == 'yes'"
                                @click="set_kj_day('no')"
                                type="primary"
                                >澳門</el-button
                            >
                            <el-button
                                v-if="kj_day == 'no'"
                                @click="set_kj_day('yes')"
                                type="primary"
                                >香港</el-button
                            >
                        </div>
                    </el-col>
                </el-row>
            </div>

            <router-view />
            <footer class="app_footer">
                <el-menu
                    :default-active="activeIndex"
                    class="el-menu-demo fixed_menu"
                    mode="horizontal"
                    @select="handleSelect"
                >
                    <el-row>
                        <el-col style="width:20%">
                            <el-menu-item
                                index="1"
                                @click="$router.push('/home')"
                            >
                                <i class="el-icon-basketball"></i>
                                <p class="footer_p">首頁</p>
                            </el-menu-item>
                        </el-col>
                        <el-col style="width:20%">
                            <el-menu-item
                                index="2"
                                @click="$router.push('/post')"
                            >
                                <i class="el-icon-bell"></i>
                                <p class="footer_p">開獎公告</p>
                            </el-menu-item>
                        </el-col>
                        <el-col style="width:20%">
                            <el-menu-item
                                index="3"
                                @click="$router.push('/live')"
                            >
                                <i class="el-icon-video-camera"></i>
                                <p class="footer_p">
                                    開獎視頻
                                </p>
                            </el-menu-item>
                        </el-col>
                        <el-col style="width:20%">
                            <el-menu-item
                                index="4"
                                @click="$router.push('/faq')"
                            >
                                <i class="el-icon-question"></i>
                                <p class="footer_p">
                                    FAQ
                                </p>
                            </el-menu-item>
                        </el-col>
                        <el-col style="width:20%">
                            <el-menu-item
                                index="5"
                                @click="$router.push('/about')"
                            >
                                <i class="el-icon-user"></i>
                                <p class="footer_p">
                                    關於我們
                                </p>
                            </el-menu-item>
                        </el-col>
                    </el-row>
                </el-menu>
            </footer>
        </el-col>
    </div>
</template>

<script>
export default {
    data() {
        return {
            activeIndex: "1",
            kj_day: "",
            BJSeviceTime: "",
        };
    },
    methods: {
        handleSelect(key, keyPath) {
            console.log(key, keyPath);
        },
        is_kj_day() {
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/is_kj_day`;
            this.axios.post(url).then((res) => {
                this.kj_day = res.data.is_kj_day;
                this.kj_day = this.kj_day == false ? "yes" : "no";
                // console.log("is_kj_day", this.kj_day);
                this.$store.commit("set_kj_day", this.kj_day);
            });
        },
        set_kj_day(e) {
            // console.log("set_kj_day", e);
            this.kj_day = e;
            this.$store.commit("set_kj_day", this.kj_day);
        },
        service_time() {
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/ServerTime`;

            window.setInterval(() => {
                this.axios.post(url).then((res) => {
                    // console.log("service_time", res.data.data.BJSeviceTime);
                    this.BJSeviceTime = res.data.data.BJSeviceTime;
                    this.BJSeviceTime = this.BJSeviceTime.substring(
                        0,
                        this.BJSeviceTime.length - 3
                    );
                });
            }, 30000);
        },
        current_time() {
            let url = `${process.env.VUE_APP_BASE_DOMAIN}/api/ServerTime`;
            this.axios.post(url).then((res) => {
                // console.log("service_time", res.data.data.BJSeviceTime);
                this.BJSeviceTime = res.data.data.BJSeviceTime;
                this.BJSeviceTime = this.BJSeviceTime.substring(
                    0,
                    this.BJSeviceTime.length - 3
                );
            });
        },
    },
    created() {
        this.is_kj_day();
        this.service_time();
        this.current_time();
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #000;
    width: 100%;
    /* min-width: 1170px; */
    height: 140px;
}

.date {
    float: right;
    line-height: 24px;
    font-size: 18px;
    color: #a4a4a4;
}

#nav {
    padding: 10px;
    border-bottom: 1px solid #dadada;
}

#nav a {
    font-weight: bold;
    color: #2c3e50;
}

#nav a.router-link-exact-active {
    color: #42b983;
}

footer {
    width: 100%;
    padding: 20px 0;
}

/*
.footer_list {
}

.footer_li {
    list-style: none;
}
.footer-list-item:not(:last-child) {
    border-right: 1px solid #fff;
}

.footer-list-item.is-active {
    color: #fff;
    background-color: #d80011;
} */

.el-menu {
    border-right: solid 1px #e6e6e6;
    list-style: none;
    position: relative;
    margin: 0;
    color: white;
    padding-left: 0;
    background-color: #0068b7 !important;
    z-index: 999;
    width: 100%;
}

.footer_p {
    margin: 0px;
    line-height: normal;
}

.el-menu-item.is-active {
    color: white !important;
    background-color: red !important;
}

.el-menu-item {
    color: white !important;
    text-align: center;
    font-size: 18px;
    padding: 10px 5px !important;
    vertical-align: middle;
}

.el-menu-item p {
    color: #fff;
}

.el-menu-item i {
    color: white !important;
}

.el-menu-item [class^="el-icon-"] {
    margin-right: 0px;
    display: block;
    width: auto !important;
    margin: 0px !important;
}

.el-menu-item:hover {
    color: white !important;
    background-color: red !important;
}

.fixed_menu {
    margin-bottom: 20px;
    position: fixed !important;
    bottom: 0;
    left: 0;
}

.el-pagination {
    white-space: nowrap;
    padding: 3px 5px !important;
    color: #303133;
    margin: 10px 0px !important;
    font-weight: 700;
}
</style>
