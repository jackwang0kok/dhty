<template>
  <div>
    <!-- 完善个人信息 -->
    <div class="completeInfo" v-title data-title="基本信息">
      <div class="maxHeightBox">
        <div class="touxiang" @click="wxUploadImg">
        <!-- <div class="touxiang"> -->
          <!-- <span class="text">头&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;像</span> -->
          <span class="text">头像</span>
          <span class="el-icon-arrow-right"></span>
          <div class="avatar" v-if="imageUrl">
            <img :src="imageUrl" style="width: 100%; height: 100%; border-radius: 50%;">
          </div>
          <!-- <el-upload
            class="avatar-uploader"
            action="none"
            :multiple='false'
            accept="image/png,image/jpg,image/jpeg"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
            ref="upload"
            :http-request="uploadSectionFile">
            
            <i v-else class="el-icon-camera-solid avatar-uploader-icon"></i>
          </el-upload> -->
        </div>
        <ul class="ul1">
          <li @click="showInput">
            <!-- <span class="titleText">昵&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;称</span> -->
            <span class="titleText">昵称</span>
            <span class="right">{{inputNickNameValue}}<i class="el-icon-arrow-right"></i></span>
            <!-- <el-input v-model="inputNickName" @blur="blurInputName" v-show="isShowInputName"></el-input> -->
          </li>
          <li @click="showSexPicker">
            性别
            <!-- 性&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;别 -->
            <span class="right">{{sexValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="showHeightPicker">
            身高
            <!-- 身&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;高 -->
            <span class="right">{{heightValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="showBirthdayPicker">
            生日
            <!-- 生&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;日 -->
            <span class="right">{{birthdayValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="birRange">
            年龄段
            <!-- 年&nbsp;龄&nbsp;段 -->
            <span class="right">{{birthRangeValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="showLevl">
            级别
            <!-- 级&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;别 -->

            <span class="right">{{levelName}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="showprofessionPicker">
            职业
            <!-- 职&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;业 -->
            <span class="right">{{professionValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
          <li @click="showLocation">
            所在地区
            <span class="right">{{addressValue}}<i class="el-icon-arrow-right"></i></span>
          </li>
        </ul>
        <div class="label">
          <p>标签</p>
          <div class="itemBox">
            <div class="addBtn" @click="selectLabel"><span class="el-icon-plus"></span></div>
            <div class="itemLabel" v-for="(item,index) in selectedList" :key="index">
              {{item.name}}
            </div>
          </div>
        </div>
      </div>
      <div class="heightBox2">
        <div class="submit" @click="submit">提交</div>
      </div>
      
      <!-- 选择器 -->
      <mt-popup
        v-model="popupVisible"
        popup-transition="popup-fade"
        closeOnClickModal="true" 
        position="bottom"
        >
        <!-- 自定义选择 -->
        <mt-picker 
          v-show="commonPicker"
          :slots="currSlots" 
          :visibleItemCount='3'
          :itemHeight='50'
          showToolbar
          @change="currChange"
          >
          <div class="picker-toolbar-title">
            <div class="usi-btn-cancel" @click="popupVisible = !popupVisible">取消</div>
            <div class="usi-btn-sure" @click="currSure">确定</div>
          </div>
        </mt-picker>
        <!-- 等级选择 -->
        <mt-picker 
          v-show="levelPicker"
          :slots="currSlots" 
          value-key="name"
          :visibleItemCount='3'
          :itemHeight='50'
          showToolbar
          @change="currChange"
          >
          <div class="picker-toolbar-title">
            <div class="usi-btn-cancel" @click="popupVisible = !popupVisible">取消</div>
            <div class="usi-btn-sure" @click="currSure">确定</div>
          </div>
        </mt-picker>
        <!-- 日期选择 -->
        <mt-datetime-picker
          v-show="!commonPicker && !levelPicker"
          ref="picker"
          type="date"
          :startDate='birthdayStartDate'
          :endDate ='birthdayEndDate'
          year-format="{value} 年"
          month-format="{value} 月"
          date-format="{value} 日"
          v-model="defaultDate"
          @confirm="handleConfirm"
          >
        </mt-datetime-picker>
      </mt-popup>
    </div>
  </div>
  
</template>

<script>
// import { mapState } from 'vuex'
// 省份城市数据
import {city, privinceList, cityList} from '../../../../static/js/region'
export default {
  name: 'CompleteInfo',
  data() {
    return {
      defaultDate: new Date('1990-6-15'),
      // isInfoPage: true,  //控制显示基本信息还是标签
      imageUrl: '',
      formData: '',
      commonPicker: true,
      levelPicker: false,
      // value1: '',  //昵称

      isShowInputName: false,
      inputNickName: '',
      inputNickNameValue: '',

      currSlots: this.slots1,
      currChange: this.changeSex,
      currSure: this.sureSex,
      sex: '男',
      sexValue: '',  //确定后的性别选择
      height: '160',
      heightValue: '',  //  确定后的身高选择
      birthdayStartDate: new Date('1930/1/1'),  //生日最小可选择
      birthdayEndDate: new Date(),  //生日最大可选择
      birthday: new Date('2000,6,15'),  //默认
      birthdayValue: '',  //确定后的生日选择
      levelName: '',  //级别
      levelSkey: '',
      levelList: [],
      birthRangeValue: '',  //年龄段
      profession: '公职人员',
      professionValue: '',  //确定后职业选择
      myprivinceList: [],    //省的数组
      mycityList: [],        //省对应城市的数组
      mydistrictList:[],     //区或者县的数组
      areapicker:'',
      myAddressPrivince:'',  //最后选中的省或直辖市
      myAddressCity:'',      //最后选中的城市
      myAddressDistrict:'',   //最后选中的区或者县
      addressValue: '',
      slots1: [
        {
          flex: 1,
          values: ['男', '女'],
          className: 'slot1',
          textAlign: 'center'
        }
      ],
      slots2: [
        {
          flex: 1,
          values: ['100', '101','102','103','104', '105','106','107','108', '109','110','111','112'
          , '113','114','115','116', '117','118','119','120', '121','122','123','124', '125','126'
          ,'127', '128','129','130', '131','132','133','134', '135','136','137', '138','139','140'
          ,'150', '151','152','153','154', '155','156','157', '158','159','160', '161','162','163'
          ,'164', '165','166','167', '168','169','170', '171','172','173','174', '175','176','177'
          , '178','179','180', '181','182','183','184', '185','186','187', '188','189','190', '191'
          ,'192','193','194', '195','196','197', '198','199','200'],
          className: 'slot1',
          defaultIndex: 51,
          textAlign: 'center'
        }
      ],
      professionSlots: [
        {
          flex: 1,
          values: ['公职人员', '专职技术人员','办事人员', '商业、服务人员','农林牧渔水利生产人员',
                   '生产运输人员','军人', '自由职业','创业','学生'],
          className: 'slotP',
          textAlign: 'center'
        }
      ],
      addressSlots: [
        {
          flex: 1,
          defaultIndex: 0,
          values:privinceList, //省份数组
          className: '_slot1',
          textAlign: 'center'
        },
        {
          pider: true,
          content: '-',
          className: '_slot2'

        }, {
          flex: 1,
          defaultIndex: 0,
          values: cityList,  //城市数据
          className: '_slot3',
          textAlign: 'center'
        },
      ],
      showToolbar: true,
      popupVisible: false,
      infoId: '',  //个人信息id,传给后端做标识
      selectedList: [],
      selectedListIds: [],
      labelsId: '',  //传给后端
      isShow: false,
      addValue: '',
      _id: '',
      showLabels: true,
      // lll: [],
      userId: '',
      timestamp: '',  //调取微信上传图片接口参数
      nonceStr: '',  //调取微信上传图片接口参数
      signature: '',  //调取微信上传图片接口参数
      localId: ''
    }
  },
  computed: {
    levelSlots() {
      let slots2 = [
        {
          flex: 1,
          values: this.levelList,
          className: 'slot1',
          textAlign: 'center',
          defaultIndex: 0,
        }
      ]
      return slots2
    }
  },
  created() {
    // 获取信息
    this.userId = window.localStorage.getItem('userId')
    this._id = window.localStorage.getItem('userId')
    this.getInfo()

    // 获取水平
    this.$http.findDictList('level').then(resp => {
      console.log(resp)
      if(resp.status == 200) {
        this.levelList = resp.data
      }
    })

    // 获取签名，调取微信图片上传接口
    this.$http.getSignature().then(resp => {
      console.log(resp)
      if(resp.status = 200) {
        this.timestamp = resp.data.timestamp
        this.nonceStr = resp.data.nonceStr
        this.signature = resp.data.signature
      }
    })
  },
  mounted() {
    this.$nextTick(() => { //vue里面全部加载好了再执行的函数 （类似于setTimeout）
      this.addressSlots[0].defaultIndex = 0
    })
  },

  watch: {
    myAddressPrivince(oldval,newval){  //省数据变化后，更新市的显示数据
      this.areapicker.setSlotValues(2,this.mycityList);
      this.areapicker.setSlotValue(2, this.mycityList[0]);
      // console.log('选中的省是'+this.myAddressPrivince);
    },
    myAddressCity(oldval,newval){    //城市的值改变后，重置区县的数据
      this.areapicker.setSlotValues(4,this.mydistrictList);
      this.areapicker.setSlotValue(4,this.mydistrictList[0]);
      // console.log('选中的市是'+this.myAddressCity);
    },
    myAddressDistrict(oldval,newval){
      // console.log('选中的区是'+this.myAddressDistrict);
    },
    showlabelList() {
      return JSON.parse(window.sessionStorage.getItem('labels'))
    },
    $route(to, from) {
      if(from.path == '/userCenter/selectLabels') {
        console.log(from)
        this.labelsId = window.sessionStorage.getItem('labelIds')
        this.selectedList = JSON.parse(window.sessionStorage.getItem('labels'))
        console.log(this.labelsId)
        console.log(this.selectedLabels)
      }
    },
  },
  methods: {
    getInfo() {
      this.selectedList = []
      this.selectedListIds = []
      this.showLabels = true
      // 获取信息
      this.$http.findPersonalInformation(this._id).then(resp => {
        console.log(resp)
        if(resp.status == 200) {
          this.infoId = resp.data.id
          this.imageUrl = resp.data.image
          this.inputNickNameValue = resp.data.name
          this.sexValue = resp.data.sex
          if(resp.data.height !== null) {
            this.heightValue = resp.data.height + 'cm'
          }else{
            this.heightValue = '请选择'
          }
          this.birthdayValue = resp.data.birthday
          if(resp.data.birthday !== null) {
            this.birthdayValue = resp.data.birthday
          }else{
            this.birthdayValue = '请选择'
          }
          this.birthRangeValue = resp.data.ageGroup
          this.levelName = resp.data.occupationLevelName
          this.levelSkey = resp.data.occupationLevelSkey

          this.professionValue = resp.data.occupation
          this.addressValue = resp.data.region
          if(resp.data.region !== null) {
            this.addressValue = resp.data.region
          }else{
            this.addressValue = '请选择'
          }
          this.selectedList = resp.data.labelVoList
          window.sessionStorage.setItem('labels',JSON.stringify(resp.data.labelVoList))
          // 提取标签id
          var _Ids = resp.data.labelVoList
          for(let i=0; i<_Ids.length; i++) {
            let currLab = _Ids[i]
            this.selectedListIds.push(currLab.id)
          }
          this.labelsId = this.selectedListIds.join(',')
          
          // 计算年龄段
          if(resp.data.ageGroup === null) {
            const birthYear = new Date(resp.data.birthday).getFullYear().toString()
            if(new Date(resp.data.birthday).getFullYear() <= 1969) {
              this.birthRangeValue = '69前'
            }else if(new Date(resp.data.birthday).getFullYear() >= 2000) {
              this.birthRangeValue = '00后'
            }else{
              this.birthRangeValue = birthYear.substring(2,3) + '0后'
            }
          }else{
            this.birthRangeValue = resp.data.ageGroup
          }
        }
      })
    },
    // 微信上传头像
    wxUploadImg() {
      const that = this
      wx.config({
        // debug: false,
        appId: 'wxd3d4d3045a1213a1',
        // appId: 'wxf1894ca38c849d17',  //测试号
        timestamp: that.timestamp,
        nonceStr: that.nonceStr,
        signature: that.signature,
        jsApiList: ['chooseImage','getLocalImgData']
      });
      wx.ready(function() {
        // 选择图片
        wx.chooseImage({
          count: 1, // 默认9
          sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
          sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
          success: function (res) {
            console.log(res)
            that.localId = res.localIds[0]; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
            // alert(localIds)
            // 图片上传
            wx.getLocalImgData({
              localId: that.localId, // 图片的localID
              success: function (res) {
                console.log(res)
                var localData = res.localData; // localData是图片的base64数据，可以用img标签显示
                if (localData.indexOf('data:image') != 0) {
                  //判断是否有这样的头部
                  localData = 'data:image/jpeg;base64,' +  localData
                }
                localData = localData.replace(/\r|\n/g, '').replace('data:image/jgp', 'data:image/jpeg')
                that.imageUrl = localData
                that.formData = new FormData()
                that.formData.append('file', localData);
                that.$http.testPostUpolad(that.formData).then((resp) => {
                  console.log(resp)
                  if (resp.status == 200) {
                    that.imageUrl = resp.data[0]; // 请求成功之后赋给头像的URL
                    that.$indicator.close();
                    that.$toast({
                      message: '头像上传成功！',
                      duration: 2000
                    });
                  } else {
                    that.$toast({
                      message: '头像上传失败！',
                      duration: 2000
                    });
                  }
                });
              } 
            });
          }
        });
      });
      // 调取微信接口失败
      wx.error(function(res){
        this.$toast({
          message: '头像上传失败，稍后重试！',
          duration: 2000
        });
      })
    },
    // 昵称输入
    showInput() {
      this.$messagebox.prompt('请填写昵称').then(({ value, action }) => {
        // console.log(value)
        this.inputNickNameValue = value
      })
    },
    blurInputName() {
      this.isShowInputName = false
      this.inputNickNameValue = this.inputNickName
    },
    // 性别选择
    showSexPicker() {
      this.popupVisible = true
      this.commonPicker = true
      this.levelPicker = false
      this.currSlots = this.slots1
      this.currChange = this.changeSex
      this.currSure = this.sureSex
    },
    changeSex(picker, values) {
      this.sex = values[0]
      if (values[0] > values[1]) {
        picker.setSlotValue(1, values[0]);
      }
    },
    sureSex() {
      this.popupVisible = !this.popupVisible
      this.sexValue = this.sex
      if(this.sexValue !== '女') {
        this.sexValue = '男'
      }
    },
    // 显示身高选择
    showHeightPicker() {
      this.popupVisible = true
      this.commonPicker = true
      this.levelPicker = false
      this.currSlots = this.slots2
      this.currChange = this.changeHeight
      this.currSure = this.sureHeight
    },
    changeHeight(picker, values) {
      this.height = values[0]
      if (values[0] > values[1]) {
        picker.setSlotValue(1, values[0]);
      }
    },
    sureHeight() {
      this.popupVisible = !this.popupVisible
      if(this.heightValue == '-请选择-') {
        this.heightValue = '160cm'
      }else{
        this.heightValue = this.height + 'cm'
      }
    },
    birRange() {
      this.$toast({
        message: '选择生日后自动改变！',
        duration: 1000
      });
    },
    // 显示生日选择
    showBirthdayPicker() {
      this.popupVisible = true
      this.commonPicker = false
      this.levelPicker = false
      // console.log(this.birthdayStartDate)
    },
    // 格式化选择的日期
    formatDate(Time) {
      var date = Time;
      var y = date.getFullYear();
      var m = date.getMonth() + 1;
      m = m < 10 ? ('0' + m) : m;
      var d = date.getDate();
      d = d < 10 ? ('0' + d) : d;
      var h = date.getHours();
      h = h < 10 ? ('0' + h) : h;
      var minute = date.getMinutes();
      var second = date.getSeconds();
      minute = minute < 10 ? ('0' + minute) : minute;
      return y + '-' + m + '-' + d;
    },
    handleConfirm(v) {
      // console.log(v.getTime())
      this.popupVisible = !this.popupVisible
      this.birthdayValue = this.formatDate(v)
      const birthYear = v.getFullYear().toString()
      if(v.getFullYear() <= 1969) {
        this.birthRangeValue = '69前'
      }else if(v.getFullYear() >= 2000) {
        this.birthRangeValue = '00后'
      }else{
        this.birthRangeValue = birthYear.substring(2,3) + '0后'
      }
    },
    // 级别选择
    showLevl() {
      this.popupVisible = true
      this.commonPicker = false
      this.levelPicker = true
      this.currSlots = this.levelSlots
      this.currChange = this.changeLevel
      this.currSure = this.sureLevle
    },
    changeLevel(picker, values) {
      this.levelSkey = values[0].skey
      this.levelName = values[0].name
      if (values[0] > values[1]) {
        picker.setSlotValue(1, values[0]);
      }
    },
    sureLevle() {
      if(this.levelName == '') {
        this.levelSkey = this.levelSlots[0].values[0].skey
        this.levelName = this.levelSlots[0].values[0].name
      }
      this.popupVisible = !this.popupVisible
      // this.levelName = this.levelName
    },
    //显示职业选择
    showprofessionPicker() {
      this.popupVisible = true
      this.commonPicker = true
      this.levelPicker = false
      this.currSlots = this.professionSlots
      this.currChange = this.changeProfession
      this.currSure = this.sureProfession
    },
    changeProfession(picker, values) {
      this.profession = values[0]
      if (values[0] > values[1]) {
        picker.setSlotValue(1, values[0]);
      }
    },
    sureProfession() {
      this.popupVisible = !this.popupVisible
      this.professionValue = this.profession
      // console.log(this.professionValue)
    },
    // 显示地区选择
    showLocation() {
      this.popupVisible = true
      this.commonPicker = true
      this.levelPicker = false
      this.currSlots = this.addressSlots
      this.currChange = this.onAddressChange
      this.currSure = this.sureAddress
    },
    onAddressChange:function(picker, values){
      this.areapicker = picker;
      this.mycityList = [];
      var chooseList = city.filter(function(item){
        return item.name == values[0];
      });
      if(chooseList[0].sub){
        for(var item of chooseList[0].sub){
          this.mycityList.push(item.name);
        }
        //获取非直辖市数据
        if(chooseList[0].sub.length>1){
          var choosedisList=[];
          if(this.mycityList.indexOf(values[2])>-1 && values[2]!= '其他'){
            choosedisList = chooseList[0].sub.filter(function(item){
              return item.name == values[2];
            });
              for(var item of choosedisList[0].sub){
                this.mydistrictList.push(item.name);
              }
          }else{
              this.mydistrictList=[];
          }
        }
        //获取直辖市数据
        else{
          for(var item of chooseList[0].sub[0].sub){
            this.mydistrictList.push(item.name);
          }
        }
      }
      this.myAddressPrivince = values[0];
      this.myAddressCity = values[2];
      this.myAddressDistrict = values[4];
    },
    sureAddress() {
      this.popupVisible = !this.popupVisible
      this.addressValue = this.myAddressPrivince + this.myAddressCity
    },
    // 选择标签
    selectLabel() {
      window.sessionStorage.setItem('labels',JSON.stringify(this.selectedList))
      this.$router.push({
        path: '/userCenter/selectLabels'
      })
      
    },
    // 提交信息
    submit() {
      window.scrollTo(0,0)
      if(this.height == '请选择') {
        this.height = null
      }
      if(this.birthdayValue == '请选择') {
        this.birthdayValue = null
      }
      if(this.addressValue == '请选择') {
        this.addressValue = null
      }
      const params = {
        id: this.infoId,
        userId: this.userId,
        image: this.imageUrl,
        name: this.inputNickNameValue,
        sex: this.sexValue,
        height: this.height,
        birthday: this.birthdayValue,
        occupation: this.professionValue,
        region: this.addressValue,
        occupationLevel: this.levelSkey,
        ageGroup: this.birthRangeValue,
        labelId: this.labelsId
      }
      console.log(params)
      this.$http.completeInfo(params).then(resp => {
        console.log(resp)
        if(resp.status == 200) {
          this.$toast({
            message: '保存成功！',
            duration: 1000
          });
          const _this = this
          setTimeout(function() {
            _this.$router.replace({
              path: '/userCenter/manageHome'
            })
          },1000)
        }
      })
    },
  },  
  beforeDestroy() {
    window.sessionStorage.removeItem('labelIds')
    window.sessionStorage.removeItem('labels')
  }
}
</script>

<style lang="scss" scoped>
  .completeInfo{
    width: 100%;
    min-height: 100vh;
    background: #f2f2f2;
    padding-top: 25px;
    overflow: hidden;
    // position: relative;
    .maxHeightBox{
      width: 100%;
      height: 81.5vh;
      overflow: auto;
      padding: 0 47px;
    }
    .heightBox2{
      width: 100%;
      height: 17vh;
      padding: 0 47px;
      overflow: hidden;
    }
    .touxiang{
      width: 100%;
      height: 140px;
      background: #fff;
      padding: 0 33px;
      line-height: 140px;
      font-size: 30px;
      color: #6d6d6d;
      margin-bottom: 6px;
      border-bottom: 1px solid #dddddd;
      box-shadow: 0 1px 1px #f1efef;
      .el-icon-arrow-right{
        width: 20px;
        height: 140px;
        line-height: 140px;
        font-size: 34px;
        display: block;
        float: right;
      }
      .avatar{
        width: 94px;
        height: 94px;
        float: right;
        margin-top: 20px;
        border: 1px dashed #d9d9d9;
        border-radius: 50%;
        margin-right: 15px;
      }
    }
    .ul1{
      width: 100%;
      height: auto;
      li{
        width: 100%;
        height: 100px;
        padding: 0 33px;
        background: #fff;
        border-top: 2px solid #f2f2f2;
        box-shadow: 0 -1px 2px #f2f2f2;
        border-radius: 5px;
        font-size: 30px;
        color: #545454;
        line-height: 100px;
        margin-bottom: 6px;
        .titleText{
          line-height: 100px;
          font-size: 28px;
          color: #6d6d6d;
          display: block;
          float: left;
        }
        input{
          width: 70%;
          height: 60px;
          line-height: 40px;
          padding-left: 10px;
          font-size: 25px;
          border: none;
          color: #868686;
        }
        span{
          line-height: 100px;
          float: right;
          margin-right: -10px;
          color: #6d6d6d;
          i{
            padding-left: 10px;
            font-size: 34px;
            color: #6d6d6d;
          }
        }
      }
    }
    .label{
      width: 100%;
      min-height: 200px;
      margin-top: 40px;
      background: #fff;
      p{
        width: 100%;
        height: 94px;
        line-height: 94px;
        color: #6d6d6d;
        border-top: 2px solid #f6f6f6;
        padding-left: 30px;
      }
      .itemBox{
        width: 100%;
        height: auto;
        padding-left: 40px;
        font-size: 0;
        .addBtn{
          width: 100px;
          height: 46px;
          line-height: 42px;
          text-align: center;
          font-size: 26px;
          color: #aaaaaa;
          border: 1px solid #aaaaaa;
          border-radius: 25px;
          font-weight: bold;
          display: inline-block;
          margin-right: 18px;
          margin-bottom: 30px;
          vertical-align: top;
        }
        .itemLabel{
          display: inline-block;
          width: 120px;
          height: 46px;
          line-height: 42px;
          border: 1px solid #f9c31b;
          color: #f9c21f;
          background: #fcebb8;
          border-radius: 25px;
          text-align: center;
          font-size: 22px;
          // padding: 0 40px;
          margin-right: 18px;
          margin-bottom: 30px;
          vertical-align: top;
        }
      }
    }
    .submit{
      width: 100%;
      height: 94px;
      line-height: 94px;
      color: #fff;
      text-align: center;
      background: #fac31e;
      margin-top: 45px;
      border-radius: 14px;
      letter-spacing: 1px;
      font-size: 32px;
    }
  }
</style>
<style>
  .completeInfo li .el-input{
    width: 300px;
    float: left;
    font-size: 25px;
    height: 50px;
    line-height: 50px;
    margin-left: 20px;
    margin-top: 23px;
    border: none;
    outline: none;
    /* border: 1px solid red; */
  }
  .completeInfo li .el-input .el-input__inner{
    width: 300px;
    float: left;
    font-size: 25px;
    height: 50px;
    line-height: 50px;
    border: none;
    outline: none;
  }
  .mint-msgbox{
    width: 75%;
  }
  .mint-msgbox-btns{
  height: 80px;
  line-height: 70px;
  
}
.mint-msgbox-input{
  padding-top: 40px;
  padding-bottom: 10px;
}
.mint-msgbox-message{
  color: rgb(37, 37, 37);
  font-size: 29px;
}
.mint-msgbox-confirm{
  color: #fac31e
}
.mint-msgbox-input input{
  height: 70px;
  width: 90%;
  margin-left: 5%;
  font-size: 26px;
}
.mint-msgbox-header{
  padding: 20px 0;
}
.mint-msgbox-title{
  font-size: 24px;
}
.mint-popup{
  width: 100%;
  transition: .3s ease-out;
}
.completeInfo .picker{
  width: 100%;
  background: #fff;
}
.completeInfo .picker-toolbar{
  height: 70px;
  padding: 0 100px;
}
.completeInfo .picker-toolbar-title{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  height: 70px;
  line-height: 90px;
  font-size: 24px;
}
.completeInfo .picker-slot{
  font-size: 26px;
}
.usi-btn-cancel,
.usi-btn-sure {
  font-size: 30px;
  text-align: center;
  color: #fac31e
}
.completeInfo .usi-btn-cancel{
  color: rgb(139, 138, 138)
}
.completeInfo ._slot1 .picker-item{
  padding-left: 80px;
}
.completeInfo ._slot3 .picker-item{
  padding-right: 80px;
}
.completeInfo .mint-datetime-action{
  line-height: 70px;
  color: #fac31e;
  font-size: 30px;
}
.completeInfo .picker-item{
  font-size: 30px;
}
.completeInfo .mint-datetime-cancel{
  color: rgb(139, 138, 138)
}
.completeInfo .mint-datetime-picker .picker-toolbar{
  padding: 0;
}
.completeInfo .mint-indicator-wrapper{
  padding: 20px 40px !important;
}
.completeInfo .mint-indicator-text{
  font-size: 20px;
}
</style>

