<!--select封装-->
<template>    
    <el-select class="filter_select" v-model="currentValue" :multiple="multiple"
        filterable :placeholder="placeholder? placeholder: '请选择'"
        :disabled="disabled" :clearable="!notClearable"
        :allow-create="allowCreate"
        @change="updateVal">

        <el-option v-for="(item,index) in currentOptions" :key="index"
            :label="item[finalProps.label]" :value="item[finalProps.value]">
        </el-option>
    </el-select>
</template>

<script>
    export default {
        name: "selects",
        props:{
            value: {
                required: true
            },
            options: { //选项列表
                required: true,
                type: Array
            },
            placeholder: String,
            multiple: Boolean, //是否多选
            valueType: String, //接收数据类型(String or Array),默认为Array
            props: Object, //配置选项
            notClearable: Boolean, //是否可清除
            allowCreate: Boolean, //是否允许添加
            loading: Boolean, //是否正在获取选项
            disabled: Boolean //禁用状态
        },
        data(){
            return {
                currentValue: this.value,
                currentOptions: this.options,
                originOptions: this.options,
                connector: ",", //value分隔符
                defaultProps: {
                    label: 'title',
                    value: 'value'
                }
            }
        },
        computed:{
            finalProps(){
                return this.props || this.defaultProps;
            }
        },
        methods:{
            makeValue(value){
                if(this.multiple){ //模拟多选
                    if(this.valueType=='string'){
                        value = value || "";
                        this.currentValue = value? value.split(this.connector): [];

                    }else{
                        this.currentValue = value || [];
                    }

                }else{ //单选
                    value = (value || value===0)? value: "";
                    this.currentValue = value;
                }
            },
            //向父级传值
            updateVal(value){
                let finalValue = null;
                if(this.multiple && this.valueType=='string'){ //模拟多选
                    
                    finalValue = value.length>0? value.join(this.connector): "";
                }else{
                    finalValue = value;
                }

                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
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
                    this.originOptions = newVal || [];
                    this.currentOptions = newVal || [];
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_select{
        display: inline-block;
    }
</style>