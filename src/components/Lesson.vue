<template>
  <div class="row classes">
    <!-- to call classes.js-->
    <div
      class="card"
      style="width: 270px"
      v-for="lecture in searchSortLecture"
      :key="lecture.id"
    >
      <img v-bind:src="baseURL + '/' + lecture.image" style="width: 268px; height: 200px" />
      <div class="card-body">
        <font-awesome-icon v-bind:icon="lecture.fontAwesomeIcon" /><br />
        <h4 align="left">Subject: <b v-text="lecture.subject"></b></h4>
        <p align="left">Location: <b v-text="lecture.location"></b></p>
        <p align="left">
          Price: <b class="price">£{{ lecture.price }}</b>
        </p>
        <p align="left">
          Spaces: <b v-text="lecture.spaces - cartCount(lecture)"></b>
        </p>
        <!--the number of spaces reduces when an item is added to cart-->
        <span class="message" v-if="lecture.spaces - cartCount(lecture) === 0">
          Fully booked!
        </span>
        <span class="message" v-else>Book a lesson with us today! </span>
      </div>

      <!-- adding the add to cart button-->
      <!--if there is still any space available button is enabled -->
      <button
        class="btn btn-info text-light btn-lg"
        v-on:click="addToCart(lecture)"
        v-if="canAddToCart(lecture)"
      >
        Add to cart
      </button>

      <!--if the number of spaces is = 0 the button is disabled -->
      <button disabled="true" class="btn btn-secondary btn-lg" v-else>
        Add to cart
      </button>
      <br />
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  props: ["searchSortLecture", "baseURL", "cart"],
  methods: {
    //returns number of spaces greater than cartCount(lecture) boolean data type
    canAddToCart(lecture) {
      return lecture.spaces > this.cartCount(lecture);
    },
    //counting the number of items(spaces) being added to cart
    cartCount(lecture) {
      let count = 0;
      for (var i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === lecture) {
          count++;
        }
      }
      return count;
    },
    // adds a lesson to cart
    addToCart: function (lecture) {
      this.$emit("add-item-to-cart", lecture);
    },
  },
};
</script>
