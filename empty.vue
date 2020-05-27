<!--空组件封装-->
<template>
    <div class="form_item_empty">
        <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
            :rules="[{required:false}]" ref="formItem">
        </form-item>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "empty",
        props:{
            itemid: Number,
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String //对象名
        },
        components:{
            formItem 
        },
        methods:{
            //清空验证(调用子组件方法)
            clearValidate(){
                this.$nextTick(()=>{
                    this.$refs.formItem.clearValidate();
                })
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_empty{
        .form_item{
            padding-top: 2px !important;
            padding-bottom: 2px !important;

            label{
                width: 100% !important;
            }
        }
    }
</style>