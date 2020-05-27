<!--select封装-->
<template>
    <div class="form_item_select" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['change']}
                ]" ref="formItem">

                <div>
                    <selects v-model="currentValue" :options="options" :placeholder="placeholder"
                        :disabled="disabled" :multiple="multiple" :valueType="valueType" :props="props" :notClearable="notClearable"
                        :allowCreate="allowCreate" :style="{'width':width}"
                        @change="updateVal">
                    </selects>

                    <slot name="other"></slot>
                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>
            </form-item>
        </template>

        <template v-else>
            <selects v-model="currentValue" :options="options" :placeholder="placeholder"
                :disabled="disabled" :multiple="multiple" :valueType="valueType" :props="props" :notClearable="notClearable"
                :allowCreate="allowCreate" :style="{'width':width}"
                @change="updateVal">
            </selects>
        </template>
    </div>
</template>

<script>
    import formItem from '../formItem';
    import selects from './select';
import { networkInterfaces } from 'os';

    export default {
        name: "selectsForm",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /**********************/
            width: String, //宽度
            /*********************/
            value: {
                required: true
            },
            options: { //选项列表
                required: true,
                type: Array
            },
            placeholder: String,
            multiple: Boolean, //是否多选
            valueType: String, //接收数据类型(String or Array),默认为Array
            props: Object, //配置选项
            notClearable: Boolean, //是否可清除
            allowCreate: Boolean, //是否允许添加
            loading: Boolean, //是否正在获取选项
            disabled: Boolean //禁用状态
        },
        data(){
            return {
                currentValue: ""
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            //清空验证(调用子组件方法)
            clearValidate(){
                this.$nextTick(()=>{
                    this.$refs.formItem.clearValidate();
                })
            }
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = (newVal || newVal===0)? newVal: "";
                }
            }
        },
        components:{
            formItem, selects
        }
	}
</script>

<style lang="scss" type="text/css">
    // .form_item_select{
    //     display: inline-block;
    // }
</style>