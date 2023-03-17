<template>
<div class="row classes">
                <!-- to call classes.js-->
                <div class="card" style="width:270px;" v-for="lecture in cart" :key="lecture.id">
                    <img v-bind:src="baseURL + '/' + lecture.lecture.image" style="width:268px; height:200px;">
                    <div class="card-body">
                      <font-awesome-icon v-bind:icon="lecture.lecture.fontAwesomeIcon" />
                        <!-- <i v-bind:class="lecture.lecture.fontAwesomeIcon" class="fa-3x"></i>--> <br /> 
                        <h4 align="left">Subject: <b v-text="lecture.lecture.subject"></b></h4>
                        <p align="left">Location: <b v-text="lecture.lecture.location"></b></p>
                        <p align="left">Item in cart: <b v-text="lecture.spaces"></b></p>
                        <p align="left">Price: <b class="price">£{{ lecture.lecture.price }}</b></p>
                    </div>

                    <!-- to call the removeFromCart() method which would remove the items from the cart when the button is clicked on-->
                    <button class="btn btn-secondary btn-lg" v-on:click="removeFromCart(lecture.lessonId)">
                        Remove from cart
                    </button>  
                </div>
            <br /><br /><br /><br />
                <div style="text-align: center; padding-left: 20px; padding-right: 20px; padding-top: 100px;" class="checkout">
                    <div class="form-group">
                        <div class="card bg-dark text-light" style="width: 1050px;">
                            <h2>
                                <strong>CHECK OUT</strong>
                            </h2>
                        </div>
                    </div>
                    <form @submit.prevent>
                        <div class="form-group">
                            <div class="col-md-12"><strong>Name:</strong></div>
                                <input v-model.trim="name" class="form-control" id="name" name="name" type="text" placeholder="Bxxxxxxx Exxxxxxx" v-on:keypress="isLetter($event)"/>
                        </div>
                        <div class="form-group">
                            <div class="col-md-12"><strong>Phone Number:</strong></div>
                                <input v-model="phoneNumber" class="form-control" id="phoneNumber" name="phoneNumber" placeholder="+44 xxxxxxxxxx" v-on:keypress="isNumber($event)"/>
                        </div>
                        <div class="form-group">
                            <div class="col-md-12">
                                <button type="submit" class="btn btn-info submit" :disabled="!disabled.every(i => i === false)" v-on:click="orderMessage">
                                    Place Order
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      disabled: [true, true],
      name: "",
      phoneNumber: "",
    };
  },
  props: ["searchSortLecture", "baseURL", "cart"],
  methods: {
    orderMessage() {
      alert("Your Order has been successfully taken");
      window.location.reload();
    },

    isLetter(e) {
      const nameField = document.getElementById("name");
      let char = String.fromCharCode(e.keyCode); // Gets the character
      if (/^[A-Za-z]+$/.test(char)) {
        nameField.style.border = "green solid 3px";
        this.disabled = [false, this.disabled[1]];
      } else {
        nameField.style.border = "red solid 3px";
        this.disabled = [true, this.disabled[1]];
      }
    },

    isNumber(e) {
      const phoneNumberField = document.getElementById("phoneNumber");
      let char = String.fromCharCode(e.keyCode); // Gets the character
      if (/^[0-9]+$/.test(char)) {
        phoneNumberField.style.border = "green solid 3px";
        this.disabled = [this.disabled[0], false];
      } else {
        phoneNumberField.style.border = "red solid 3px";
        this.disabled = [this.disabled[0], true];
      }
    },
    // removes a lesson from cart
    removeFromCart(lessonId) {
      this.$emit("remove-item-from-cart", lessonId);
    },
  },
};
</script>
