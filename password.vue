<!--select封装-->
<template>
    <div class="form_item_input">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-input class="filter_input" v-model="currentValue" :placeholder="placeholder" show-password
                        :style="{'width':width}" :maxlength="maxlength || 20"
                        @blur="updateVal(currentValue)"
                        @keydown.enter.native.prevent="updateVal(currentValue)"
                        :disabled="disabled">
                    </el-input>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-input class="filter_input" v-model="currentValue" :placeholder="placeholder" show-password
                :style="{'width':width}" :maxlength="maxlength || 20"
                @blur="updateVal(currentValue)"
                @keydown.enter.native.prevent="updateVal(currentValue)"
                :disabled="disabled">
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
        display: inline-block;
        // .filter_input{
        //     width: $inputWidth;
        // }
    }
</style>