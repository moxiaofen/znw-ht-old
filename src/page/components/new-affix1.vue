<template>
<div>
 
    <div class='cont-sides'>
        <p class="cont-item">附件信息      
        </p>
        <img  v-if="itemShow" class="add-img" src="static/assets/icon_add.png"  @click="showPopup"/>
    </div>
    <Divider></Divider>
    <!-- 客户认证上传数据列表 -->
    <div v-if="itemShow">
      <div class="affix-lib">
        <div class="affix-type">
           <span >资料类型</span>
        </div>

        <div class="affix-name">
         <span >附件名称</span>
        </div>

        <div class="affix-remark">
            <span >备注</span>
        </div>
      </div>
      <div v-for="(item,index) in handleFileList" :key="index">
          <div class="affix-lib por">
            <div class="affix-type">
              <span >{{item.dataText}}</span>
            </div>
            <div class="affix-name">
            <span >{{item.fileName}}</span>
            </div>

            <div class="affix-remark">
                <span >{{item.fileDesc}}</span>
            </div>
            <img class='clear-file' @click="clearFile(index)" src="/static/assets/gray-del.png"/>
          </div>
      </div>

    </div>

    <Divider></Divider>

    <!-- 弹窗 -->
    <van-popup v-model="show" bind:close="onClose" class="pop-content">
        <!--资料类型-->
        <div class='top'>
          <span class='common'>资料类型</span>
          <select name="" id=""  v-model="selected"  class="pop-select">
              <!-- option的value赋值对象 -->
              <option v-for="(item,index) in optDate" :value="item" :key="index">{{item.text}}</option>
          </select>  
        </div>
        <!--横线-->
        <Divider></Divider>
        
        <!--说明-->
        <div class='top'>
          <span class='inputLable'>资料说明</span>
          <input class='inputVal' type="text" ref="rFileDesc" v-model="vFileDesc" placeholder="请输入说明" />
          <img class='clearCss' v-show="vFileDesc" @click.stop ="clear('vFileDesc')" src="/static/assets/clear.png" />
        </div>
        <!--横线-->
        <Divider></Divider>

        <!--附件名称回填的附件名称-->
        <div class='top'>
          <span class='inputLable'>附件名称</span>
          <input class='inputVal' type="text" ref="rFileName" v-model="vFileName" placeholder="请输入附件名称" />
          <img class='clearCss' readonly="true" v-show="vFileName" @click.stop ="clear('vFileName')" src="/static/assets/clear.png" />
        </div>
        <!--横线-->
        <Divider></Divider> 

        <div class="file">
            <img class="img-photo hide" ref='img' alt="handleFileList" :src="src" />
            <a class="file-button" href="javascript:void(0);">上传</a>
            <input type="file" multiple class="file-input" ref="inputImg" @change.stop="handleFile" >
        </div>

        <div class="file tac">
            <a class="file-button confirm" @click="submitHandleFile">确定</a>
            <a class="file-button cancel" @click="onClose">取消</a>
        </div>

    </van-popup>



</div>
</template>

