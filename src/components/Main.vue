<template>
  <div class="main">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">

<!-- Search Bar -->
    <div class="search">
      <input v-model="searchProduct" type="text" class="searchTerm" placeholder="Search a product here ...">
      <button type="submit" class="searchButton">
        <i class="fa fa-search"></i>
     </button>
    </div>

<!-- Categories Part -->
<div class="categories">
     <ul>
        <li v-for="(value, index) in categories" v-bind:key="index">
          <b-card @click="onSelect(value)" 
                  v-bind:class="[value.active ? 'selected-category-card': 'category-card', hovered ? 'hovered-category-card' : '']" 
                  text-variant="dark">
            <b-card-text class="card-name">
              {{value.name}} <p class="text-muted">{{value.quantity}}</p>
            </b-card-text>
          </b-card>
       </li>
     </ul>   
   </div>

   

<!--Products' Part -->
   <div class="products">
     <ul>
        <li v-for="(product, index) in filteredList" v-bind:key="index">
          <div class="product-card">
            <div class="product-image">
              <img :src=product.image>
            </div>
            <div class="product-name">
              {{product.name}}
            </div>
            <div class="product-cost">
              {{product.cost}}
            </div>
            <i @click="addProduct(product)" id="add-icon" class="fa fa-plus-square"></i>
          </div>
        </li>
     </ul>
   </div>

<!-- Cart -->
    <div class="cart">
      <div class="title">Cart: </div>
      <ul>
        <li class="item" v-for="(product, index) in cartProducts" v-bind:key="index">
          <div @click="deleteProduct(product)" class="delete">
            <i class="fa fa-trash-o"></i>
          </div>
          <div class="product-name">
            {{product.name}}
          </div>
          <div class="product-cost">
            {{product.cost}}
          </div>
          <div class="quantity">
            <i @click="incrementQuantity(product)" id="plus-btn" class="fa fa-plus"></i>
            <span> {{product.quantity}}</span>
            <i @click="decrementQuantity(product)" id="minus-btn" class="fa fa-minus"></i>
          </div>
          <div class="current-price">${{calculatePrice(product)}}</div>
        </li>
      </ul>
    </div>

<!-- Paycheck -->
      <div class="paycheck">
        <div class="text"> Всего </div>
        <div class="price">${{totalPrice}}</div>
        <div style="margin-top: 26px" class="text"> Скидка </div>
        <div class="price">0</div>
        <div class="total">
          <div class="text">Итого к оплате</div>
          <div class="price"> ${{totalPrice}} </div>
        </div>
        <div class="paycheck-icons">
          <i @click="printWindow" class="fa fa-print"></i>
          <i @click="showModal = true" class="fa fa-floppy-o" aria-hidden="true"></i>
        </div>
          
        <button class="btn btn-success">Оплата</button>
      </div>

<!-- Pop up modal -->
      <div v-if="showModal">
          <transition name="modal">
            <div class="modal-mask">
              <div class="modal-wrapper">
                <div class="modal-dialog">
                  <div class="modal-content">
                    <div class="modal-header">
                      <button type="button" class="close" @click="showModal=false">
                        <span aria-hidden="true">&times;</span>
                      </button>
                    </div>
                    <div class="modal-body">
                      Successfully saved!
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </transition>
      </div>

  </div>
</template>

<script>
export default {
  name: 'Main',
  props: {
    categories: Array,
    products: Array
  },
  data() {
    return {
      cartProducts: [],
      selectedProducts: [],
      searchProduct: '',
      selected: false,
      hovered: true,
      showModal: false
    }
  },
  methods: {
    onSelect(value){
      this.selected = !this.selected
      for(let i=0; i<this.products.length; i++){
          if(this.products[i].category == value.name){
            this.selectedProducts.push(this.products[i])
          }
      }
      for(let i=0; i<this.categories.length; i++){
          if(this.categories[i].name == value.name){
            this.categories[i].active = true
          }else{
          this.categories[i].active = false
          }
      }
      if(this.selected == true){
        this.selectedProducts = []
      }
    },

    addProduct(product) {
      let contains = false
      let item = {
        quantity: 1,
        name: product.name,
        cost: product.cost
      }

      for(let i=0; i<this.cartProducts.length; i++){
          if(this.cartProducts[i].name == product.name){
              contains = true
          }
      }

      if(!contains){
        this.cartProducts.push(item)
      }else{
        for(let i=0; i<this.cartProducts.length; i++){
          if(this.cartProducts[i].name == product.name){
              this.cartProducts[i].quantity++
        }
      }
      }
    },

    deleteProduct(product) {
      for(let i=0; i<this.cartProducts.length; i++){
          if(this.cartProducts[i] == product){
              this.cartProducts.splice(i,1)
          }
        }
    },

    calculatePrice(product){
      let price = 0
      for(let i=0; i<this.cartProducts.length; i++)
      {
        if(this.cartProducts[i].name == product.name){
            let cost = this.cartProducts[i].cost.replace('$', '') 
            price = cost * this.cartProducts[i].quantity
        }
      }
      return price.toFixed(1)
    },

    incrementQuantity(product){
      for(let i=0; i<this.cartProducts.length; i++)
      {
        if(this.cartProducts[i].name == product.name){
            this.cartProducts[i].quantity++
            this.calculatePrice(product)
        }
      }
    },

    decrementQuantity(product){
      for(let i=0; i<this.cartProducts.length; i++)
      {
        if(this.cartProducts[i].name == product.name){
          if(this.cartProducts[i].quantity > 0){
            this.cartProducts[i].quantity--
            this.calculatePrice(product)
          }
        }
      }
    },

    printWindow(){
      window.print()
    },

  },

  computed: {
    totalPrice(){
      let total = 0
      for(let i=0; i<this.cartProducts.length; i++)
      {
        let cost = this.cartProducts[i].cost.replace('$', '') 
        total += cost * this.cartProducts[i].quantity
      }
      return total.toFixed(2)
    },

    filteredList() {
      if(this.searchProduct == '' || this.searchProduct == null){
        return this.selectedProducts
      }else{
        return this.selectedProducts.filter(product => {
          return product.name.toLowerCase().includes(this.searchProduct.toLowerCase())
        })
      }
      
    }
    
  },

  mounted() {
      for(let i=0; i<this.categories.length; i++){
        for(let j=0; j<this.products.length; j++){
          if(this.categories[i].name == this.products[j].category){
              this.categories[i].quantity++
          }
        }
      } 
    }
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}

