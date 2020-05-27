<!--图片上传封装-->
<template>
    <div class="filter_picUpload_mult">
        <el-upload class="pic_uploader" :class="{'read_only': readOnly}"
            :disabled="disabled" action="string"
            list-type="picture-card"
            accept="image/jpeg,image/png,image/bmp"
            :file-list="fileList"
            :before-upload="beforeUpload"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :http-request="uploadPic">

            <i class="el-icon-plus pic_uploader_icon"></i>

            <!--提示-->
            <div slot="tip" class="upload_tip">只能上传jpg/png/bmp文件，且不超过15mb</div>
        </el-upload>

        <!--图片展示框-->
        <el-dialog :visible.sync="dialogVisible" :modal="false">
            <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>

    </div>
</template>

<script>
    export default {
        name: "picUploadMult",
        props:{
            value: { //已上传的文件（字符串）
                required: true
            },
            limit: Number, //最大上传数
            disabled: Boolean,
            valueType: String, //接收数据类型(String or Array),默认为Array
            readOnly: Boolean
        },
        data(){
            return {
                fileList: [],
                fileList_bak: [],
                connector: ",", //分隔符
                dialogImageUrl: "",
                dialogVisible: false, //用于展示图片的弹框
                handleShow: false //遮罩层显示状态
            }
        },
        methods:{
            //向父级传值
            updateVal(){
                this.bakFileList(); //备份
                let finalValue = this.fileList.map(o=>(o.url))

                if(this.valueType=="string"){
                    finalValue = finalValue.join(this.connector);
                }
                //console.log(finalValue);

                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
            },
            //上传前验证
            beforeUpload(file){
                console.log(file);

                const typeChecked = (file.type==='image/jpeg') || (file.type==='image/png') || (file.type==='image/bmp');
                const sizeChecked = file.size / 1024 / 1024 < 15;

                if (!typeChecked) {
                    this.$message.error('上传图片格式不符合要求！');
                    this.$emit("error", "上传图片格式不符合要求！");
                    return false;
                }
                if (!sizeChecked) {
                    this.$message.error('上传图片大小不符合要求！');
                    this.$emit("error", "上传图片大小不符合要求！");
                    return false;
                }

                return true;
            },
            //调用接口上传图片
            uploadPic(item){
                //console.log(item);
                this.handleShow = false;

                let formData = new FormData();
                formData.append('file', item.file);
                //console.log(formData);

                this.$dataPostFormdata("/files/images", formData, (data, all)=>{
                    this.fileList.push({
                        name: item.file.name, //文件名
                        url: data //图片网络路径
                    })
                    this.updateVal();

                }, (error)=>{
                    
                });
            },
            //放大图片
            handlePreview(file){
                this.dialogImageUrl = file.url;
                this.dialogVisible = true;
            },
            //删除图片
            handleRemove(file, fileList) {
                //console.log(file, fileList);
                this.fileList = fileList;
                this.updateVal();
            },
            //备份文件列表
            bakFileList(){
                let fileList = this.fileList.map(o=>({name:o.name, url:o.url}));
                this.fileList_bak = this.$deepCopy(fileList); //备份
            }
            
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    let tempFileList = null;
                    if(this.valueType=="string"){
                        tempFileList = newVal? newVal.split(this.connector): [];
                    }else{
                        tempFileList = newVal || [];
                    }
                    tempFileList = tempFileList.map((val,i)=>({name:i.toString(), url:val})); //格式转换

                    //console.log(JSON.stringify(this.fileList_bak), JSON.stringify(tempFileList));
                    if(this.fileList_bak.length != tempFileList.length){
                        this.fileList = tempFileList;
                        this.bakFileList(); //备份
                    }
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_picUpload_mult{
        $picWidth: 120px;
        $picHeight: 120px;

        .pic_uploader .el-upload {
            position: relative;
            border: 1px dashed #d9d9d9;
            border-radius: 6px;
            cursor: pointer;
            overflow: hidden;
            //重置长宽
            width: $picWidth;
            height: $picHeight;
            line-height: $picHeight;
        }
        .pic_uploader .el-upload:hover {
            border-color: #409EFF;
            .avatar_delete_icon{
                visibility: visible;
            }
        }
        //重置列表长宽
        .pic_uploader .el-upload-list__item{
            width: $picWidth;
            height: $picHeight;
        }

        //提示
        .upload_tip {
            font-size: 12px;
            margin-top: 8px;
            height: 20px;
            line-height: 20px;
        }

        //只读状态
        .read_only{
            .el-upload{
                display: none;
            }
            .upload_tip{
                display: none;
            }
            .el-upload-list__item-delete{
                display: none !important;
            }
            .el-upload-list__item-status-label{
                display: none !important;
            }
        }
    }
</style>