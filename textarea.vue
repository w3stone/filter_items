<!--textarea封装-->
<template>
    <div class="form_item_textarea">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-input class="filter_textarea" v-model="currentValue" type="textarea" 
                        :rows="finalRows" :placeholder="placeholder || '请输入内容'"
                        :disabled="disabled" :maxlength="maxlength || 250"
                        @blur="updateVal(currentValue)">
                    </el-input>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-input class="filter_textarea" v-model="currentValue" type="textarea" 
                :rows="finalRows" :placeholder="placeholder || '请输入内容'"
                :disabled="disabled" :maxlength="maxlength || 250"
                @blur="updateVal(currentValue)">
            </el-input>
        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "textareas",
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
            rows: Number, //行数
            disabled: Boolean,
            maxlength: Number //最大长度
        },
        data(){
            return {
                currentValue: this.value
            }
        },
        computed:{
            finalRows(){
                return this.rows || 5;
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
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
        watch:{
            value:{
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
    .form_item_textarea{
        .filter_textarea{
            //输入框
            .el-textarea__inner{
                resize: none;
            }
        }
    }
</style>