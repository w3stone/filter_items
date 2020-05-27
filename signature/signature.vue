<!--签名控件核心-->
<template>
      <section class="signature">
          <div class="signature_box">
              <div class="canvas_box" ref="canvasHW">
                  <canvas ref="canvasF" @touchstart='touchStart' @touchmove='touchMove' @touchend='touchEnd' @mousedown="mouseDown" @mousemove="mouseMove" @mouseup="mouseUp"></canvas>
                  <div class="btn_box">
                      <div @click="overwrite">重写</div>
                      <div @click="commit">提交签名</div>
                  </div>   
              </div>
          </div>
          <!-- <img class="imgCanvas" :src="imgUrl"> -->
      </section>    
</template>

<script>
    export default {
        name: "signature",
        props:{
            visible:Boolean
        },
        data() {
            return {
                dgVisible: this.visible,
                /*****************/
                stageInfo:'',
                imgUrl:'',
                client: {},
                points: [],
                canvasTxt: null,
                startX: 0,
                startY: 0,
                moveY: 0,
                moveX: 0,
                endY: 0,
                endX: 0,
                w: null,
                h: null,
                isDown: false,
                isViewAutograph: this.$route.query.isViews > 0,
                contractSuccess: this.$route.query.contractSuccess
            }
        },
        mounted() {
            this.initSignature();
        },
        methods: {
            //初始化签名版
            initSignature(){
                let canvas = this.$refs.canvasF;
                if(!canvas) return false;

                canvas.height = this.$refs.canvasHW.offsetHeight - 500;
                canvas.width = this.$refs.canvasHW.offsetWidth;
                this.canvasTxt = canvas.getContext('2d');
                this.canvasTxt.fillStyle = "#fff"; //fillStyle 属性设置或返回用于填充绘画的颜色、渐变或模式。
                this.canvasTxt.fillRect(0, 0, canvas.width, canvas.height);//绘制已填充矩形fillRect(左上角x坐标, 左上角y坐标, 宽, 高)
                //
                this.stageInfo = canvas.getBoundingClientRect();
            },
            //mobile
            touchStart(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (ev.touches.length == 1) {
                let obj = {
                    x: ev.targetTouches[0].clientX - this.stageInfo.left,
                    y: ev.targetTouches[0].clientY - this.stageInfo.top
                }
                this.startX = obj.x;
                this.startY = obj.y;
                this.canvasTxt.beginPath();
                this.canvasTxt.moveTo(this.startX, this.startY);
                this.canvasTxt.lineTo(obj.x, obj.y);
                this.canvasTxt.stroke();
                this.canvasTxt.closePath();
                this.points.push(obj);
                }
            },
            touchMove(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (ev.touches.length == 1) {
                    let obj = {
                        x: ev.targetTouches[0].clientX - this.stageInfo.left,
                        y: ev.targetTouches[0].clientY - this.stageInfo.top
                    }
                    this.moveY = obj.y;
                    this.moveX = obj.x;
                    this.canvasTxt.beginPath();
                    this.canvasTxt.moveTo(this.startX, this.startY);
                    this.canvasTxt.lineTo(obj.x, obj.y);
                    this.canvasTxt.stroke();
                    this.canvasTxt.closePath();
                    this.startY = obj.y;
                    this.startX = obj.x;
                    this.points.push(obj);
                }
            },
            touchEnd(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (ev.touches.length == 1) {
                    let obj = {
                        x: ev.targetTouches[0].clientX - this.stageInfo.left,
                        y: ev.targetTouches[0].clientY - this.stageInfo.top
                    }
                    this.canvasTxt.beginPath();
                    this.canvasTxt.moveTo(this.startX, this.startY);
                    this.canvasTxt.lineTo(obj.x, obj.y);
                    this.canvasTxt.stroke();
                    this.canvasTxt.closePath();
                    this.points.push(obj);
                }
            },
            //pc
            mouseDown(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (1) {
                    let obj = {
                        x: ev.offsetX,
                        y: ev.offsetY
                    }
                    this.startX = obj.x;
                    this.startY = obj.y;
                    this.canvasTxt.beginPath();
                    this.canvasTxt.moveTo(this.startX, this.startY);
                    this.canvasTxt.lineTo(obj.x, obj.y);
                    this.canvasTxt.stroke();
                    this.canvasTxt.closePath();
                    this.points.push(obj);
                    this.isDown = true;
                }
            },
            mouseMove(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (this.isDown) {
                    let obj = {
                        x: ev.offsetX,
                        y: ev.offsetY
                    }
                    this.moveY = obj.y;
                    this.moveX = obj.x;
                    this.canvasTxt.beginPath();
                    this.canvasTxt.moveTo(this.startX, this.startY);
                    this.canvasTxt.lineTo(obj.x, obj.y);
                    this.canvasTxt.stroke();
                    this.canvasTxt.closePath();
                    this.startY = obj.y;
                    this.startX = obj.x;
                    this.points.push(obj);
                }
            },
            mouseUp(ev) {
                ev = ev || event;
                ev.preventDefault();
                if (1) {
                    let obj = {
                        x: ev.offsetX,
                        y: ev.offsetY
                    }
                    this.canvasTxt.beginPath();
                    this.canvasTxt.moveTo(this.startX, this.startY);
                    this.canvasTxt.lineTo(obj.x, obj.y);
                    this.canvasTxt.stroke();
                    this.canvasTxt.closePath();
                    this.points.push(obj);
                    this.points.push({x: -1, y: -1});
                    this.isDown = false;
                }
            },
            //重写
            overwrite() {
                if(!this.canvasTxt) return false;
                this.canvasTxt.clearRect(0, 0, this.$refs.canvasF.width, this.$refs.canvasF.height);
                this.canvasTxt.fillStyle = "#fff";
			    this.canvasTxt.fillRect(0, 0, this.$refs.canvasF.width, this.$refs.canvasF.height);
                this.points = [];
            },
            //提交签名
            commit() {
                this.imgUrl = this.$refs.canvasF.toDataURL();
                console.log(this.imgUrl); //签名img回传后台

                this.$setLoading(true);
                this.$dataPost("/files/baseimages", {"content":this.imgUrl}, data=>{
                    this.$setLoading(false);
                    this.$emit("submitted", data);
                    this.dgVisible = false;
                });
            }
        },
        watch:{
            visible:{
                immediate: true,
                handler(newVal, oldVal){
                    this.dgVisible = newVal;
                    if(newVal==true){ //弹框打开
                        this.initSignature();
                    }else{
                        this.overwrite();
                    }
                }
            },
            dgVisible:{
                handler(newVal, oldVal){
                    this.$emit('update:visible', newVal);
                } 
            }
        }
    }
</script>

<style lang="scss" type="text/css">
    .signature_box{
        background-color: #fff;
        padding: 8px;

        canvas{
            border: 1px solid #333;
        }
        .btn_box div{
            display: inline-block;
            text-align: center;
            width: 50%;
            margin-top: 6px;
        }
    }
</style>