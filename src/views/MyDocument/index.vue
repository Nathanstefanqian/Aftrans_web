<template>
  <main class="my-main-layout">
    <div class="my-main-doc-menu">
      <span class="my-main-doc-menu-title" @click="showUploadDiv" :class="{ active: currentIndex === 0 }">文档翻译</span>
      <span class="my-main-doc-menu-space">|</span>
      <span class="my-main-doc-menu-title" @click="showProjectDiv" :class="{ active: currentIndex === 1 }">任务管理</span>
    </div>
    <div class="my-main-doc-toggle">
      <div class="my-main-doc-toggle-upload-header" v-show="showUpload">
        <el-select v-model="curValue" placeholder="当前语言" filterable>
          <el-option v-for="item in data.Lang " :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
        <img class="my-main-doc-toggle-upload-header-trans" src="@/assets/myText/translate.svg">
        <el-select v-model="tarValue" placeholder="目前只支持中文">
          <el-option v-for="item in data.lang " :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </div>
      <UploadFileBtn 
      className="my-main-doc-toggle-upload" 
      :fromLang="this.data.Lang[this.curValue-1].name"
      :allowedTypes="allowedTypes"
      tip="只能上传DOC,DOCX,PDF,XLS,XLSX,PPT,PPTX,TXT格式的文件"
      url="/trans/doc" 
      v-show="showUpload" 
      @btnClick="getData" />
      <div class="my-main-doc-toggle-project" v-show="showProject">
        <div class="my-main-doc-toggle-project-header">
          <div class="my-main-doc-toggle-project-header-left">
            <el-input placeholder="请输入文件名" v-model="inputSearch" style="width: 200px"></el-input>
            <el-button type="primary" style="background-color: #3867FF; margin-left: 10px" @click="getDataByid">查询</el-button>
          </div>
          <div class="my-main-doc-toggle-project-header-right">
            <el-button type="primary" style="background-color: #3867FF; margin-left: 10px" @click="getData(true)">刷新</el-button>
          </div>
        </div>
        <div class="my-main-doc-toggle-project-table">
          <el-table :data="data.workList" border  max-height="580px"> 
            <el-table-column prop="index" align="center" label="序号" width="40"></el-table-column>
            <el-table-column prop="id" label="DOCID" width="120" ></el-table-column>
            <el-table-column prop="filename" label="文件名" width="180"></el-table-column>
          <el-table-column prop="size" label="文件大小" width="120"></el-table-column>
            <el-table-column prop="from" label="当前语言" width="120">
            </el-table-column>
            <el-table-column prop="" label="目标语言" width="120">中文</el-table-column>
            <el-table-column prop="status" label="翻译状态" width="100" >
              <template slot-scope="{ row }">
                {{ mapStatus[row.status] }}
              </template>
            </el-table-column>
            <el-table-column prop="createTime" label="创建时间" width="180"></el-table-column>
            <el-table-column prop=""  label="操作">
              <template slot-scope="{ row }">
                <div class="button-container">
                  <el-button type="danger"  @click="deleteData(row)" size="mini">删除</el-button>
                    <el-button type="primary" width="20%" size="mini" style="background-color: #3867FF; margin-left: 10px" @click="getDownloadRes(row)">译文下载</el-button>
                    <el-button type="primary" width="20%" size="mini" style="background-color: #3867FF; margin-left: 10px" @click="getDownloadSrc(row)">原文下载</el-button>
                </div> 
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </div>  
  </main>
</template>
<script>
  import api from '@/api'
  import UploadFileBtn from '@/components/UploadFileBtn'
  import data from '@/models/MyTextnDoc/index'
  export default {
  components:{
    UploadFileBtn
  },
  data() {
    return {
      ...data,
      checked: false,
      currentIndex: 0,
      curValue:4,
      tarValue:'',
      showUpload: true,
      showProject: false,
      orderValue: '',
      transValue: '',
      inputSearch:'',
      mapStatus: ['未翻译','正在翻译','翻译完成','不支持类型','出错'],
      allowedTypes: [
      'application/msword',          // doc
      'application/vnd.openxmlformats-officedocument.wordprocessingml.document',  // docx
      'application/pdf',             // pdf
      'application/vnd.ms-excel',    // xls
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',         // xlsx
      'application/vnd.ms-powerpoint',  // ppt
      'application/vnd.openxmlformats-officedocument.presentationml.presentation', // pptx
      'text/plain',                  // txt
      ]
    }
  },
  created() {
  this.getData()
  },
  methods:{
    ...api.mydocument,
    showUploadDiv() {
      this.showUpload = true;
      this.showProject= false;
      this.handleChangeColor(0)
    },
    showProjectDiv() {
      this.showProject= true;
      this.showUpload = false;
      this.handleChangeColor(1)
    },
    handleChangeColor(index) {
      if(index !== this.currentIndex) {
        this.currentIndex = index;
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.my-main-doc {
  &-menu {
  display: flex;
  &-title {
  cursor: pointer;
  font-weight: 600;
  &:hover {
  transition: all 0.5s;
  color: #3867FF;
    }
  }
  &-space {
    margin: 0 8px; } }
  &-toggle {
    margin-top: 20px;
  
    &-upload{
      display: flex;
      align-items: center;
      justify-content: center; 
      flex-direction: column;
      width:100%;
      height:350px;
      border: 1px dotted #999;
      &-header{
        margin-top:60px;
        margin-bottom:30px;
        >img{
          margin: 0 10px;
        }
      }
      &-btn {
        width: 220px;
        height: 40px;
        line-height: 40px;
        text-align: center;
        background-color: #4366f6;
        box-shadow: 0 6px 13px rgb(34 119 230 / 25%);
        border-radius: 4px;
        color: #fff;
        cursor: pointer;
      }
    }
    &-project {
      display: flex;
      flex-direction: column;
      &-header {
        display: flex;
        justify-content: space-between;
      }
      &-table {
        margin-top: 20px;
      }
    }
  }
}
.active {
  color: #3867FF;
}
.button-container {
  display: flex;
}
</style>