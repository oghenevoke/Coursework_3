<template>
  <div id="app">
    <header class="header">
      <nav
        class="navbar navbar-expand-lg navbar-dark py-4 shadow-sm bg-info"
        style="height: 80px"
      >
        <div class="container-fluid">
          <div class="navbar-header">
            <!-- <a class="navbar-brand text-light" href="#"
              ><img src="images/logo.png" alt="" width="120" height="120"
            /></a> -->
          </div>

          <div class="p-3 shadow-sm index-forward border-top" id="search">
            <form class="navbar-form navbar-left">
              <div class="input-group">
                <input
                  type="text"
                  class="form-control"
                  placeholder="Search"
                  name="search"
                  v-model="search"
                />
              </div>
            </form>
          </div>
        </div>
      </nav>
      <nav
        class="navbar navbar-expand-lg navbar-dark py-0 shadow-sm bg-dark"
        style="height: 45px"
      >
        <div class="container-fluid">
          <div class="navbar-header">
            <!--<a class="navbar-brand text-dark" href="#">E-Learning</a>-->
            <select v-model="sorting" align="left">
              <option disabled value="">Sort by</option>
              <option value="price">Price</option>
              <option value="location">Location</option>
              <option value="subject">Subject</option>
              <option value="space">Availability</option>
            </select>

            <select v-model="order" align="left">
              <option disabled value="">
                <i class="fa-solid fa-arrow-up-a-z"></i>Order in
              </option>
              <option value="ascending">Ascending</option>
              <option value="descending">Descending</option></select
            ><br />
          </div>

          <ul class="nav navbar-nav">
            <!-- if the item in the cart is more than 0 the button is enabled and clickable-->
            <li v-if="this.cart.length > 0">
              <!-- when clicked on displays the cart item -->
              <button v-on:click="togglePage" class="btn-light">
                <!--showing the number of lessons being added to cart-->
                {{ cartItemCount }}
                <font-awesome-icon icon="fa-solid fa-cart-shopping" /> Cart
              </button>
            </li>
            <li v-else>
              <!-- if the item in the cart is less than 0 the button is disabled-->
              <button disabled="true" class="btn-light">
                <font-awesome-icon icon="fa-solid fa-cart-shopping" /> Cart
              </button>
            </li>
          </ul>
        </div>
      </nav>
    </header>

    <main>
      <component
        :is="currentView"
        :searchSortLecture="searchSortLecture"
        :baseURL="baseURL"
        :cart="cart"
        @add-item-to-cart="addToCart"
        @remove-item-from-cart="removeFromCart"
      ></component>
    </main>
  </div>
</template>

<script>
import Lesson from "./components/Lesson.vue";
import Checkout from "./components/Checkout.vue";

export default {
  name: "app",
  data() {
    return {
      currentView: Lesson,
      classes: [],
      cart: [],
      sorting: "", //this is to sort by price, subject, location or availability/spaces
      order: "", // to order in ascending or descending order
      search: "", // to search by subject and location
      baseURL:
        "https://lessonsapp-env.eba-eqrrgh64.eu-west-2.elasticbeanstalk.com",
    };
  },
  created: function () {
    fetch(`${this.baseURL}/collections/lessons`)
      .then((response) => response.json())
      .then((classes) => {
        this.classes = classes;
      });
  },
  components: {
    Lesson,
    Checkout,
  },

  computed: {
    //searches by subject and location
    searchSortLecture: function () {
      let tempLecture = this.classes;

      if (this.search != "" && this.search) {
        tempLecture = tempLecture.filter((lecture) => {
          return (
            lecture.subject.toLowerCase().match(this.search.toLowerCase()) ||
            lecture.location.toLowerCase().match(this.search.toLowerCase())
          );
        });
      }

      tempLecture = tempLecture.sort((a, b) => {
        if (this.order == "ascending") {
          if (this.sorting == "subject") {
            if (a.subject.toLowerCase() < b.subject.toLowerCase()) {
              return -1;
            }
            if (a.subject.toLowerCase() > b.subject.toLowerCase()) {
              return 1;
            }
            return 0;
          }
          if (this.sorting == "location") {
            if (a.location.toLowerCase() < b.location.toLowerCase()) {
              return -1;
            }
            if (a.location.toLowerCase() > b.location.toLowerCase()) {
              return 1;
            }
            return 0;
          }
          if (this.sorting == "price") {
            return a.price - b.price;
          }
          if (this.sorting == "space") {
            return a.spaces - a.cartItemCount - (b.spaces - b.cartItemCount);
          }
        } else if (this.order == "descending") {
          if (this.sorting == "subject") {
            if (a.subject.toLowerCase() < b.subject.toLowerCase()) {
              return 1;
            }
            if (a.subject.toLowerCase() > b.subject.toLowerCase()) {
              return -1;
            }
            return 0;
          }
          if (this.sorting == "location") {
            if (a.location.toLowerCase() < b.location.toLowerCase()) {
              return 1;
            }
            if (a.location.toLowerCase() > b.location.toLowerCase()) {
              return -1;
            }
            return 0;
          }
          if (this.sorting == "price") {
            return b.price - a.price;
          }
          if (this.sorting == "space") {
            return b.spaces - b.cartItemCount - (a.spaces - a.cartItemCount);
          }
        }
      });

      return tempLecture;
    },
    // Number of items in Cart
    cartItemCount: function () {
      if (this.cart.length > 0)
        return this.cart.reduce((total, item) => total + item.spaces, 0);
      return 0;
    },
  },

  methods: {
    //counting the number of items(spaces) being added to cart

    //this is just saying they are equal to each other and whenever this method is used to toggle's to homepage when not and homepage and when not on home page to the check out page
    togglePage() {
      if (this.currentView == Lesson) {
        this.currentView = Checkout;
      } else {
        this.currentView = Lesson;
      }
    },
    //adds or pushes items to cart
    addToCart(lecture) {
      --lecture.spaces;
      var lectureInCart = this.cart.find((u) => u.lessonId == lecture._id);

      if (lectureInCart != null) {
        ++lectureInCart.spaces;
      } else {
        lectureInCart = { lessonId: lecture._id, spaces: 1, lecture: lecture };
        this.cart.push(lectureInCart);
      }
    },
    //removes items from cart and adds back the number of space
    removeFromCart(lessonId) {
      var itemInCart = this.cart.find((u) => u.lessonId == lessonId);
      if (itemInCart.spaces == 1) {
        var index = this.cart.map((x) => x.lessonId).indexOf(lessonId);
        this.cart.splice(index, 1);

        //when the cart is empty goes back to home page
        if (this.cart.length <= 0) {
          this.togglePage();
        }
      } else {
        --itemInCart.spaces;
      }

      var lecture = this.classes.find((u) => u._id == lessonId);
      ++lecture.spaces;
    },
  },
};
</script>
