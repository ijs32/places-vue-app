<template>
  <div class="home">
    <div v-for="place in places" :key="place.id">
      <h1>Name: {{ place.name }}</h1>
      <p>Name: {{ place.address }}</p>
      <dialog id="place-details">
        <form method="dialog">
          <h1>Place Info:</h1>
          <p>
            Name:
            <input type="text" v-model="currentPlace.name" />
          </p>
          <p>
            Address:
            <input type="text" v-model="currentPlace.address" />
          </p>
          <button v-on:click="updatePlace(currentPlace)">Update Place</button>
          <button v-on:click="destroyPlace(currentPlace)">Delete</button>
          <button>close</button>
        </form>
      </dialog>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <h1>New Place</h1>
    <p>
      Name:
      <input type="text" v-model="newPlacesParams.name" />
    </p>
    <p>
      Address:
      <input type="text" v-model="newPlacesParams.address" />
    </p>
    <button v-on:click="createPlace()">Create Place</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlacesParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    axios
      .get("/places")
      .then((response) => {
        this.places = response.data;
        console.log("Successfully indexed places");
      })
      .catch((error) => {
        this.errors = error.response.data.errors;
      });
  },
  methods: {
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlacesParams)
        .then((response) => {
          console.log("Success", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    updatePlace: function (places) {
      axios
        .patch("/places/" + places.id, places)
        .then((response) => {
          console.log("Success", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (places) {
      axios
        .delete("/places/" + places.id)
        .then((response) => {
          console.log("Success!", response.data);
          var index = this.places.indexOf(places);
          this.places.splice(index, 1);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      console.log(place);
      document.querySelector("#place-details").showModal();
    },
  },
};
</script>
