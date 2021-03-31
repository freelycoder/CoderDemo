<template>
  <div class="myvideo myvideo_zs">
    <!-- 知识列表 -->
	<div class="zs_part1" v-if="partStyle == false" >
	  <div class="auth_sel" v-if="role=='01'">
	      <div class="fr addVideo_bt addVideo_bt1" @click="addNews">
	          <i class="el-icon-plus"></i>
	          添加文章
	      </div>
	      <div class="cf"></div>
	  </div>
	  <div class="zs_center">
		  <div class="fl zs_center_left">
			  <div class="zs_center_left1">
				  <div class="fl zs_center_left11"><img src="../assets/images/book.gif" alt=""></div>
				  <div class="f2 zs_center_left12">分类</div>
				  <div class="cf"></div>
			  </div>
			  <div class="zs_center_left2">
				  <div class="zs_center_left21" v-for="(item,index) in newsTypesList" :key="index" :class="{'zs_active':active==index}" @click="selNews(item,index)">
					  <div>{{item.label}}</div>
				  </div>
			  </div>  
			  
		  </div>
		  <div class="fl zs_center_right">
			  <el-table :data="newsList" border style="width: 100%" @row-click="goDetail">
				  <el-table-column label="文章编号" prop="nid" width="100" align="center"></el-table-column>
				  <el-table-column label="标题" align="left">
					  <template slot-scope="scope" >
						<div class="fl">{{scope.row.title}}</div>
						<div class="fl" v-if="scope.row.type == '01'" style="color:#de88df" >【超声知识】</div>
						<div class="fl" v-if="scope.row.type == '02'" style="color:#55deb5">【急危重症知识】</div>
						<div class="fl" v-if="scope.row.type == '03'" style="color:#ffce4b">【超声学习资讯】</div>
						<div class="fl" v-if="scope.row.type == '04'" style="color:#46e4fc">【超声仪器介绍】</div>
						<span class="iconfont huixingzhen" v-if="scope.row.file.length !== 0">&#xe619;</span>
					  </template>
				  </el-table-column>
				  <el-table-column prop="address" label="操作" width="180" align="center" v-if="role=='01'">
					<template slot-scope="scope" >
						<el-button @click.stop="setTop(scope)" type="text" size="small" class="zs_yellow">
						  置顶
						</el-button>
						<el-button @click.stop="editRow(scope)" type="text" size="small">
						  编辑
						</el-button>
						<el-button @click.stop="deleteRow(scope)"  type="text" size="small" class="zs_red">
						  删除
						</el-button>
					</template>
				  </el-table-column>
				</el-table>
			  <div class="zs_page">
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
                           :current-page="currentPage" :page-sizes="[10,20,30,40]"
                           :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper"
                           :total="tableCount">
            </el-pagination>
			    <div class="cf"></div>
			  </div>
		  </div>
		  <div class="cf"></div>
	  </div>
	  
	</div>
	
    <!-- 添加视频的弹窗 -->
    <div class="zs_part2" v-if="partStyle == true">
		
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="60px" class="zs_form">
		<div class="fl zs_part2_left">
			<div class="zs_part2_left1">
				<div class="fl">文章设置</div>
				<el-upload
						class="fr zs_part2_left11"
						:action="importDocUrl"
						:on-remove="pdfRemove"
						:on-preview="pdfReview"
						accept=".doc,.docx"
						:limit="1"
						:on-exceed="listNum"
						:before-remove="delPdfFile"
						:file-list="importDoc"
						:http-request="impoortDoc"
						>
						<i class="el-icon-folder-add" style="font-size: 18px;margin-right: 2px;"></i>文章导入
				</el-upload>
				
				<div class="cf"></div>
			</div>
			<el-form-item label="标题:" prop="title">
				<el-input type="textarea" v-model="ruleForm.title" placeholder="请在这里输入标题"></el-input>
			</el-form-item>
			<el-form-item label="作者:" prop="author">
				<el-input type="text"  v-model="ruleForm.author" placeholder="请输入作者"></el-input>
			</el-form-item>
			<el-form-item label="分类:" prop="type">
				<el-select v-model="ruleForm.type" placeholder="请选择分类">
				  <el-option
					v-for="item in newsTypesList1"
					:key="item.value"
					:label="item.label"
					:value="item.value">
				  </el-option>
				</el-select>
			</el-form-item>
			<el-form-item label="附件:" prop="region">
                  <el-upload
                          class="sel2"
                          :action="uploadUrl"
                          :on-preview="pdfReview"
                          :on-remove="pdfRemove"
                          accept=".pdf,.PDF,.xls,.xlsx,.doc,.docx,.ppt,.pptx,.jpg,.jpeg,.png,.gif,.mp4,.mov"
                          :limit="5"
                          :on-exceed="listNum"
                          :before-remove="delPdfFile"
                          :file-list="ruleForm.fileList"
                          :http-request="uploadPDF"
                          >
                  <el-button slot="trigger" class="sel1 fr daoru" type="warning">上传附件</el-button>
                  </el-upload>
			</el-form-item>
			
			
		</div>
		<div class="fl zs_part2_right">
			<!-- <el-form-item label="附件:" prop="region">
                  <el-upload
                          class="sel2"
                          :action="importDocUrl"
                          :on-preview="pdfReview"
                          :on-remove="pdfRemove"
                          accept=".doc,.docx"
                          :limit="1"
                          :on-exceed="listNum"
                          :before-remove="delPdfFile"
                          :file-list="importDoc"
                          :http-request="impoortDoc"
                          >
                  <el-button slot="trigger" class="sel1 fr daoru" type="warning">上传附件</el-button>
                  </el-upload>
			</el-form-item>


			<el-form-item label="" prop="region" class="zs_part2_right1"  label-width="0px" >
			  <el-input type="text"   v-model="ruleForm.title" placeholder="请在这里输入标题"></el-input>
			</el-form-item>
			<el-form-item label="" prop="region"  label-width="00px" >
			  <el-input type="text"  v-model="ruleForm.author" placeholder="请输入作者"></el-input>
			</el-form-item> -->
			<div class="zs_part2_right1">
				<el-form-item label=""  label-width="0px" prop="content" class="zs_part2_right1a">
				<el-upload
					class="wenzhang_upload"
					:action="uploadUrl"
					accept=".jpg,.jpeg,.png,.gif"
					:file-list="ruleForm.imgList"
					:http-request="uploadIMG"
					>
				
				<el-button slot="trigger" class="sel1 fr daoru" type="warning">上传附件</el-button>
				</el-upload>
				<el-upload
					class="video_upload"
					:action="uploadUrl"
					accept=".mp4"
					:before-upload="beforeAvatarUpload"
					:file-list="ruleForm.videoList"
					:http-request="uploadVIDEO"
					>
					<el-button slot="trigger" class="sel1 fr daoru" type="warning">上传附件</el-button>
				</el-upload>
				<quill-editor v-model="ruleForm.content" ref="myQuillEditor" class="editor" :options="editorOption" @blur="onEditorBlur($event)" @focus="onEditorFocus($event)" @change="onEditorChange($event)">
				</quill-editor>
				</el-form-item>
				<el-form-item label="" class="zs_part2_right1b" label-width="0px">
				  <div class="zs_bt_center">
					<button class="zs_bt1" @click.prevent="submitForm('ruleForm')">发布</button>
					<button class="zs_bt2"  @click.prevent="closeForm('ruleForm')">取消</button>
				  </div>
				</el-form-item>
			</div>
			
		</div>
		<div class="cf"></div>
        
      </el-form>
    </div>
  </div>
