<template>
    <div id="app">
        <el-col :span="24">
            <div id="nav">
                <el-row>
                    <el-col :span="6">
                        <div style="float: left;">
                            <img
                                alt="Vue logo"
                                src="@/assets/logo.png"
                                style="width: 100px;"
                            />
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
        };
    },
    methods: {
        handleSelect(key, keyPath) {
            console.log(key, keyPath);
        },
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
    line-height: 110px;
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
</style>
