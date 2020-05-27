<!--select封装-->
<template>
    <div class="form_item_input">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-autocomplete class="filter_input" v-model="currentValue" 
                        :style="{'width':width}" :maxlength="maxlength || 100"
                        :fetch-suggestions="querySearch"
                        :placeholder="placeholder || '请输入内容'" 
                        @blur="updateVal(currentValue)" @select="updateVal(currentValue)"
                        :disabled="disabled" clearable>
                    </el-autocomplete>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-autocomplete class="filter_input" v-model="currentValue" 
                :style="{'width':width}" :maxlength="maxlength || 100"
                :fetch-suggestions="querySearch"
                :placeholder="placeholder || '请输入内容'" 
                @blur="updateVal(currentValue)" @select="updateVal(currentValue)"
                :disabled="disabled" clearable>
            </el-autocomplete>

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
            options: { //选项列表
                required: true,
                type: Array
            },
            props: Object, //配置规则
            width: String, //宽度
            placeholder: String,
            validate: String, //校验规则(正则)
            disabled: Boolean,
            maxlength: Number //最大长度
        },
        data(){
            return {
                currentValue: this.value,
                originOptions: [],
                currentOptions: []
            }
        },
        computed:{
            f_props(){
                return this.props || {name: "name"};
            }
        },
        methods:{
            //重置options(本地自动补全多选)
            querySearch(queryString, callback){
                let list = this.originOptions;
                let result = queryString? list.filter(o => {
                    let temptitle = o[this.f_props.name] || "";
                    return temptitle.indexOf(queryString)!=-1;
                }): list.slice(0,100);
                result = result.map(o=>( {"value": o[this.f_props.name]} ));
                callback(result);
            },
            //向父级传值
            updateVal(value){
                //console.log(value);
                value = value || "";

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
            formItem
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                    this.currentValue_bak = newVal; //备份
                }
            },
            options:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.originOptions = this.options || []; //完整字典表
                    this.currentOptions = this.originOptions.slice(0,100);
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .form_item_input{
        display: inline-block;
        .filter_input{
            width: $inputWidth;
        }
    }
</style>