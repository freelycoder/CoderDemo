<template>
  <div class="myvideo myvideo_zs">
    	<el-upload
	   class="wenzhang_upload"
	   :action="uploadUrl"
	   accept=".jpg,.jpeg,.png,.gif"
	   :file-list="ruleForm.imgList"
	   :http-request="uploadIMG">
	<el-button slot="trigger" class="sel1 fr daoru" type="warning">上传附件</el-button>
	</el-upload>
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

	uploadUrl:'/uploadVideo',
	importDocUrl:'/importDocUrl',
        imgList:[],
	active:0,//导航切换的样式
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
	}
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
