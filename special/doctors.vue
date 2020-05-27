<!--select封装-->
<template>
    <div class="form_item_select">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur','change']}
                ]" ref="formItem">

                <div>
                    <el-autocomplete class="filter_autocomplete" v-model="currentValue" :fetch-suggestions="querySearch" placeholder="请输入"
                        @select="updateVal(currentValue)" @blur="blur" :disabled="disabled" clearable>
                    </el-autocomplete>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>
            </form-item>
        </template>

        <template v-else>
            <el-autocomplete class="filter_autocomplete" v-model="currentValue" :fetch-suggestions="querySearch" placeholder="请输入"
                @select="updateVal(currentValue)" @blur="blur" :disabled="disabled" clearable>
            </el-autocomplete>
        </template>
    </div>
</template>

<script>
    import formItem from '../formItem';

    export default {
        name: "special_doctors",
        props:{
            withForm: Boolean,
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            value: {
                required: true
            },
            disabled: Boolean, //禁用状态
            placeholder: String,
            disabled: Boolean
        },
        data(){
            return {
                currentValue: "",
                currentOptions: []
            }
        },
        computed:{
            patientHosid(){
                return this.$getSession("patientHosid") || "0";
            }
        },
        mounted(){
            this.makeValue(this.value);
            this.getDoctors();
        },
        methods:{
            getDoctors(){
                this.$dataPost("/Visit/itemdict", {"ItemId":this.itemid, "HospitalId":this.patientHosid}, (data)=>{
                    this.currentOptions = data || [];
                });
            },
            makeValue(value){
                this.currentValue = value;
            },
            querySearch(queryString, callback){
                let list = this.currentOptions;
                let results = queryString ? list.filter(o=>o.value.indexOf(queryString)>-1): list;
                callback(results);
            },
            //向父级传值
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            blur(e){
                //console.log(e);
                this.updateVal(this.currentValue);
                this.$emit('blur', e);
                
                //如果没有，临时插入一条
                if(this.currentOptions.filter(o=>o.value==this.currentValue).length==0 && this.currentValue){
                    this.currentOptions.unshift({"title":this.currentValue, "value":this.currentValue});
                }
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
    .form_item_select{
        .filter_autocomplete{
            width: $inputWidth;
        }
    }
</style>