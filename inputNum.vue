<!--input计数器控件-->
<template>
    <div class="form_item_inputNum" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']},
                    {validator: verifyFromResult, trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-input class="filter_inputNum" type="number" v-model="currentValue" :placeholder="placeholder" :step="step"
                        :style="{'width':width}"
                        @change="updateVal" :disabled="disabled">
                    </el-input>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-input class="filter_inputNum" type="number" v-model="currentValue" :placeholder="placeholder" :step="step"
                :style="{'width':width}"
                @change="updateVal" :disabled="disabled">
            </el-input>
        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "inputNum",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /**************/
            value: {
                required: true
            },
            range: String, //滑块范围
            width: String, //宽度
            placeholder: String,
            notBlank: Boolean,
            disabled: Boolean,
            type: String //整数还小数
        },
        data(){
            return {
                currentValue: this.value
            }
        },
        computed:{
            rangeStr(){
                return this.range || "";
            },
            minValue(){
                let min = this.rangeStr.split(",")[0];
                return this.str2Value(min);
            },
            maxValue(){
                let max = this.rangeStr.split(",")[1];
                return this.str2Value(max);
            },
            fixed(){
                let fixed = this.$string2Num(this.type); //默认保留两位小数
                if(!fixed && fixed!=0) fixed=2; //默认赋值2
                return fixed;
            },
            step(){
                if(this.type=="0") return 1;
                if(this.type=="1") return 0.1;
                return 0.01;
            }
        },
        methods:{
            str2Value(numStr){ //字符转数字
                return (typeof this.$string2Num(numStr)=='number')? parseFloat(numStr): "";
            },
            makeValue(value){
                value = this.$string2Num(value);
                if(typeof value=='number'){
                    //this.updateVal(value.toString());
                    this.currentValue = value;
                }else{
                    this.currentValue = ""; //默认为空
                }
            },
            //验证是否在范围内
            checkRange(value){
                if(this.range){
                    if( !(value>=this.minNum && value<=this.maxNum) ) return false;
                }
                return true;
            },
            updateVal(value){
                let valueStr = value.toString();
                //console.log(valueStr);

                if(valueStr.length>10){ //长度超过10截取
                    value = parseFloat(value.toString().slice(0,10));
                }else if(valueStr!=""){
                    value = parseFloat(value);
                }
                
                if(value!=""){
                    this.currentValue = this.fixed!=0? parseFloat(value.toFixed(this.fixed)): parseInt(value);
                }
                
                this.$emit('input', value);
                this.$emit('change', value);
            },
            setPrecision(){ //弃用
                return parseInt(this.currentValue)==this.currentValue? undefined: 2; //整数精度为0, 小数精度为2
            },
            //校验
            verifyFromResult(rule, value, callback){
                if(value!=""){
                    value = parseFloat(value);
                }

                let minValue = this.minValue;
                let maxValue = this.maxValue;

                if(minValue!=="" && maxValue!==""){ //有最大最小值限制
                    if(value>=minValue && value<=maxValue){
                        callback();
                    }else{
                        callback("取值不在范围内");
                    }
                }

                setTimeout(() => { //1秒后清空
                    callback();
                }, 1000);
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
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_inputNum{
        .el-input {
            width: 220px; //设置默认宽度
        }
    }
</style>