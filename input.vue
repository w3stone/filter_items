<!--input封装-->
<template>
    <div class="form_item_input" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']},
                    {validator: verifyFromResult, trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-input class="filter_input" v-model="currentValue" type="text" :placeholder="placeholder"
                        :style="{'width':width}" :maxlength="maxlength || 100"
                        @blur="updateVal(currentValue)" @clear="updateVal(currentValue)"
                        @keydown.enter.native.prevent="updateVal(currentValue)"
                        :disabled="disabled" clearable>
                    </el-input>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-input class="filter_input" v-model="currentValue" type="text" :placeholder="placeholder"
                :style="{'width':width}" :maxlength="maxlength || 100"
                @blur="updateVal(currentValue)" @clear="updateVal(currentValue)"
                @keydown.enter.native.prevent="updateVal(currentValue)"
                :disabled="disabled" clearable>
            </el-input>

        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "inputs",
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
            width: String, //宽度
            placeholder: String,
            validate: String, //校验规则(正则)
            disabled: Boolean,
            maxlength: Number //最大长度
        },
        data(){
            return {
                currentValue: this.value,
                currentValue_bak: "" //备份当前值
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                value = value || "";
                //if(value===this.currentValue_bak) return false;

                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            //校验
            verifyFromResult(rule, value, callback){
                let reg = this.validate? eval(this.validate): null;

                if(reg && !reg.test(value)){
                    callback("请输入正确格式!"); //特殊校验
                }
                callback();

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
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                    this.currentValue_bak = newVal; //备份
                }
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_input{
        .filter_input{
            width: 220px; //设置默认宽度
        }
    }
</style>