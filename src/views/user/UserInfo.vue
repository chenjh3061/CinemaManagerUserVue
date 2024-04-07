<template>
<div class="user-info-container">
  <div style="width: 30%">
    <h1>用户头像</h1>
    <el-upload action=""
               list-type="picture-card"
               :auto-upload="false"
               :limit="1"
               :file-list="pics"
               :on-change="handleChange"
               :on-success="handleSuccess"
               :on-error="handleError"
               :on-exceed="handleExceed"
               ref="pictureRef"
               :http-request="submitFile">
      <i slot="default" class="el-icon-plus"></i>
      <div slot="file" slot-scope="{file}">
        <img class="el-upload-list__item-thumbnail" :src="(picDialog? '' : global.base)+file.url" alt="">
        <span class="el-upload-list__item-actions">
          <span class="el-upload-list__item-delete" @click="handleRemove(file)">
            <i class="el-icon-delete"></i>
          </span>
        </span>
      </div>
    </el-upload>
  </div>
  <div style="width: 70%">
    <el-form :model="loginUser"  :rules="loginUserRules" ref="editFormRef" label-width="80px">
      <el-form-item label="用户名" prop="userName">
        <el-input v-model="loginUser.userName"></el-input>
      </el-form-item>
      <el-form-item label="电子邮箱" prop="email">
        <el-input v-model="loginUser.email"></el-input>
      </el-form-item>
      <el-form-item label="手机号码" prop="phoneNumber">
        <el-input v-model="loginUser.phoneNumber"></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
          <el-radio v-model="loginUser.sex" :label="true">男</el-radio>
          <el-radio v-model="loginUser.sex" :label="false">女</el-radio>
      </el-form-item>
      <el-form-item label="生日" prop="birthday">
        <el-date-picker
            v-model="loginUser.birthday"
            value-format="yyyy-MM-dd"
            type="date"
            placeholder="选择日期"></el-date-picker>
      </el-form-item>
      <el-form-item label="身份认证" prop="identity">
        <el-radio-group v-model="loginUser.userIdentity" @change="identify">
          <el-radio-button :label="0">普通用户</el-radio-button>
          <el-radio-button :label="1">学生认证</el-radio-button>
          <el-radio-button :label="2">长者认证</el-radio-button>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="个性签名" prop="autograph">
        <el-input v-model="loginUser.autograph"></el-input>
      </el-form-item>
        <el-button type="primary" @click="editUser" style="font-size: 20px;"> 保存</el-button>
    </el-form>
  </div>
</div>
</template>

<script>
export default {
  name: "UserInfo",
  data() {
    let checkEmail = (rule, value, cb) => {
      const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/
      if (regEmail.test(value)){
        return cb()
      }
      cb(new Error('请输入合法的邮箱'))
    }
    let checkMobile = (rule, value, cb) => {
      const regMobile = /^(0|86|17951)?(13[0-9]|15[0123456789]|17[678]|18[0-9]|14[57])[0-9]{8}$/
      if (regMobile.test(value)){
        return cb()
      }
      cb(new Error('请输入合法的手机号码'))
    }
    return {
      picDialog:false,
      loginUser:{
        userIdentity: 0
      },
      pics:[],
      pictureList:[],
      picSrc:"",
      deletePicList:[],

      //验证表单规则对象
      loginUserRules: {
        userName: [
          { required: true, message: '请输入用户名', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入电子邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur'}
        ],
        phoneNumber: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur'}
        ],
        sex: [
          { required: true, message: '请选择性别', trigger: 'change' }
        ],
        birthday: [
          { required: true, message: '请选择生日', trigger: 'change' }
        ]
      }
    }
  },
  created() {
    this.getUser()
  },
  methods:{
    async getUser(){
      const { data : res } = await axios.get('sysUser/'+JSON.parse(window.sessionStorage.getItem('loginUser')).userId)
      this.loginUser = res.data
      if(JSON.parse(this.loginUser.userPicture).length>0)
        this.pics.push({name:'picture',url:JSON.parse(this.loginUser.userPicture)[0]})
      else this.pics = [];
      this.loginUser.identity = res.data.userIdentity
    },
    async handleSuccess(response, file, fileList){
      this.pics = fileList
      console.log(this.pics)
      await this.submitFile()
      this.picSrc = this.pictureList[0]
    },
    handleError(err){
      console.log(err)
    },
    handleChange(file, filelist){
      this.pics = filelist.slice(0)
      console.log(111)
      console.log(this.pics)
      this.picDialog =true
    },
    handleRemove(file) {
      const filePath = file.url
      console.log(filePath)
      const idx = this.pics.findIndex(x => x.url === filePath)
      if(file.status === 'success'){
        this.deletePicList.push(file.url)
      }
      this.pics.splice(idx, 1)
    },
    identify(value){
      console.log(value, this.loginUser.userIdentity);
        // 根据用户选择的身份进行相应的处理
      if (this.loginUser.userIdentity === 1) {
        // 执行学生认证的操作，比如弹出上传学生证的小窗口

        const confirmResult = this.$confirm('确定进行学生认证吗？', '学生认证', {
          confirmButtonText: '确认',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(()=>{
          // 设置用户身份为学生
          this.loginUser.userIdentity = 1
        }).catch(() => {
          // 用户点击取消，设置用户身份为普通用户
          this.loginUser.userIdentity = 0
        })
      } else if (this.loginUser.userIdentity === 2) {
        // 执行长者认证的操作，根据用户的生日进行判断
        if (!this.loginUser.birthday) {
          // 如果用户的生日没有填写，则弹出提示让用户填写
          this.$message.warning('请填写您的生日信息')
          return
        }

        // 计算用户的年龄
        const birthday = new Date(this.loginUser.birthday)
        const today = new Date()
        const age = today.getFullYear() - birthday.getFullYear()

        // 判断用户是否达到了长者认证的条件（60岁以上）
        if (age >= 60) {
          // 用户通过长者认证，将用户身份设置为长者
          this.loginUser.userIdentity = 2
        } else {
          // 用户未达到长者认证条件，给出提示
          this.$message.warning('您未满足长者认证条件（60岁以上）')
          this.loginUser.userIdentity = 0
          return
        }
      }else{
        this.loginUser.userIdentity = 0
      }
    },
     async editUser(){
       await this.submitFile()
       this.loginUser.userPicture = JSON.stringify(this.pictureList)

        console.log(this.loginUser);
       //提交信息到后端保存
       const _this = this
       let success = true
       axios.defaults.headers.put['Content-Type'] = 'application/json'
       await axios.put('sysUser',JSON.stringify(this.loginUser)).then(resp=>{
         if (resp.data.code !== 200){
           this.$message.error('修改用户信息失败！')
           success = false
         }
       })
       if(!success) return
       this.$message.success('修改用户信息成功!')
       //清空图片列表病重新获取用户信息
       this.pics = []
       this.pictureList = []
       await this.getUser()
       this.picDialog = false
    },
    async submitFile(){
      const _this = this
      for (let i = 0; i < this.pics.length; i++){
        let formData = new FormData()
        if (this.pics[i].status === 'success') {
          let s = this.pics[i].url
          this.pictureList.push(s.substring(s.indexOf('/images')))
          continue
        }
        let file = this.pics[i].raw
        formData.append('file', file)
        await axios.post('upload/user', formData).then(response =>{
          console.log(response)
          _this.pictureList.push(response.data.data)
        })
      }
    },
    handleExceed(){
      this.$message.error("用户头像最多上传一张！")
    },
  }
}
</script>

<style scoped>
.user-info-container{
  display: flex;
}
</style>