</template>

<script>
	import 'quill/dist/quill.core.css'
	import 'quill/dist/quill.snow.css'
	import 'quill/dist/quill.bubble.css'
	import { quillEditor } from 'vue-quill-editor'
	import Sortable from 'sortablejs'
	// 工具栏配置
	const toolbarOptions = [
		['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线 -----['bold', 'italic', 'underline', 'strike']
		['blockquote', 'code-block'], // 引用  代码块-----['blockquote', 'code-block']
		[{ header: 1 }, { header: 2 }], // 1、2 级标题-----[{ header: 1 }, { header: 2 }]
		[{ list: 'ordered' }, { list: 'bullet' }], // 有序、无序列表-----[{ list: 'ordered' }, { list: 'bullet' }]
		[{ script: 'sub' }, { script: 'super' }], // 上标/下标-----[{ script: 'sub' }, { script: 'super' }]
		[{ indent: '-1' }, { indent: '+1' }], // 缩进-----[{ indent: '-1' }, { indent: '+1' }]
		[{'direction': 'rtl'}], // 文本方向-----[{'direction': 'rtl'}]
		[{ size: ['small', false, 'large', 'huge'] }], // 字体大小-----[{ size: ['small', false, 'large', 'huge'] }]
		[{ header: [1, 2, 3, 4, 5, 6, false] }], // 标题-----[{ header: [1, 2, 3, 4, 5, 6, false] }]
		[{ color: [] }, { background: [] }], // 字体颜色、字体背景颜色-----[{ color: [] }, { background: [] }]
		[{ font: [] }], // 字体种类-----[{ font: [] }]
		[{ align: [] }], // 对齐方式-----[{ align: [] }]
		['clean'], // 清除文本格式-----['clean']
		['link', 'image', 'video'] // 链接、图片、视频-----['link', 'image', 'video']
	]
export default {
	components: { quillEditor },
    data() {
      return {
		// 搜索框关键词
		searchContent:'',
		uploadUrl:'/uploadVideo',
		importDocUrl:'/importDocUrl',
		currentPage: 1,
        tableCount:0,
        pageSize:10,
		// 文章导入
		importDoc:[],
        newsTypesList: [{
          value: 'all',
          label: '全部'
        },{
          value: '01',
          label: '超声知识'
        },{
          value: '02',
          label: '急危重症知识'
        },{
          value: '03',
          label: '超声学习资讯'
        },{
		  value: '04',
		  label: '超声仪器介绍'
		}],
		newsTypesList1: [{
		  value: '01',
		  label: '超声知识'
		},{
		  value: '02',
		  label: '急危重症知识'
		},{
		  value: '03',
		  label: '超声学习资讯'
		},{
		  value: '04',
		  label: '超声仪器介绍'
		}],
		role:'',
        selectNewsType:'all',
        newsList:[],
        partStyle:false,// 添加文章弹窗的状态
        shoucang:true,//收藏
        ruleForm: {
          title: '',//标题
		  author:'',//作者
		  type:'',
          fileList:[],//附件
		  imgList:[],
		  videoList:[],
          link:'',//链接
          content:''//内容
        },
        rules: {
			title: [
			  { required: true, message: '请输入标题', trigger: 'blur' },
			],
			author: [
			  { required: true, message: '请输入作者', trigger: 'blur' },
			],
			type: [
			  { required: true, message: '请选择分类', trigger: 'change' },
			],
			content: [
			  { required: true, message: '请输入文章内容', trigger: 'blur' },
			],
		},
        ////////////////////////////
        /////////添加文章////////////
        imgList:[],
		active:0,//导航切换的样式
		/////////////////
		//////编辑器//////
		videoFlag: false,
		//是否显示进度条
		videoUploadPercent: "",
		//进度条的进度，
		isShowUploadVideo: false,
		//显示上传按钮
		videoForm: {
		    showVideoPath: ''
		},
		nid:'',
		flag:'',
		content: null,
		dialogFnOpenUpload:false,
		editorOption: {
		  placeholder: '请输入文本...',
		  theme: 'snow',
		  modules: {
		    toolbar:{
		      container: toolbarOptions,
		      handlers: {
		        'video': function(value) {
		          if (value) {
		          	document.querySelector('.video_upload .el-upload').click()
		          } else {
		          	this.quill.format('video', false);
		          }
		        },
				'image': function (value) {
					if (value) {
						document.querySelector('.wenzhang_upload .el-upload').click()
					} else {
						this.quill.format('image', false);
					}
				}
		      }
		    }
		  }
		}
      };
    },
    created(){
	  this.role = localStorage.getItem('role');
	  var para = this.$router.history.current.query;
	  if(para.searchContent){
			this.searchContent = this.common.decodePara(para.searchContent);
	  }
      this.getNews({page:this.currentPage,limit:this.pageSize});
    },
	mounted() {
	  this.rowDrop()
	},
    methods: {
		// 文本编辑器上传图片
		async uploadIMG(content){
			let that = this
			let formData = new FormData();
			formData.append('file', content.file);
			var res = await that.common.postData(content.action,formData);
			let quill = that.$refs.myQuillEditor.quill
			if(res.data.state === "0000"){
				var imageUrl = res.data.body.url
				// console.info(res.data)
				// 获取光标所在位置
				let length = quill.getSelection().index;
				// 插入图片，res为服务器返回的图片链接地址
				quill.insertEmbed(length, 'image', imageUrl)
				// 调整光标到最后
				quill.setSelection(length + 1)
			}else{
			  that.$message.error(res.data.body[1].errmsg);
			}
		},
		// 文本编辑器上传视频
		async uploadVIDEO(content){
			let that = this
			let formData = new FormData();
			formData.append('file', content.file);
			var res = await that.common.postData(content.action,formData);
			console.info(res)
			let quill = that.$refs.myQuillEditor.quill
			console.info(res.data)
			if(res.data.state === "0000"){
				var imageUrl = res.data.body.url
				let length = quill.getSelection().index;
				quill.insertEmbed(length, 'video', imageUrl)
				quill.setSelection(length + 1)
			}else{
			  that.$message.error(res.data.body[1].errmsg);
			}
		},

		// 视频置顶
		async setTop(obj){
			var res = await this.common.postData("/setTop",obj.row);
			if(res.data.state == "0000" ){
			   var data  = {page:this.currentPage,limit:this.pageSize};
			   this.getNews(data);
			}
		},

		// 拖拽的代码
		rowDrop() {
		   var that = this;
		   const tbody = document.querySelector('.el-table__body-wrapper tbody')
		   Sortable.create(tbody, {
			 onMove: evt => {
			   const {dragged, related} = evt;
			 },
			 onEnd: async(evt) => {
			   console.info(evt.newIndex)
			   // 拖动结束，查询上下的记录ID
			   var nextRecord = this.newsList[evt.newIndex+1];
			   var postData ={};
			   if(!nextRecord){
				   // 取下一页的第一条
					var res = await this.common.postData("/getNews",{selectNewsType:this.selectNewsType,pageInfo:{page:this.currentPage+1,limit:this.pageSize}});
					if(res.data.state == "0000" ){
						if(res.data.body.newsList){
							nextRecord = res.data.body.newsList[0];
						}else{
							// 排到了最后一列
							nextRecord = null;
						}
					}else{
						this.$message.error(res.data.body[1].errmsg)
					}
			   }
			   postData ={
					preRecord:this.newsList[evt.newIndex],
					nextRecord:nextRecord,
					currentRecord:this.newsList[evt.oldIndex]
			   }
			   var res = await this.common.postData("/alterSortIdx",postData);
      
			   if(res.data.state == "0000" ){
				   var data  = {page:that.currentPage,limit:this.pageSize};
				   that.getNews(data);
			   }

			 },
			 filter: '.disabled-drag'
		   });
		},

      async getNews(data){
		  this.newsList =[];
          var res = await this.common.postData("/getNews",{selectNewsType:this.selectNewsType,pageInfo:data,searchContent:this.searchContent});
      
          if(res.data.state == "0000" ){
              this.newsList = res.data.body.newsList;
			  this.tableCount = res.data.body.countList[0].cnt;
          }else{
              this.$message.error(res.data.body[1].errmsg)
          }
      },
      selNews(val,index){
		  this.active = index;
		  this.selectNewsType = val.value
		  this.getNews({page:this.currentPage,limit:this.pageSize});
      },
      // 跳转到详情页
		goDetail(row, column, event){
			window.localStorage.setItem('content',JSON.stringify(row));
			this.$router.push('/newsDetail')
		},
		// 添加文章
		addNews(){
			this.flag = 1;
			this.partStyle = true
		},
		//编辑文章
		editRow(scope){
			this.flag = 2;
			console.info(scope)
			this.nid = scope.row.nid;
			this.partStyle = true
			this.ruleForm = scope.row
			this.ruleForm.fileList = scope.row.file
		},
		//删除文章
	   deleteRow(scope){
			this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
			  confirmButtonText: '确定',
			  cancelButtonText: '取消',
			  type: 'warning'
			}).then(async() => {
				this.nid = scope.row.nid;
				var data = {page:this.currentPage,limit:this.pageSize};
				var res = await this.common.postData("/delNews",{nid:this.nid});
				if(res.data.state == "0000" ){
					var data  = {page:this.currentPage,limit:this.pageSize};
					this.getNews(data);
				}else{
					
				}
				this.$message({
					type: 'success',
					message: '删除成功!'
				});
			}).catch(() => {
			//   this.$message({
			// 	type: 'info',
			// 	message: '已取消删除'
			//   });          
			});
			
		},
      ///////////////////////////////////////////////////////////////////////
      //////////////////////////////////////////////////////////////////////
      async imgUpload(content) {
          let formData = new FormData();
          formData.append('file', content.file);
          var res = await this.common.postData(content.action,formData);
          if(res.data.state === "0000"){
              this.imgList.push(res.data.body)
          }else{
              this.allDialog = this.common.openDialog(res.data.msg);
          }
      },
    

      /////////////////////////////////////////////////////////////////////////
	  ////////附件上传//////////////////////////////////////////////////////////
	  ////////////////////////////////////////////////////////////////////////
		// 文章导入
		async impoortDoc(content){
                let that = this
                let formData = new FormData();
                formData.append('file', content.file);
                var res = await that.common.postData(content.action,formData);
                if(res.data.state === "0000"){
					this.ruleForm.content = res.data.body.content;
                    this.importDoc.push(res.data.body)
                }else{
                   // that.$message.error(res.data.body[1].errmsg);
                }
        },

          async uploadPDF(content){
                let that = this
                let formData = new FormData();
                formData.append('file', content.file);
                var res = await that.common.postData(content.action,formData);
                if(res.data.state === "0000"){
                    that.ruleForm.fileList.push(res.data.body)
                }else{
                    that.$message.error(res.data.body[1].errmsg);
                }
            },
            //上传PDF时校验类型与大小
            validatePDF(file) {
                let that = this
                const isLt20M = file.size / 1024 / 1024 < 20;
                const isLtType = ['application/pdf','video/mp4','xls', 'xlsx','image/jpeg'].indexOf(file.type) != -1;
                if (!isLt20M) {
                    that.$message.error('上传PDF大小不能超过20MB!');
                }
                if (!isLtType) {
                    that.$message.error("请上传正确的PDF格式");
                }
                return isLt20M&&isLtType;
            },
            //限制附件数量
            listNum(files, fileList){
                this.$message.error('PDF上传数量超出限制！');
            },
            //附件预览
            pdfReview(file){
                if(file.name.indexOf('pdf')>0){
                    // pdf预览
                    let url = file.url.replace(this.common.commonUrl+'/file/','')
                    window.open(this.common.commonUrl+'/file/pdf/web/viewer.html?file=../../'+url)
                }else if(file.name.indexOf('docx')>0 || file.name.indexOf('doc')>0 || file.name.indexOf('ppt')>0 || file.name.indexOf('pptx')>0 || file.name.indexOf('xls')>0 ||file.name.indexOf('xlsx')>0){
                    // 文档预览
                    window.open('https://view.officeapps.live.com/op/view.aspx?src='+encodeURIComponent(file.url))
                    //window.open(' https://docs.google.com/viewer?url='+encodeURIComponent(file.url))
                    //window.open('http://api.yozocloud.cn/getPreview?k=54962500411169177711592&url='+encodeURIComponent(file.url))
                }else{
                    // 图片预览
                }
            },
            //附件移除
            pdfRemove(file,fileList){
                this.ruleForm.fileList = fileList
            },
            //附件移除前置方法
            delPdfFile(file,fileList){
                let that = this
                axios.post(that.common.commonUrl+'/api/deleteFile', {url:file.url}).then(res => {
                    if(res.data.state != "0000"){
                        that.$message.error(res.data.body[1].errmsg)
                        return false
                    }
                }).catch((error) => {
                    that.$message.error('删除PDF附件错误,请稍后重试!')
                    return false
                });
                return true;
            },


      /////////////////////////////////////////////////////////////////////
      ////////////////////////////////////////////////////////////////////



      // 关闭弹窗
	closeForm(formName){
		console.info(formName)
		this.partStyle = false
		this.flag = ''
		this.$refs[formName].resetFields();
	},
      submitForm(formName) {
		var that = this;
        this.$refs[formName].validate(async(valid) => {
          if (valid) {
			// 文章保存到数据库
			if(that.flag == 1){
				var res = await this.common.postData("/saveNews",{content:this.ruleForm});
			}else if(that.flag == 2){
				var res = await this.common.postData("/saveNews",{nid:this.nid,content:this.ruleForm});
			}
			if(res.data.state == "0000" ){
				var data  = {page:that.currentPage,limit:this.pageSize};
				that.$refs[formName].resetFields();
				that.partStyle = false;
				that.getNews(data);
				
				//this.ruleForm.content = res.data.body.content;
			}else{
				this.$message.error(res.data.body[1].errmsg)
			}
          } else {
            console.log('error submit!!');
            return false;
          }
        });
		this.importDoc =[];
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
	  //////////////////////////
	  // 以下为富文本编辑器的方法//
	  //////////////////////////
	  onEditorBlur () {
	    // 失去焦点事件
	  },
	  onEditorFocus () {
	    // 获得焦点事件
	  },
	  onEditorChange () {
	    // 内容改变事件
	  },
	 
	  //上传前回调
	  beforeAvatarUpload(file) {
	      var fileSize = file.size / 1024 / 1024 < 50;
	      if (['video/mp4'].indexOf(file.type) == -1) {
	          this.$message("请上传正确的视频格式");
	          return false;
	      }
	      if (!fileSize) {
	          this.$message("视频大小不能超过50MB");
	          return false;
	      }
	      // this.isShowUploadVideo = false;
	  },
	  //进度条
	  uploadVideoProcess(event, file, fileList) {
	      this.videoFlag = true;
	      this.videoUploadPercent = file.percentage.toFixed(0) * 1;
	  },
	  //上传成功回调
	  handleVideoSuccess(res, file) {
	      this.isShowUploadVideo = true;
	      this.videoFlag = false;
	      this.videoUploadPercent = 0;
	  },
	  //添加视频进页面
	  addVideo(){
		  this.dialogFnOpenUpload = false;
		  //将视频链接插入到当前的富文本当中
		  this.$refs.editer.quill.insertEmbed(index, 'video', this.videoLink);
	  },

	  /////////////////////////////////////////
	  /////////// 分页 ////////////////////////
	  async handleSizeChange(val){
			var that = this;
			var old_page = that.currentPage;
			var new_page = 0;
			var page_size = `${val}`;
			var count = this.tableCount;
			if(count%page_size === 0){
				new_page = count/page_size;
			}else{
				new_page = parseInt(count/page_size)+1;
			}
			if(old_page <= new_page){
				var  data = {
						page:old_page,
						limit:page_size,
					};
			   that.getNews(data);
			}else{
				that.pageSize = parseInt(page_size);
			}
		},
		//分页中页数进行切换时触发
		async handleCurrentChange(val) {
			let that = this
			var page_num = `${val}`;
			let data = {
					page:page_num,
					limit:this.pageSize
				};
			that.getNews(data);
			this.currentPage = parseInt(page_num);
		},
  
    },
  }
