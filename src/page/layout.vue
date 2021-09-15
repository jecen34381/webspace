<template>
    <el-container style="height: 100%">
        <el-header>壹o伍聊天室</el-header>
        <el-container>
            <el-aside width="200px">
                <div class="infinite-list-wrapper" style="overflow:auto">
                    <ul
                            class="list"
                            v-infinite-scroll="load"
                            infinite-scroll-disabled="disabled">
                        <li v-bind:="i in count" class="list-item">{{ i }}</li>
                    </ul>
                    <p v-if="loading">加载中...</p>
                    <p v-if="noMore">没有更多了</p>
                </div>
            </el-aside>
            <el-main>
                <el-input v-model="name" placeholder="请输入你的聊天昵称，如：“老子姓张嚣张的张”"></el-input>
                <el-container style="width: auto;height: 200px">
                    <el-aside width="600px">
                        <el-input
                                type="textarea"
                                :rows="2"
                                placeholder="请输入内容"
                                v-model="chatMessage" style="width: 100%;height: 100%">
                        </el-input>
                    </el-aside>
                    <el-main>
                        <el-row>
                            <el-button round @click="sendMessage">消息发送</el-button>
                        </el-row>
                        <el-row>
                            <el-button round @click="receiveMessage">消息接受开启</el-button>
                        </el-row>
                    </el-main>
                </el-container>
            </el-main>
        </el-container>
    </el-container>
</template>

<script>
    export default {
        name: "layout",
        methods: {
            handleOpen(key, keyPath) {
                console.log(key, keyPath);
            },
            handleClose(key, keyPath) {
                console.log(key, keyPath);
            },
            sendMessage(){
                this.$axios
                    .post('/chat/push/chat/message',
                        {
                            message:this.name + ':' + this.chatMessage
                        }
                    )
                    .catch(function (error) { // 请求失败处理
                        console.log(error);
                    });
            },
            receiveMessage(){
                setInterval(() => {
                    this.fun()
                }, 500)
            },
            fun(){
                setTimeout(() => {

                    this.$axios
                        .post('/chat/message/pop', {
                            start:0,
                            end:0
                        })
                        .then(response => {
                            this.info = response.data.messages;
                            if (this.info.length <= 0 || this.info == null ){
                                this.info = "暂无消息"
                            }else{
                                this.msg = "";
                                this.info.forEach(item => {
                                    this.msg += item + "\n"
                                })
                                this.info = this.msg
                            }


                        })
                        .catch(function (error) { // 请求失败处理
                            console.log(error);
                        });
                    //clearInterval();
                }, 0)
            },
            load () {
                this.loading = true
                setTimeout(() => {
                    this.count += 2
                    this.loading = false
                }, 2000)
            }
        },
        data() {
            return {
                name: '',
                textarea: '',
                info: null,
                chatMessage:'',
                count: 10,
                loading: false
            }
        },
        mounted() {
            // this.$axios
            //     .get('/task/backlog/list')
            //     .then(response => (this.info = response.data))
            //     .catch(function (error) { // 请求失败处理
            //         console.log(error);
            //     });

        },
        computed(){

        }
    }
</script>

<style scoped>
    .el-header, .el-footer {
        background-color: #B3C0D1;
        color: #333;
        text-align: center;
        line-height: 60px;
    }

    .el-aside {
        background-color: #D3DCE6;
        color: #333;
        text-align: center;
        line-height: 200px;
    }

    .el-main {
        background-color: #E9EEF3;
        color: #333;
        text-align: center;
        line-height: 160px;
        height: 100%;
    }

    body > .el-container {
        margin-bottom: 40px;
        height: 800px;
    }

    .el-container:nth-child(5) .el-aside,
    .el-container:nth-child(6) .el-aside {
        line-height: 260px;
    }

    .el-container:nth-child(7) .el-aside {
        line-height: 320px;
        height: 800px;
    }

</style>