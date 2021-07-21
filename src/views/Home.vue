<template>
  <div class="home">
    <h2>{{ message }}</h2>
    <h1>New Place!</h1>
    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <button v-on:click="createPlace()">Create Place!</button>
    </div>
    <h1>All Places!</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>Name: {{ place.name }}</h2>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info Please!</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>My Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
    <ul>
      <li v-for="error in errors" :key="error.id">{{ error }}</li>
    </ul>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to My Places!",
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("places index", response);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log("place created", response);
          this.places.push(response.data);
          this.newPlaceParams = {};
        })
        .catch((error) => {
          console.log("place create error", error.response);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var editPlaceParams = place;
      axios
        .patch("http://localhost:3000/places/" + place.id, editPlaceParams)
        .then((response) => {
          console.log("place updated", response);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log("places update error", error.response);
        });
    },
    destroyPlace: function (place) {
      axios.delete("http://localhost:3000/places/" + place.id).then((response) => {
        console.log("place destroy", response);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
