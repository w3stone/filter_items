<!--select封装-->
<template>
    <el-form-item class="form_item" :prop="prop" 
        :rules="rules" ref="formItem">

        <template v-if="titleName">
            <h4 class="label_title" slot="label" v-html="(titleBefore || '') + titleName"><span>*</span></h4>
        </template>

        <slot></slot>

    </el-form-item>
</template>

<script>
    export default {
        name: "formItem",
        props:{
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            rules: Array //表单验证规则
        },
        mounted(){
            this.clearValidate();
        },
        methods:{
            clearValidate(){
                this.$nextTick(()=>{
                    //console.log("清空");
                    if(this.$refs.formItem){
                        this.$refs.formItem.clearValidate();
                    }
                })
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item{
        .el-form-item__label{ //标题
            text-align: left;

            .label_title{
                display: inline;
                font-weight: normal;
                position: relative;
                span{
                    display: none; //默认隐藏
                    color: #F56C6C;
                    //margin-left: 5px;
                }
            }
        }
        &.is-required{ //必填
            .el-form-item__label{
                &::before{
                    display: none;
                }
                .label_title{
                    span{
                        display: inline;
                        margin-left: 4px;
                    }
                }
            }
        }
    }
</style>