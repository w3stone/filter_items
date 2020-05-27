<template>
    <div class="form_item_numRange">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']},
                    {validator:verifyFromResult, trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <div class="filter_numRange">
                        <el-input type="number" class="range_input" v-model="currentValueBefore" @blur="updateVal(currentValueBefore)"
                            placeholder="请输入最小值" :disabled="disabled" />
                        
                        <span class="range_split">{{split}}</span>

                        <el-input type="number"  class="range_input" v-model="currentValueAfter" @blur="updateVal(currentValueAfter)"
                            placeholder="请输入最大值" :disabled="disabled" />
                    </div>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <div class="filter_numRange">
                <el-input type="number" class="range_input" v-model="currentValueBefore" @blur="updateVal(currentValueBefore)"
                    placeholder="请输入" :disabled="disabled" />
                
                <span class="range_split">{{split}}</span>

                <el-input type="number"  class="range_input" v-model="currentValueAfter" @blur="updateVal(currentValueAfter)"
                    placeholder="请输入" :disabled="disabled" />
            </div>
        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "numrRange",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*************************/
            value: {
                required: true,
                //type: String
            },
            split: {
                required: true,
                type: String
            },
            range: String,
            disabled: Boolean
        },
        data(){
            return {
                currentValueBefore: 0,
                currentValueAfter: 0
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
            }
        },
        methods:{
            str2Value(numStr){ //字符转数字
                return (typeof this.$string2Num(numStr)=='number')? parseFloat(numStr): "";
            },
            makeCurrentValue(value){ //设置两个值
                value = value || "";
                let list = value.split(this.split);
                //console.log(list);
                this.currentValueBefore = this.str2Value(list[0]);
                this.currentValueAfter = this.str2Value(list[1]);
            },
            updateVal(value){ //
                let finalValue = ""; //最终返回值

                if(this.currentValueBefore!="" || this.currentValueAfter!=""){
                    finalValue = this.currentValueBefore + this.split + this.currentValueAfter;
                }

                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
            },
            //校验
            verifyFromResult(rule, value, callback){
                let currentValueBefore = this.currentValueBefore;
                let currentValueAfter = this.currentValueAfter;

                let minValue = this.minValue;
                let maxValue = this.maxValue;

                if(currentValueBefore==="" && currentValueAfter===""){ //都不填
                    callback();
                }

                if(currentValueBefore==="" || currentValueAfter===""){ //有一项未填
                    callback("两项都必填或不填");
                }

                currentValueBefore = parseFloat(currentValueBefore);
                currentValueAfter = parseFloat(currentValueAfter);

                if(currentValueAfter<currentValueBefore){
                    callback("后值不能小于前值");
                }    

                if(minValue!="" && maxValue!=""){ //有最大最小值限制
                    if( (currentValueBefore<minValue) || (currentValueAfter>maxValue) ){
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
        components:{
            formItem
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.makeCurrentValue(newVal);
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_numRange{
        display: inline-block;

        .range_input{
            max-width: 160px;
        }
        .range_split{
            margin: 0 4px; 
        }
    }
</style>