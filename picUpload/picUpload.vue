<!--图片上传封装-->
<template>
    <div class="filter_picUpload">
        <el-upload class="avatar_uploader" 
            :multiple="false" :disabled="disabled" action="string"
            accept="image/jpeg,image/png,image/bmp"
            :before-upload="beforeUpload"
            :http-request="uploadPic"
            :show-file-list="false">

            <span v-if="imageUrl" 
                @mouseenter.stop="handleShow=true">
                <img class="avatar_pic" :src="imageUrl">
            </span>

            <i v-else class="el-icon-plus avatar_uploader_icon"></i>
            
            <!--遮罩层-->
            <span class="avatar_modal center" :class="{'modal_disabled':disabled}" 
                v-show="(handleShow && imageUrl) || disabled"
                @mouseleave.stop="handleShow=false"
                @click.stop="()=>{}">

                <i class="handle_icon el-icon-zoom-in" @click.stop="zoomPic()" v-show="imageUrl"></i>
                <i class="handle_icon el-icon-delete" @click.stop="deletePic()" v-if="!disabled"></i>
            </span>

            <!--提示-->
            <div slot="tip" class="upload_tip">只能上传jpg/png/bmp文件，且不超过15mb</div>
        </el-upload>

        <!--图片展示框-->
        <el-dialog :visible.sync="dialogVisible" :modal="false">
            <img width="100%" :src="imageUrl" alt="">
        </el-dialog>

    </div>
</template>

<script>
    export default {
        name: "picUpload",
        props:{
            value: {
                required: true
            },
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                currentValue_bak: "", //备份原图片路径
                imageUrl: "",
                errorContent: "", //错误提示
                dialogVisible: false, //用于展示图片的弹框
                handleShow: false //遮罩层显示状态
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
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
                console.log(item);
                //this.imageUrl = URL.createObjectURL(item.file);
                this.handleShow = false;

                let formData = new FormData();
                formData.append('file', item.file);

                this.$dataPostFormdata("/files/images", formData, (data, all)=>{
                    let picWebUrl = data;
                    this.imageUrl = picWebUrl;
                    this.currentValue = picWebUrl;
                    this.updateVal(this.currentValue); //向父级传值

                }, (error)=>{
                    this.currentValue = this.currentValue_bak; //还原
                });
            },
            //前端删除图片
            deletePic(){
                //清空
                this.imageUrl = ""; 
                this.currentValue = "";
                this.updateVal(""); //向父级传值
            },
            //放大图片
            zoomPic(){
                this.dialogVisible = true;
                this.handleShow = false;
            }
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                    this.currentValue_bak = this.currentValue;
                    if(this.currentValue) this.imageUrl = this.currentValue; //图片路径赋值
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_picUpload{
        $picWidth: 180px;
        $picHeight: 180px;

        .avatar_uploader .el-upload {
            position: relative;
            border: 1px dashed #d9d9d9;
            border-radius: 6px;
            cursor: pointer;
            overflow: hidden;
        }
        .avatar_uploader .el-upload:hover {
            border-color: #409EFF;
            .avatar_delete_icon{
                visibility: visible;
            }
        }
        .avatar_uploader_icon {
            font-size: 28px;
            color: #8c939d;
            width: 178px;
            height: 178px;
            line-height: 178px;
            text-align: center;
        }
        .avatar_pic {
            width: 178px;
            height: 178px;
            display: block;
        }
        //遮罩层
        .avatar_modal{
            width: $picWidth;
            height: $picHeight;
            background-color: rgba(0,0,0,0.4);
            //z-index: 99;
            .handle_icon{
                color: #fff;
                font-size: 24px;
                margin: 0 10px;
                line-height: $picHeight;
                cursor: pointer;
            }
            &.modal_disabled{
                cursor: not-allowed;
            }
        }
        //提示
        .upload_tip {
            font-size: 12px;
            margin-top: -8px;
            height: 20px;
            line-height: 20px;
        }
    }
</style>