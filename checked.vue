<!--勾选空间封装-->
<template>
    <div class="form_item_checked" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-checkbox v-model="currentValue" :disabled="disabled" @change="updateVal">{{label}}</el-checkbox>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-checkbox v-model="currentValue" :disabled="disabled" @change="updateVal">{{label}}</el-checkbox>
        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "checked",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*******************************/
            value: {
                required: true
            },
            label: String,
            props: Object, //配置选项
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                defaultProps: {
                    true: true,
                    false: false
                }
            }
        },
        computed:{
            finalProps(){
                return this.props || this.defaultProps;
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                let finalValue = value? this.finalProps.true: this.finalProps.false;

                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
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
                    this.currentValue = (newVal===this.finalProps.true? true: false);
                }
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    // .form_item_checked{
    //     display: inline-block;      
    // }
</style>