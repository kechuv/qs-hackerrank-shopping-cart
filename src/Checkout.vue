<template>
  <div class="layout-row">
    <ProductList
      class="flex-70"
      :products="products"
      @add="addToCart"
      @remove="removeFromCart"
    />
    <Cart
      class="flex-30"
      :cart="cart"
      @discount="setDiscount"
    />
  </div>
</template>

<script>
import ProductList from "@/components/ProductList";
import Cart from "@/components/Cart";

export default {
  name: "Checkout",
  components: {Cart, ProductList},
  data() {
    return {
      cart: {
        items: [],
        subTotal: 0,
        totalPrice: 0,
        discount: 0,
        selectedCoupon: 0
      },
      products: []
    }
  },
  watch: {
    'cart.items': {
      handler() {
        this.calcTotal();
      },
      deep: true
    },
    'cart.selectedCoupon'() {
      this.calcTotal();
    }
  },
  created() {
    this.products = PRODUCTS.map((product, index) => {
      product.id = index + 1;
      product.image = `/images/items/${product.name.toLocaleLowerCase()}.png`;
      product.cartQuantity = 0;
      return product;
    });
  },
  methods: {
    addToCart(product) {
      const index = this.products.findIndex(p => p.id === product.id);
      this.products[index].cartQuantity = 1;
      this.cart.items.push({
        id: this.products[index].id,
        item: this.products[index].heading,
        price: this.products[index].price,
        quantity: 1
      });
    },
    removeFromCart(product) {
      const index = this.products.findIndex(p => p.id === product.id);
      this.products[index].cartQuantity = 0;
      const cartIndex = this.cart.items.findIndex(c => c.id === product.id);
      this.cart.items.splice(cartIndex, 1);
    },
    setDiscount(discount) {
      this.cart.selectedCoupon = discount;
    },
    calcSubTotal() {
      this.cart.subTotal = this.cart.items.reduce((acc, item) => {
        return acc + (item.price * item.quantity)
      }, 0)
    },
    calcDiscount() {
      this.cart.discount = this.cart.subTotal * (this.cart.selectedCoupon / 100);
    },
    calcTotal() {
      this.calcSubTotal();
      this.calcDiscount();
      this.cart.totalPrice = this.cart.subTotal - this.cart.discount;
    }
  }
}
export const PRODUCTS = [
  {
    heading: "Cap - $10",
    name: "Cap",
    price: 10
  },
  {
    heading: "Hand Bag - $30",
    name: "HandBag",
    price: 30
  },
  {
    heading: "Shirt - $30",
    name: "Shirt",
    price: 30
  },
  {
    heading: "Shoes - $50",
    name: "Shoe",
    price: 50
  },
  {
    heading: "Pant - $40",
    name: "Pant",
    price: 40
  },
  {
    heading: "Slipper - $20",
    name: "Slipper",
    price: 20
  }
]

</script>

<style scoped>

</style>
