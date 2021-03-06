<template>
  <v-app-bar app color="purple" light>
    <v-toolbar-title class="font-weight-light">
      <v-btn
        text
        class="text-none font-weight-light headline"
        @click="$router.push('/')"
      >
        <span class="font-semibold">Tina's Pastries</span>
      </v-btn>
    </v-toolbar-title>

    <v-spacer></v-spacer>

    <v-btn v-if="!user" to="/login" text>
      <span class="mr-2">Login</span>
    </v-btn>
    <div v-else>
      <v-menu bottom left>
        <template v-slot:activator="{ on }">
          <v-btn text v-on="on">
            <span class="mr-2">{{ user.displayName }}</span>
            <v-icon>mdi-menu-down</v-icon>
          </v-btn>
          <v-badge v-if="cart" color="blue" overlap>
            <template v-slot:badge>{{ cart.items.length }}</template>
            <v-btn text @click="$router.push('/cart')">
              <v-icon>mdi-cart</v-icon>
            </v-btn>
          </v-badge>
        </template>

        <v-list>
          <v-list-item>
            <v-list-item-title>
              <v-btn block text to="/about">About Us</v-btn>
            </v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>
              <v-btn block text to="/myorders">Orders</v-btn>
            </v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>
              <v-btn block text to="/cart">Cart</v-btn>
            </v-list-item-title>
          </v-list-item>
          <v-list-item v-if="user.roles && user.roles.admin">
            <v-list-item-title>
              <v-btn block text to="/inventory">Inventory</v-btn>
            </v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>
              <v-btn block text @click="logOut">Logout</v-btn>
            </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </div>
  </v-app-bar>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import { db } from "../plugins/firebase";

export default {
  name: "NavBar",
  data() {
    return {
      cart: {}
    };
  },
  computed: {
    ...mapGetters({
      user: "getUser"
    })
  },
  updated() {
    this.bind();
  },
  methods: {
    async logOut() {
      await this.$firebase.auth().signOut();
      this.setUser("");
      this.$router.push("/");
    },
    async bind() {
      await this.$bind("cart", db.collection("cart").doc(this.user.uid));
    },
    ...mapActions(["setUser"])
  }
};
</script>
