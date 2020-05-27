<!--签名控件封装-->
<template>
    <div class="form_item_signature">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['change']},
                    {validator:verifyFromResult, trigger:['change']}
                ]" ref="formItem">

                <div>
                    <signature v-model="currentValue" :disabled="disabled"
                        @change="updateVal">
                    </signature>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>
            </form-item>
        </template>

        <template v-else>
            <signature v-model="currentValue" :disabled="disabled"
                @change="updateVal">
            </signature>
        </template>

    </div>
</template>

<script>
    import formItem from '../formItem';
    import signature from './frame';

    export default {
        name: "signatureForm",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /***************************/
            value: {
                required: true
            },
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            //校验
            verifyFromResult(rule, value, callback){
                // console.log(rule, value);
                // console.log(this.errorContent);
                callback();
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
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                }
            }
        },
        components:{
            formItem, signature
        }
	}
</script>

<style lang="scss" type="text/css">
    
</style>