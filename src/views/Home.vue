<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("/places", this.newPlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.push(response.data);
          this.newPlaceParams = {};
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      axios
        .patch(`/places/${place.id}`, this.editPlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log("places update error", error.response.data.errors);
        });
    },
    destroyPhoto: function (place) {
      axios.delete("/places/" + place.id).then((response) => {
        console.log("Places destroy", response);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
    // destroyPlace: function (place) {
    //   if (confirm("Are you sure you to delete this?")) {
    //     axios.delete(`/places/${place.id}`).then((response) => {
    //       console.log("Success", response);
    //       var index = this.places.indexOf(place);
    //       this.places.splice(index, 1);
    //     });
    //   }
    // },
  },
};
</script>

<template>
  <div class="home"></div>
  <h1>All Places</h1>
  <div v-for="place in places" v-bind:key="place.id">
    <p>{{ place.id }}</p>
    <dialog id="place-details">
      <form method="dialog">
        Name:
        <input type="text" v-model="editPlaceParams.name" />

        Address:
        <input type="text" v-model="editPlaceParams.address" />
        <br />
        <button v-on:click="destroyPlace(currentPlace)">Delete Place</button>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button>Close</button>
      </form>
    </dialog>
    <button v-on:click="showPlace(place)">More info</button>
  </div>
  <dialog id="place-details">
    <form method="dialog">
      <h1>Place info</h1>
      <p>Name: {{ currentPlace.name }}</p>
      <p>Address: {{ currentPlace.address }}</p>
      <button>Close</button>
    </form>
  </dialog>
  <h2>New Place</h2>
  <p>{{ newPlaceParams }}</p>
  <div>
    Name:
    <input type="text" v-model="newPlaceParams.name" />

    Address:
    <input type="text" v-model="newPlaceParams.address" />
    <br />
    <button v-on:click="createPlace()">Create</button>
  </div>
  <!-- <div>
    <h3>Update Place</h3>
    <p>
      Id:
      <input type="text" v-model="changePlace.id" />
    </p>
    <p>
      Name:
      <input type="text" v-model="changePlace.name" />
    </p>
    <p>
      Address:
      <input type="text" v-model="changePlace.address" />
    </p>
    <button v-on:click="updateMovie()">Commit</button>
  </div> -->
</template>
