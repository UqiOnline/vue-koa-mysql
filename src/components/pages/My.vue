<template>
    <div class="my">
        <!-- <Header></Header>-->
        <section class="my-head">
            <div class="my-head-top clearfix">
                <div class="fl user-tel">
                    <p class="upload">
                        <input type="file" @change="getImg($event)" accept="image/*" :disabled="false">
                        <img :src="src" v-if="src" />
                    </p>
                    <span>{{tel | formats}}</span>
                </div>
                <div class="fr">
                    <img :src="'../../static/images/account_'+eye_status+'_eye.png'" @click="toggle_status" class="headImg">
                </div>
            </div>
            <div class="head-account clearfix">
                <div class="fl">
                    <p class="benifit animated bounce" v-if="eye_status=='open'">{{account.yesterdayEarning}}</p>
                    <p class="benifit" v-else>***</p>
                    <p>昨日收益(元)</p>
                </div>
                <ul class="fr">
                    <li>总资产(元):<span v-if="eye_status=='open'">{{account.totalAssets || 0.00}}</span><span v-else>***</span> </li>
                    <li>可用余额(元):<span v-if="eye_status=='open'">{{account.useableAmt || 0.00}} </span><span v-else>***</span> </li>
                    <li>冻结金额(元):<span v-if="eye_status=='open'">{{account.useableAmt || 0.00}} </span><span v-else>***</span> </li>
                    <li>累计收益(元):
                        <span v-if="eye_status=='open'">{{account.totalEarning || 0.00}} </span>
                        <span v-else>***</span>
                        <img @click="showD" src="../../../static/images/redtishi.png" />
                    </li>
                </ul>
            </div>
        </section>
        <ul class="acc-list clearfix">
            <li>充值</li>
            <li>提现</li>
            <li></li>
        </ul>
        <p @click="logout">安全退出</p>
        <!--<p style="margin:1rem 0;" v-for="(item,$index) in arrNum" >
                <span>{{item[0]}}</span>--<input type="number" :name="'model'+$index" v-model="item[1]" @blur="dealNum(item)"/>
            </p>
        <span 
            v-for="(x,$index) in list" 
            style="display:block;margin:30px;" 
            :class="[{'active':active==$index?true:false}]"
            @click="active=$index"
            >{{x.name}}</span>-->
    </div>
</template>

<script>
    var _ ;
    export default {
        data() {
            return {
                arrNum: [
                    [0, 500],
                    [501, 1000],
                    [1001, 5000],
                    [5001, 10000],
                    [10001, 15000]
                ],
                list: [{
                        name: '11'
                    },
                    {
                        name: '22'
                    },
                    {
                        name: '33'
                    }
                ],
                active: 0,
                tel: this.cookie.get('phone'),
                src: '../../static/images/headImg.png',
                eye_status: 'close',
                account: {},
                tokenid: this.$localStore.get('tokenid'),
                firstName: 'Foo',
                lastName: 'Bar'
            }
        },
        methods: {
            toggle_status() {
                this.eye_status = this.eye_status == 'close' ? 'open' : 'close'
            },
            showD() {
                this.$dialog({
                    title: '提示',
                    text: '已加入你的累计红包变现总额0元',
                    btn: [{
                        text: '确定',
                    }],
                    class: 'btn-danger'
                })
            },
            logout() {                
                _.$ajax({
                    url: '/auth/logout',
                    key: {
                        token:_.cookie.get('token')
                    },
                    success: function(res) {
                        if(res.success){
                            // _.cookie.remove('token');
                            _.$router.push('/')
                        }
                        
                    }
                })
            },
            getImg(e) {
                var file = e.target.files[0];                   
                if (file.type.split('/')[0] != 'image') {
                    _.$toast('上传文件必须为图片类型');
                    return false;
                }
                if (file.size / 1024 > 5) {
                    _.$toast('上传图片不能大于5K');
                    return false;
                }
                var formData = new FormData();
                formData.append("name", this.name);
                formData.append("headImage", file);
                /*var xhr = new XMLHttpRequest();
                xhr.open("POST", this.$url('saveHeadImage'));
                xhr.send(formData)
                xhr.onprogress = (event) => {
                    console.log(1, event)
                    _.src = '../../static/images/loading.gif';
                }
                xhr.onreadystatechange = (res) => {
                    if (xhr.readyState == 4 && xhr.status == 200) {
                        var res = JSON.parse(res.target.responseText);
                        if (res.code == '000') {
                            var oReader = new FileReader();
                            oReader.readAsDataURL(file);
                            oReader.onload = function(e) {
                                var base64 = oReader.result;
                                _.src = base64;
                            }
                        } else {
                            _.$toast(data.msg)
                        }
                    }
                }*/
            },
            dealNum(value) { //处理区间
                var arr = this.arrNum;                   
                for (var i = 1; i < arr.length; i++) {
                    if (parseFloat(value[1]) > (arr[i][0] - 1) && parseFloat(value[1]) <= (arr[i][1])) {
                        var del = arr.splice(1, i);
                        arr[0][0] = 0;
                        arr[0][1] = parseFloat(del[del.length - 1][1]);
                        break;
                    }
                }
            }
        },
        computed: {
            fullName: {
                get: function() {
                    return this.firstName + ' ' + this.lastName
                },
                set: function(newValue) {
                    var names = newValue.split(' ')
                    this.firstName = names[0]
                    this.lastName = names[names.length - 1]
                }
            }
        },
        created() {
            _ = this;
            _.fullName = "John Smith" //测试computed
            _.$ajax({
                url: '../../static/js/json/account.json',
                type: 'get',
                success: function(res) {
                    _.account = res.data
                }
            })
            _.$ajax({
                url:'/api/todolist/3',
                type:'get',
                success:function(data){
                    console.log(data)
                }
            })   
        },
    
    }
</script>

<style lang="less">
    @import '../../../static/css/my.less';
    .dialog-btn-container .btn-danger {
        color: #ff4c4c;
    }
    
    .active {
        color: #ff4c4c;
    }
</style>
