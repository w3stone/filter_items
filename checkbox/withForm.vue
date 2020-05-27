<!--select封装-->
<template>
    <div class="form_item_checkbox" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['change']},
                    {validator:verifyFromResult, trigger:['change']}
                ]" ref="formItem">

                <div>
                    <checkboxs v-model="currentValue" :options="options" :max="max"
                        :props="props" :indeterminate="indeterminate" :border="border" :valueType="valueType"
                        :disabled="disabled" @change="updateVal">
                    </checkboxs>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>
            </form-item>
        </template>

        <template v-else>
            <checkboxs v-model="currentValue" :options="options" :max="max"
                :props="props" :indeterminate="indeterminate" :border="border" :valueType="valueType"
                :disabled="disabled" @change="updateVal">
            </checkboxs>
        </template>
    </div>
</template>

<script>
    import formItem from '../formItem';
    import checkboxs from './checkbox';


    export default {
        name: "checkboxsForm",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /**********************/
            value: {
                required: true
            },
            options: { //选项列表
                required: true,
                type: Array
            },
            max: Number, //最大可选数量
            props: Object, //配置选项
            indeterminate: Boolean, //是否包含全选
            valueType: String, //接收数据类型(String or Array),默认为Array
            border: Boolean, //是否含有边框
            disabled: Boolean
        },
        data(){
            return {
                currentValue: null
            }
        },
        methods:{
            //向父级传值
            updateVal(newVal, oldVal){
                //console.log(value);
                this.clearValidate(); //???存在验证bug

                //触发事件，并向父级传入新值
                this.$emit('input', newVal);
                this.$emit('change', newVal, oldVal);
            },
            //清空验证(调用子组件方法)
            clearValidate(){
                this.$nextTick(()=>{
                    this.$refs.formItem.clearValidate();
                })
            },
            //校验
            verifyFromResult(rule, value, callback){
                //console.log(rule, value);
                callback();
            }
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || [];
                }
            }
        },
        components:{
            formItem, checkboxs
        }
	}
</script>

<style lang="scss" type="text/css">
    // .form_item_select{
    //     display: inline-block;
    // }
</style>