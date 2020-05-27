<!--select封装-->
<template>
    <my-select class="form_item_logmar" v-model="currentValue" :options="currentOptions"
        :itemid="itemid" :required="required" :titleName="titleName" :titleBefore="titleBefore" :prop="prop"
        :disabled="disabled" :withForm="withForm" @change="updateVal"
        ref="formItem">

        <div class="logmar_box" slot="other" v-show="logmar">
            LogMAR: <span>{{logmar}}</span>
        </div>

        <slot slot="unit" name="unit"></slot>
        <slot slot="description" name="description"></slot>
    </my-select>
</template>

<script>
    import mySelect from '../select/withForm';

    export default {
        name: "special_logmar",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /********************************/
            value: {
                required: true
            },
            options: { //选项列表
                required: true,
                type: Array
            }, 
            disabled: Boolean, //禁用状态
            placeholder: String,
            notClearable: Boolean //是否可清除
        },
        data(){
            return {
                currentValue: "",
                currentOptions: []
            }
        },
        computed:{
            logmar(){
                return this.currentValue!=""? this.currentValue.split(",")[1]: ""
            }
        },
        mounted(){
            this.makeValue(this.value);
        },
        methods:{
            makeValue(value){
                this.currentValue = value;
            },
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
                    this.makeValue(newVal);
                }
            },
            options:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentOptions = newVal;
                }
            }
        },
        components:{
            mySelect
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_logmar{

        .logmar_box{
            display: inline-block;
            box-sizing: content-box;
            border: 1px solid #DCDFE6;
            height: 38px;
            line-height: 38px;
            margin-left: 4px;
            padding: 0 8px;
            border-radius: 4px;
        }
    }
</style>