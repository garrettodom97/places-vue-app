<template>
  <div>
    <h1>Add a New Place</h1>
    <p>Name:</p>
    <input type="text" v-model="newParams.name" />
    <p>Address:</p>
    <input type="text" v-model="newParams.address" />
    <p></p>
    <button v-on:click="createPlace">Add</button>
    <h1>Places</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info!</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Destroy</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newParams: {},
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var editParams = place;
      axios
        .patch("http://localhost:3000/places/" + place.id, editParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    destroyPlace: function (place) {
      axios.delete("http://localhost:3000/places/" + place.id).then((response) => {
        console.log(response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
