<template>
  <div id="rompecabezas">
    <div class="disponibles" v-show="!image.url">
      <div><img v-for="image in images" :src="image" @click="setImage(image)"></div>
    </div>
    <div v-show="image.url">
      <span class="btn default" @click="closeImage">Cerrar</span>
      <span class="btn primary" @click="randomize">Revolver</span>
      <select v-model.number="piezas_n">
        <option v-for="n in 10" :value="n" v-show="n>1">{{ n }}x{{ n }}</option>
      </select>
      <div class="image" :style="{width:image.w+'px', height:image.h+'px',
      //background:'url('+image.url+')',
      //backgroundSize:image.w+'px '+image.h+'px'
    }">
        <div v-for="(cell, cell_k) in piezas_rand" class="cell" :class="{active:(change.position === cell)}" :style="styleCell(cell)" @click="change_this(cell, cell_k)">
            {{ cell_k }}
          </div>
      </div>
    </div>
    {{ msg }}
    <div class="msg" style="display: none" v-show="completed === true">
      <div>
        You Won
        <span @click="closeImage">Cerrar</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      images:[
        './img/imagen1.jpg',
        './img/imagen2.jpg',
        './img/imagen3.jpg',
        './img/imagen4.jpg',
      ],
      image:{
        url:'',
        w:'',
        h:'',
        th_w:'',
        th_h:'',
      },
      change:{
        key:'',
        position:''
      },
      piezas_n:8,
      piezas:[],
      piezas_rand:[],
      msg:'',
    }
  },
  mounted(){
     //object.addEventListener("resize", myScript); 
  },
  methods:{
    closeImage(){
      this.image = {
          url:'',
          w:'',
          h:'',
          th_w:'',
          th_h:'',
        };
        this.piezas = [];
        this.piezas_rand = [];
        this.msg = '';
    },
    setImage(image){
      let this_ = this, img = new Image();
      this_.image.w = (window.innerWidth > 650) ? 650 : (window.innerWidth - 20);
      this_.image.th_w = this_.image.w / this_.piezas_n;
      this_.image.url = image;
      img.addEventListener("load", function(){
        this_.image.h = (this_.image.w * this.naturalHeight) / this.naturalWidth;
        this_.image.th_h = this_.image.h / this_.piezas_n;
        this_.setArrayItems();
      });
      img.src = image;
    },
    setArrayItems(){
      let this_ = this, items = new Array((this_.piezas_n * this_.piezas_n));
      this_.piezas = [];
      let row = 0, cell = 0;
      _.each(items, function(value, key){
        if(key > 0){
          if((key % this_.piezas_n)===0){
            row ++;
            cell = 0;
          }else{
            cell ++;
          }
        }
        this_.piezas.push('-'+(this_.image.th_w * cell) + 'px -' + (this_.image.th_h *  row) + 'px');
      });

      this_.piezas_rand = _.shuffle(this_.piezas);
    },
    randomize(){
      this.piezas_rand = _.shuffle(this.piezas);
    },
    styleCell(cell){
      return 'width:'+this.image.th_w+'px;'+
            'height:'+this.image.th_h+'px;'+
            'background:url('+this.image.url+');'+
            'background-size:'+this.image.w+'px '+this.image.h+'px;'+
            'background-position:'+cell;
    },
    change_this(cell, key){
      if(this.change.position){
        this.piezas_rand[this.change.key] = cell;
        this.piezas_rand[key] = this.change.position;
        this.change = {
          key:'',
          position:''
        }
      }else{
        this.change = {
          key:key,
          position:cell
        };
      }
    },
    completed(){
      let this_ = this, completed = false;
      if(this_.piezas.length > 0){
        completed = true;
        console.log('demo');
        for(let i = 0; i < this_.piezas.length; i++){
          if(this_.piezas[i] === this_.piezas_rand[i]){
            completed = completed && true;
          }else{
            completed = false;
          }
        }
      }
      console.log(completed);
      return completed;
    }
  },
  watch:{
    piezas_n(){
      this.image.th_w = this.image.w / this.piezas_n;
      this.image.th_h = this.image.h / this.piezas_n;
      this.setArrayItems();
    }
  }
}
</script>

<style lang="scss">
.disponibles{
  display: flex;
  align-items: top;
  img{
    width: 120px;
    margin: 5px
  }
}
.image{
  background-size:100% auto;
  margin:0 auto;
  display: flex;
  flex-wrap: wrap;
  .cell{
    border:1px #fff solid;
    box-sizing: border-box;
    font:700 20px arial,verdana,tahoma;
    color:#fff;
    cursor: pointer;
    &.active{
      border-color:#f00;
      box-shadow: 0 0 12px #fff;
    }
  }
}
.btn{
  display:inline-block;
  background:#f1f1f1;
  border:2px #ddd solid;
  cursor: pointer;
  font:14px/40px arial,verdana,tahoma;
  color: #555;
  padding:0 15px;
  &.primary{
    background: #188977;
    border-color:#137177;
    color:#fff;
  }
  &.danger{
    display: block;
  }
}
.msg{
  position: fixed;
  background: rgba(0,0,0,0.8);
  width: 100%;
  height: 100%;
  top:0;
  left: 0;  
  padding: 20px;
  box-sizing: border-box;
  >div{
    width: 350px;
    height: 150px;
    max-width: 100%;
    background: #fff;
    margin:0 auto;
    position: relative;
    font:700 50px/2 arial,verdana,tahoma;
    color: #FFC922;
    text-transform: uppercase;
    text-align: center;
    span{
      position: absolute;
      bottom: 0;
      left:0;
      width: 100%;
      height: 40px;
      text-align: center;
      background: #f02;
      font-size: 14px;
      line-height: 40px;
      color: #fff;
      cursor: pointer;
    }
  }
}
</style>
