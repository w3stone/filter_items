<!--签名控件封装-->
<template>
    <div class="filter_signature">
        <template v-if="currentValue">
            <div class="signature_img_box" style="width:150px;" @click="beginSignature()">
                <img :src="currentValue" />
            </div>
        </template>
        <template v-else>
            <el-button @click="beginSignature()" :disabled="disabled">点击签名</el-button>
        </template>
        

        <!--签名框-->
        <el-dialog :visible.sync="dialogVisible" :modal="false">
            <signature :visible.sync="dialogVisible" @submitted="submitted"></signature>
        </el-dialog>

    </div>
</template>

<script>
    import signature from './signature';

    export default {
        name: "filterSignature",
        props:{
            value: {
                required: true
            },
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                dialogVisible: false, //用于展示图片的弹框
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            //开始签名
            beginSignature(){
                this.dialogVisible = true;
            },
            //后台已经提交
            submitted(imgUrl){
                this.currentValue = imgUrl;
                this.updateVal(imgUrl);
            }
        },
        components:{
            signature
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_signature{
        
    }
</style>