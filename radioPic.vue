<template>
    <my-radio class="filter_radioPic" v-model="currentValue" :options="options"
        :itemid="itemid" :required="required" :titleName="titleName" :titleBefore="titleBefore" :prop="prop"
        :disabled="disabled" :withForm="withForm" :picClick="true"
        @change="updateVal" 
        ref="formItem">

        <slot slot="unit" name="unit"></slot>
        <slot slot="description" name="description"></slot>

    </my-radio>
</template>

<script>
    import myRadio from './radio/withForm'

    export default {
        name: "radio",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*******************/
            value: {
                required: true
            },
            options: { 
                required: true
            }, //选项列表
            border: Boolean,
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value
            }
        },
        methods:{
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
        components:{
            myRadio
        },
        watch:{
            value:{
                handler(newVal, oldVal){
                    //console.log(newVal);
                    this.currentValue = newVal;
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_radioPic{
        .el-checkbox-group{
            height: 88px;

            //每一选项
            .filter_check_item {
                font-size: initial;
                padding: 1px;
                width: auto;
                height: auto;

                .el-checkbox__label{
                    padding-left: 0;
                } 
            }
        }
    }
</style>