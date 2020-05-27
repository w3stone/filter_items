<!--多个input-->
<template>
    <div class="form_item_multInput">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']},
                    {validator: verifyFromResult, trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <div class="filter_each_input" v-for="(item, index) in valueArr" :key="index">
                        <el-input class="filter_input" v-model="valueArr[index]" :placeholder="placeholder" 
                            @blur="updateVal(valueArr[index])" @clear="updateVal(valueArr[index])"
                            :disabled="disabled" clearable>
                        </el-input>
                        <span class="filter_split" v-if="index!=valueArr.length-1">{{split}}</span>
                    </div>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <div class="filter_each_input" v-for="(item, index) in valueArr" :key="index">
                <el-input class="filter_input" v-model="valueArr[index]" :placeholder="placeholder" 
                    @blur="updateVal(valueArr[index])" @clear="updateVal(valueArr[index])"
                    :disabled="disabled" clearable>
                </el-input>
                <span class="filter_split" v-if="index!=valueArr.length-1">{{split}}</span>
            </div>
        </template>

    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "multInput",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*******************/
            value: {
                required: true,
                //type: String
            },
            split: { //分隔符
                required: true,
                type: String
            },
            inputNum: Number,
            requiredNum: String,
            placeholder: String,
            validate: String, //校验规则(正则)
            disabled: Boolean
        },
        data(){
            return {
                valueArr: [],
                f_requiredNum: 0
            }
        },
        methods:{
            makeCurrentValue(value){
                value = value || "";
                //初始化
                this.valueArr = value.split(this.split);
                if(this.valueArr.length<this.inputNum){ //补充空字符
                    //this.updateVal(""); //发送错误信息
                    for(var i=0; i<=this.inputNum-this.valueArr.length; i++){ //?
                        this.valueArr.push("");
                    }
                }
            },
            //验证是否有空值
            checkValue(){
                let result = true;
                this.valueArr.some(val=>{
                    if(val=="") result=false;
                })
                return result;
            },
            //向父级传值
            updateVal(value){
                let finalValue = this.valueArr.join(this.split);

                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
            },
            //校验
            verifyFromResult(rule, value, callback){
                //console.log(rule, value);
                let reg = this.validate? eval(this.validate): null;
                let hasValueNum = 0;
                let prompt = "";
                
                this.valueArr.forEach(val=>{
                    //判断是否都填
                    if(val) {
                        hasValueNum++;
                        if(reg && !reg.test(val)) prompt = "请输入正确格式!"; //特殊校验
                    }
                });

                if(hasValueNum>0 && hasValueNum<this.f_requiredNum){
                    prompt="至少" + this.f_requiredNum + "项必填!";
                }

                if(!prompt){
                    callback();
                }else{
                    callback(prompt)
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
        mounted(){
            this.f_requiredNum = this.requiredNum? parseInt(this.requiredNum): this.inputNum;
            this.makeCurrentValue(this.value);
        },
        watch:{
            value:{
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.makeCurrentValue(newVal);
                }
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_multInput{
        
        .filter_each_input{
            display: inline-block;
            
            .filter_input{
                width: $inputWidth;
            }
            .filter_split{
                display: inline-block;
                margin: 0 5px;
            }
        }
    }
</style>