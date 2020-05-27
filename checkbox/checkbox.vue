<!--checkbox封装-->
<template>
    <div class="filter_checkbox">
        <template v-if="indeterminate">
            <el-checkbox class="filter_checkbox_indeterminate" :indeterminate="isIndeterminate" v-model="checkAll" 
                @change="handleCheckAllChange">全选
            </el-checkbox>
        </template>

        <el-checkbox-group v-model="currentValue" @change="handleCheckedChange"
            :min="finalMin" :max="finalMax" :disabled="disabled">
            <el-checkbox class="filter_checkbox_item" v-for="(item,index) in currentOptions" :key="index" 
                :label="item[finalProps.value]" :border="border">{{item[finalProps.label]}}
            </el-checkbox>
        </el-checkbox-group>
    </div>
</template>

<script>
    export default {
        name: "checkboxs",
        props:{
            value: {
                required: true
            },
            options: { //选项列表
                required: true,
                type: Array
            },
            max: Number, //最大可选数量
            props: Object, //配置选项
            indeterminate: Boolean, //是否包含全选
            valueType: String, //接收数据类型(String or Array),默认为Array
            border: Boolean, //是否含有边框
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                currentValue_bak: "",
                currentOptions: this.options,
                defaultProps: {
                    label: 'title',
                    value: 'value'
                },
                checkAll: false, //是否全选
                isIndeterminate: false,
                connector: "," //value分隔符
            }
        },
        computed:{
            finalProps(){
                return this.props || this.defaultProps;
            },
            finalMin(){
                return this.min || undefined;
            },
            finalMax(){
                return this.max || undefined;
            }
        },
        methods:{
            makeValue(value){
                if(this.valueType=='string'){
                    value = value || "";
                    this.currentValue = (typeof value=="string" && value)? value.split(this.connector): [];

                }else{
                    this.currentValue = value || [];
                }
            },
            //向父级传值
            updateVal(value){
                let finalValue = null;
                if(this.valueType=='string'){ //模拟多选
                    finalValue = value.length>0? value.join(this.connector): "";
                    
                }else{
                    finalValue = value;
                }
                //console.log("finalValue", finalValue);
                let oldValue = this.currentValue_bak;

                this.$emit('input', finalValue);
                this.$emit('change', finalValue, oldValue);
                //
                this.currentValue_bak = finalValue;
            },
            //点击全选半选按钮
            handleCheckAllChange(val) {
                //console.log(val);
                if(val){ //全选
                    this.currentValue = this.currentOptions.map(o=>o[this.finalProps.value]);

                }else{ //全不选
                    this.currentValue = [];
                }
                this.isIndeterminate = false;
                this.updateVal(this.currentValue);
            },
            //选项发生变化
            handleCheckedChange(value) {
                //console.log(value);
                this.updateVal(value);

                if(this.indeterminate){
                    let checkedCount = value.length;
                    this.checkAll = (checkedCount===this.currentOptions.length);
                    this.isIndeterminate = checkedCount>0 && checkedCount<this.currentOptions.length;
                }
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
                    this.currentOptions = newVal || [];
                    if(this.indeterminate){
                        let checkedCount = this.currentValue.length;
                        this.checkAll = (checkedCount===this.currentOptions.length);
                        this.isIndeterminate = checkedCount>0 && checkedCount<this.currentOptions.length;
                    }
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css"> 
    .filter_checkbox{
        //样式覆盖
        .el-checkbox__input.is-checked .el-checkbox__inner, 
        .el-checkbox__input.is-indeterminate .el-checkbox__inner{
            background-color: $activeColor;
            border-color: $activeColor;
        }
        //全选半选
        .filter_checkbox_indeterminate{
            .el-checkbox__inner:hover{
                border-color: $activeColor;
            }
            .is-checked+.el-checkbox__label{
                color: $activeColor;
            }
        }
        //
        .filter_checkbox_item{
            &.is-bordered{
                background-color: #fff;
                margin: 0 !important;
                margin-right: 4px !important;
                margin-bottom: 4px !important;

                .el-checkbox__inner{
                    display: none;
                }
            }
            &.is-checked{
                background-color: $activeColor;
                border-color: $activeColor;
                .el-checkbox__label{
                    color: #fff;
                }
            }
        }
    }
</style>