<template>
    <div class="form_item_radio" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required: required, message:titleName+'必填', trigger:['change']}
                ]" ref="formItem">

                <div>
                    <radios v-model="currentValue" :options="options" :props="props"
                        :disabled="disabled" :border="border" :picClick="picClick"
                        @change="updateVal">
                    </radios>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>
            </form-item>
        </template>

        <template v-else>
            <radios v-model="currentValue" :options="options" :props="props"
                :disabled="disabled" :border="border" :picClick="picClick"
                @change="updateVal">
            </radios>
        </template>
    </div>
</template>

<script>
    import formItem from '../formItem';
    import radios from './radio';

    export default {
        name: "radioForm",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*********************/
            value: {
                required: true
            },
            options: { //选项列表
                required: true
            },
            border: Boolean,
            props: Object, //配置选项
            disabled: Boolean,
            picClick: Boolean, //是否图片点击
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
                    this.currentValue = newVal || "";
                }
            }
        },
        components:{
            formItem, radios
        }
	}
</script>

<style lang="scss" type="text/css">
    // .form_item_radio{
    //     position: relative;
    // }
</style>