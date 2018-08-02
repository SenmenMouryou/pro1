<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-plus"></i> 登录</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" class="demo-ruleForm">
                <el-form-item prop="username">
                    <el-input v-model="ruleForm.username" placeholder="email"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input type="password" placeholder="密码" v-model="ruleForm.password" @keyup.enter.native="submitForm('ruleForm')"></el-input>
                </el-form-item>
                <el-form-item prop="authcode">
                    <div>
                        <el-col :span="11">
                            <el-input placeholder="验证码" v-model="ruleForm.authcode" @keyup.enter.native="submitForm('ruleForm')"></el-input>
                        </el-col>
                        <el-col :span="2"><img src=""/></el-col>
                        <el-col :span="11">
                            <img :src.sync="authSrc" @click="updateAuthCode()">
                        </el-col>

                    </div>
                </el-form-item>
                <div class="login-btn">
                </div>

                <el-form-item>
                    <div class="login-btn">
                        <el-col :span="11">
                            <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
                        </el-col>
                        <el-col :span="2"> <img src=""/></el-col>
                        <el-col :span="11">
                            <el-button type="primary" @click="putDialogFormVisible=true">注册</el-button>
                        </el-col>
                    </div>
                </el-form-item>
            </el-form>
            <el-dialog id="putDialog" title="注册" :visible.sync="putDialogFormVisible">
                <SignUp></SignUp>
            </el-dialog>


        </div>
    </div>
</template>

<script>
    import SignUp from "./SignUp";
    export default {
        components: {SignUp},
        data: function(){
            return {
                ruleForm: {
                    username: '',
                    password: '',
                    authcode:''
                },
                url:'',
                putDialogFormVisible:false,
                authSrc:'http://localhost:8888/user/authCode?a=11',
                rules: {
                    username: [
                        { required: true, message: '请输入邮箱', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'blur' }
                    ],
                    authcode: [
                        { required: true, message: '请输入验证码', trigger: 'blur' }
                    ]
                }
            }
        },
        methods: {
            submitForm(formName) {
                const self = this;
                self.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.updateDevice(formName);

                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            updateDevice(form) {
                this.$http.post(this.url, form).then((form) => form.json().then((data) => {
                    if (data.code == 200) {

                        localStorage.setItem('ms_username',self.ruleForm.username);
                        localStorage.setItem('ms_isLogin','true');

                        this.GLOBAL.data=data;

                        console.log("Login.vue:"+localStorage.getItem('ms_username')
                            +"\n"+localStorage.getItem('ms_isLogin'));

                        self.$router.push('/readme');
                        location.reload();
                        this.$message.success('登录成功');
                    }else {
                        this.$message.error(data.data);
                    }
                }), err => this.$message.error('添加出错'));
            },
            updateAuthCode(){
                this.authSrc='http://localhost:8888/user/authCode?a='+Math.random()*1000000;
            }
        }
    }
</script>

<style scoped>
    .login-wrap{
        position: relative;
        width:100%;
        height:100%;
        background-color: #fff;
    }
    .ms-title{
        position: absolute;
        top:50%;
        width:100%;
        margin-top: -230px;
        text-align: center;
        font-size:30px;
    }
    .ms-login{
        position: absolute;
        left:50%;
        top:50%;
        width:300px;
        height:200px;
        margin:-150px 0 0 -190px;
        padding:40px;
        border-radius: 5px;
        background: #c0ccda;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:100%;
        height:36px;
    }
</style>