/* Search Bar Style */
.search {
  margin: 10px 10px;
  width: 62%;
  position: relative;
  display: flex;
}
.searchTerm {
  width: 100%;
  border: 1px solid #00B4CC;
  border-right: none;
  padding: 5px;
  height: 36px;
  border-radius: 5px 0 0 5px;
  outline: none;
}
.searchButton {
  width: 40px;
  height: 36px;
  border: 1px solid #00B4CC;
  background: #00B4CC;
  text-align: center;
  color: #fff;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
  font-size: 20px;
}

/* Category Cards Styles */
.categories {
    background: #d4d3d3;
    margin: 10px 10px;
    width: 200px;
    float: left;
}
.category-card{
  margin: 5px 0;
  width: 180px;
  height: 100px;
  border: 1px #00B4CC solid;
  cursor: pointer;
}
.selected-category-card{
  background-color: rgb(187, 207, 211);
  margin: 5px 0;
  width: 180px;
  height: 100px;
  border: 1px #1ed107 solid;
  cursor: initial;
}
.hovered-category-card:hover{
  background: rgb(187, 207, 211);
}
.card-name{
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
}

/* Product Cards Styles */
.products{
  width: 50%;
  margin: 10px 0;
  float: left;
}
.product-card{
  width: 300px;
  height: 80px;
  margin: 10px 5px;
  padding: 5px 5px;
  border: 1px skyblue solid;
  border-radius: 10px;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
}
.product-card:hover{
  background: rgb(187, 207, 211);
}
.product-name{
  margin: 10px 15px;
  float: left;
  text-transform: uppercase;
}
.product-image{
  margin: 5px 0;
  overflow: auto;
  float:left
}
.product-cost{
  margin-top: 30px;
  margin-right: 150px;
  opacity: .8;
}
#add-icon{
  transform: translate(-10px, -170%);
  font-size: 30px;
  float: right;
  cursor: pointer;
}

/* Cart style */
.cart{
  float: center;
  width: 450px;
  height: auto;
  margin: 10px 10px;
  background: #FFFFFF;
  box-shadow: 1px 2px 3px 0px rgba(0,0,0,0.10);
  border: 1px #00B4CC solid;
  display: flex;
  flex-direction: column;
  border-radius: 5px;
}
.cart .title{
  height: 50px;
  border-bottom: 1px solid #E1E8EE;
  padding: 10px 30px;
  color: #5E6977;
  font-size: 25px;
  font-weight: 500;
}
.cart .item{
  padding: 20px 20px;
  height: 120px;
  display: flex;
  border-bottom: 1px solid #E1E8EE; ;
}
.delete{
  position: relative;
  padding-top: 25px;
  margin-right: 5px;
  cursor: pointer;
  font-size: 20px;
}
.delete:hover{
  color: #6e7c7e;
}
.cart .product-name {
  padding-top: 10px;
  margin-right: 10px;
  width: 100px;
  font-size: 16px;
  color: #43484D;
  font-weight: 400;
  text-align: left;
}
.quantity>i{
  margin: 5px 20px;
  display: block;
  font-size: 20px;
  cursor: pointer;
}
 .quantity>i:hover{
   color: rgb(171, 171, 201);
 }
 
 .current-price {
  padding-top: 25px;
  text-align: center;
  font-size: 16px;
  color: #43484D;
  font-weight: 300;
}

/* Paycheck box style */
.paycheck{
  float: right;
  width: 450px;
  height: 200px;
  margin: 5px 5px;
  padding-top: 10px ;
  border: 1px rgb(12, 8, 26) solid;
  border-radius: 5px;
  background: rgb(24, 58, 87);
}
.paycheck>.text{
  position: absolute;
  float: left;
  padding-left: 15px;
  color: #929292;
  font-size: 17px;
}
.paycheck>.price{
  float: right;
  padding-right: 15px;
  color: rgb(236, 234, 234);
  margin-left: 320px;
  font-size: 18px;
}
.paycheck>.btn{
  position: absolute;
  right: 0;
  margin-top: 130px;
  margin-right: 25px;
}
.paycheck-icons{
  position: absolute;
  color: rgb(165, 158, 158);
  font-size: 25px;
  float: left;
  margin: 130px 20px;
  cursor: pointer;
}
.paycheck-icons>i{
  margin-right: 15px;
}
.paycheck-icons>i:hover{
  color: rgb(255, 255, 255);
}
.total>.text{
  color: rgb(165, 158, 158);
  float: left;
  padding-top: 5px;
  padding-left: 15px;
}
.total>.price{  
  float: right;
  padding-right: 15px;
  color: rgb(236, 234, 234);
  font-size: 22px;
}

/* Popup window when pressing SAVE button */
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

/* Media for printing page */
@media print {
  div.search{
    display: none;
  }
  div.categories{
    display: none;
  }
  div.cart{
    position: absolute;
  }
}
</style>