</script>

<style>
	.editor {
	  line-height: normal !important;
	  background:white;
	  height: 100%;
	  position: relative;
	}
	.ql-snow .ql-tooltip[data-mode=link]::before {
	  content: "请输入链接地址:";
	}
	.ql-snow .ql-tooltip.ql-editing a.ql-action::after {
	    border-right: 0px;
	    content: '保存';
	    padding-right: 0px;
	}
	
	.ql-snow .ql-tooltip[data-mode=video]::before {
	    content: "请输入视频地址:";
	}
	
	.ql-snow .ql-picker.ql-size .ql-picker-label::before,
	.ql-snow .ql-picker.ql-size .ql-picker-item::before {
	  content: '14px';
	}
	.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=small]::before,
	.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=small]::before {
	  content: '10px';
	}
	.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=large]::before,
	.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=large]::before {
	  content: '18px';
	}
	.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=huge]::before,
	.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=huge]::before {
	  content: '32px';
	}
	
	.ql-snow .ql-picker.ql-header .ql-picker-label::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item::before {
	  content: '文本';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="1"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="1"]::before {
	  content: '标题1';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="2"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="2"]::before {
	  content: '标题2';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="3"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="3"]::before {
	  content: '标题3';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="4"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="4"]::before {
	  content: '标题4';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="5"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="5"]::before {
	  content: '标题5';
	}
	.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="6"]::before,
	.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="6"]::before {
	  content: '标题6';
	}
	
	.ql-snow .ql-picker.ql-font .ql-picker-label::before,
	.ql-snow .ql-picker.ql-font .ql-picker-item::before {
	  content: '标准字体';
	}
	.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=serif]::before,
	.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=serif]::before {
	  content: '衬线字体';
	}
	.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=monospace]::before,
	.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=monospace]::before {
	  content: '等宽字体';
	}
	.ql-toolbar.ql-snow + .ql-container.ql-snow{
		height: calc(100% - 115px);
		position: absolute;
		bottom: 50px;
		left: 0;
		right: 0;
		border-width:0px;
	}
	.ql-editor{
		height: initial;
	}
	.ql-toolbar{
		position: fixed;
		width: calc(100% - 33rem);
		border: 0 !important;
		background: white;
		z-index: 9;
	}
	.ql-editor{
		padding: 0 15px;
		background: white;
		padding-bottom: 35px;
	}
</style>