<script>
  import Divider from '@/components/Divider.vue'
    export default {
        components: {
          Divider,
        },
        props:["optDate",'itemShow','handleFileList'],
        computed: {},
        data() {
            return {
              nonet: false,//断网
              enable: true,//立即注册 按钮默认不可用
              show: false,//测试时为true
              //vDataType:this.optDate[0].value,
              //给select赋默认值
              selected:{'value':this.optDate[0].value,'text':this.optDate[0].text},
              vFileDesc:"",
              vFileName:"",
              src:"",
              handleFileList:[],
            }
        },
        mounted(){
                      
        },
        methods: {

          //清除输入框数据
          clear (str) {
            this[str] = ''
          },     
          //上传多种文件
          handleFile() {
              //console.log()
              const file = this.$refs.inputImg.files[0]
              console.log(file)
              this.vFileName = file.name;
              const rimg = this.$refs.img;
              var reader = new FileReader();
              reader.readAsDataURL(file);
              reader.onload = function (e) {
                      rimg.src = e.target.result;
              }
              var dataFile = {
                dataText : this.selected.text ,
                dataType : this.selected.value ,
                fileName : this.vFileName ,
                fileDesc : this.vFileDesc 
             }
              //获取其他file文件，用new FormData()
            //   this.vFileName = this.$refs.inputImg.files[0].name;
            //   var fd = new FormData();
            //   fd.append('file',this.$refs.inputImg.files[0])
             console.log(dataFile)
             return dataFile
          },

          //企业认证上传列表确定
          submitHandleFile(){
              //inputfile的base64字符串去掉','前的部分，不然传给后端的数据有误
              const dataFile = this.handleFile();
              dataFile.fileInput = this.$refs.img.src.slice(22);
              this.handleFileList.push(dataFile);
              console.log(this.handleFileList);
              this.show = false;
          },
          //删除企业认证列表
          clearFile(index){
              this.handleFileList.splice(index, 1);
          },

          //弹窗
          showPopup() {
            this.show = true;
            this.vFileDesc = '';
            this.vFileName = '';
            this.src = '';
          },
          onClose() {
            this.show = false
          },                
        
        },

    }
</script>

<style scoped>

  .top {
    display: flex;
    flex-direction: row;
    justify-content: space-between; 
    /*background-color:burlywood;*/
    position: relative;
    padding: 0 32px;
  }

  .inputLable {
    font-size: 0.4rem;
    line-height: 45px;
    border: none;
    padding: 55px 0 20px;
  }
.inputVal{
  font-size: 0.4rem;
  line-height: 45px;
  border: none;
  padding: 55px 0 20px;
  margin-right: 30px;
  text-align: right;
}
  .common {
    font-size: 0.4rem;
    line-height: 45px;
    border: none;
    padding: 55px 0 20px;
  }

  .clearCss {
    width: 21px;
    height: 21px; 
    /*margin: 68px 30px 30px;*/
    position: absolute;
    right: 32px;
    top: 68px;
  }
  .add-img {
    width: 50px;
    height: 50px;
    padding: 20px 30px 18px
  } 
  .cont-sides{
    display: flex;
    -webkit-box-orient: horizontal;
    -webkit-box-direction: normal;
    flex-direction: row;
    -webkit-box-pack: justify;
    -ms-flex-pack: justify;
    justify-content: space-between;
    background-color: #fafafa;
  }

  /* 弹窗样式 */
  .pop-content{
    width: 80%;
  }
  .pop-select{
    outline:none;
    /* -webkit-appearance:none; */
    -webkit-tap-highlight-color:rgba(255,0,0,0);
    border: none;
    text-align: right;
    text-align-last: right;
    background: transparent;
  }

  .file{
     /* text-align: center; */
     padding: 30px;
     position: relative;
  }
  .file-button{
      /* width: 120px;
      height: 60px; */
      color: #fff;
    background-color: #5cb85c;
    border-color: #4cae4c;
    padding: 10px 40px;
    border-radius: 3px;
  }
  .file input{
    position: absolute;
    top: 0;
    left: 0;
    width: 30%;
    height: 100%;
    opacity: 0;
  }
  .tac{
    text-align: center;
  }
  .confirm{
    background: #fec301;
    margin-right: 20px;
    border: 1px solid #fec301;
  }
  .cancel{
    background-color: #fff;
    margin-left: 20px;
    color: #333;
    border: 1px solid #666;
  }
  .hide{
    display: none;
  }

 .affix-lib {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: row;
    font-size: 30px;
    padding: 20px 32px;
    border-bottom: 1px solid #eee;
  }

  .affix-type {
      width: 30%;
  }

  .affix-name {
  width: 40%;
  }

  .affix-remark {
  width: 30%;
  }

  .por{
    position: relative;
  }
  .clear-file{
    position: absolute;
    top: 24px;
    right: 32px;
    width: 26px;
    height: auto;
  }

</style>
