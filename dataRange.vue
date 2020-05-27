<!--日期范围控件-->
<template>
    <div class="form_item_date">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur','change']}
                ]" ref="formItem">

                <div>
                    <el-date-picker v-model="currentValue" type="daterange"
                        :range-separator="split" 
                        :start-placeholder="'开始' + (placeholder? placeholder: '日期')" 
                        :end-placeholder="'结束' + (placeholder? placeholder: '日期')"
                        format="yyyy-MM-dd" :value-format="finalValueFormat" 
                        unlink-panels :picker-options="[]" 
                        :disabled="disabled" @change="updateVal">
                    </el-date-picker>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-date-picker v-model="currentValue" type="daterange"
                :range-separator="split"
                :start-placeholder="'开始' + (placeholder? placeholder: '日期')" 
                :end-placeholder="'结束' + (placeholder? placeholder: '日期')"
                format="yyyy-MM-dd" :value-format="finalValueFormat" 
                unlink-panels :picker-options="[]" 
                :disabled="disabled" @change="updateVal">
            </el-date-picker>
        </template>

    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "dateRange",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String, //对象名
            /**************/
            value: {
                required: true
            },
            split: { //分隔符
                required: true,
                type: String
            },
            valueFormat: String, //日期数据格式
            placeholder: String,
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value
            }
        },
        computed:{
            finalValueFormat(){
                return this.valueFormat || "yyyy-MM-dd";
            }
        },
        mounted(){
            this.makeValue(this.value);
        },
        methods:{
            makeValue(value){
                if(value){
                    this.currentValue = value.split(this.split); //转回数组
                }else{
                    this.currentValue = [];
                }
            },
            //向父级传值
            updateVal(value){
                let finalValue = "";
                if(value){
                    finalValue = value.join(this.split); //转换成字符
                }
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
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.makeValue(newVal);
                }
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    
</style>