<!--日期控件(返回xxxx年xx月xx日)-->
<template>
    <div class="form_item_date" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur','change']}
                ]" ref="formItem">

                <div>
                    <el-date-picker class="filter_date" v-model="currentValue" type="date" 
                        :placeholder="placeholder? placeholder: '选择日期'" 
                        format="yyyy-MM-dd" :value-format="finalValueFormat" :picker-options="pickerOptions" 
                        :disabled="disabled" @change="updateVal">
                    </el-date-picker>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-date-picker class="filter_date" v-model="currentValue" type="date"
                :placeholder="placeholder? placeholder: '选择日期'" 
                format="yyyy-MM-dd" :value-format="finalValueFormat" :picker-options="pickerOptions" 
                :disabled="disabled" @change="updateVal">
            </el-date-picker>
        </template>

    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "dates",
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
            placeholder: String,
            range: String, //可选日期范围
            valueFormat: String, //日期数据格式
            ifNowDate: String, //是否默认当前日期
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                startDate: 0,
                endDate: 0,
                pickerOptions: {
                    disabledDate: (time) => {
                        if(this.range){
                            let dateArr = eval(this.range);
                            let currentDate = time.getTime();
                            this.startDate = this.startDate || this.$dateToTimestamp(dateArr[0]);
                            this.endDate = this.endDate || this.$dateToTimestamp(dateArr[1]);
                            return currentDate<this.startDate || currentDate>this.endDate;
                        }
                    }
                }
            }
        },
        computed:{
            finalValueFormat(){
                return this.valueFormat || "yyyy-MM-dd";
            }
        },
        mounted(){
            //设定默认值为当前日期
            if(this.ifNowDate=="nowDate" && !this.value){
                this.currentValue = this.$setDate(new Date());
                this.updateVal(this.currentValue);
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                value = value || "";
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
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    // .form_item_date{
    //     display: inline-block;
    // }
</style>