<template>
  <div id="app" class="app">
    <h1>ResaWise</h1>
    <div class="tabs">
      <button :class="{ active: currentTab === 'parking' }" @click="currentTab = 'parking'">Car Parking</button>
      <button :class="{ active: currentTab === 'electric' }" @click="currentTab = 'electric'">Electric charging car parking</button>
      <button :class="{ active: currentTab === 'sport' }" @click="currentTab = 'sport'">Sport Time Slots</button>
      <button :class="{ active: currentTab === 'burger' }" @click="currentTab = 'burger'">Burger Time Slots</button>
    </div>
    <div v-if="currentTab === 'parking'">
      <ReservationForm ref="reservationForm" :reservation="selectedReservation" @submitReservation="addReservation"
        @addToWaitingList="addWaitingList" />
      <ReservationList :reservations="reservations" :waitingList="waitingList" @removeReservation="removeReservation"
        @editReservation="selectReservation" @removeWaitingList="removeFromWaitingList" />
    </div>
    <div v-if="currentTab === 'electric'">
      <ElectricReservationForm ref="reservationForm" :reservation="selectedReservation" @submitReservation="addReservation"
        @addToWaitingList="addWaitingList" />
      <ElectricReservationList :reservations="reservations" :waitingList="waitingList" @removeReservation="removeReservation"
        @editReservation="selectReservation" @removeWaitingList="removeFromWaitingList" />
    </div>
    <div v-if="currentTab === 'sport'">
      <SportReservationForm ref="reservationForm" :reservation="selectedReservation" @submitReservation="addReservation"
        @addToWaitingList="addWaitingList" />
      <SportReservationList :reservations="reservations" :waitingList="waitingList" @removeReservation="removeReservation"
        @editReservation="selectReservation" @removeWaitingList="removeFromWaitingList" />
    </div>
    <div v-if="currentTab === 'burger'">
      <BurgerReservationForm ref="reservationForm" :reservation="selectedReservation" @submitReservation="addReservation"
        @addToWaitingList="addWaitingList" />
      <BurgerReservationList :reservations="reservations" :waitingList="waitingList" @removeReservation="removeReservation"
        @editReservation="selectReservation" @removeWaitingList="removeFromWaitingList" />
    </div>

  </div>
</template>

<script>
import ReservationForm from "./components/parking/ReservationForm.vue";
import ReservationList from "./components/parking/ReservationList.vue";
import ElectricReservationForm from "./components/electric/ReservationForm.vue";
import ElectricReservationList from "./components/electric/ReservationList.vue";
import SportReservationForm from "./components/sport/ReservationForm.vue";
import SportReservationList from "./components/sport/ReservationList.vue";
import BurgerReservationForm from "./components/burger/ReservationForm.vue";
import BurgerReservationList from "./components/burger/ReservationList.vue";

export default {
  name: "App",
  components: {
    ReservationForm,
    ReservationList,
    SportReservationForm,
    SportReservationList,
    BurgerReservationForm,
    BurgerReservationList,
    ElectricReservationForm,
    ElectricReservationList,
  },
  data() {
    return {
      currentTab: "parking",
      reservations: [],
      selectedReservation: null,
      waitingList: []
    };
  },
  methods: {
    sortReservations(forms) {
      forms.sort((a, b) => {
        // Compare dates
        let dateComparison = new Date(a.date).getTime() - new Date(b.date).getTime();
        if (dateComparison !== 0) {
          return dateComparison;
        }

        // Compare timeSlots
        let timeSlotComparison = a.timeSlot.localeCompare(b.timeSlot);
        if (timeSlotComparison !== 0) {
          return timeSlotComparison;
        }

        // Compare spots
        let aSpot = a.spot === null || a.spot === "" ? Number.MAX_SAFE_INTEGER : a.spot;
        let bSpot = b.spot === null || b.spot === "" ? Number.MAX_SAFE_INTEGER : b.spot;
        let spotComparison = aSpot - bSpot;
        if (spotComparison !== 0) {
          return spotComparison;
        }

        // Compare ids
        return a.id - b.id;
      });

      return forms; // Return the sorted array
    },
    addReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id && r.type === reservation.type);
      if (index === -1) {
        this.reservations.push(reservation);
      } else {
        this.reservations.splice(index, 1, reservation);
      }
      this.sortReservations(this.reservations);
    },
    addWaitingList(reservation) {
      this.waitingList.push(reservation);
      this.sortReservations(this.waitingList);
    },
    removeReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id && r.type === reservation.type);
      const deletedReservations = this.reservations.splice(index, 1);
      this.updateFromWaitingList(deletedReservations[0]);
    },
    removeFromWaitingList(reservation) {
      const index = this.waitingList.findIndex((r) => r.id === reservation.id && r.type === reservation.type);
      this.waitingList.splice(index, 1);
    },
    updateFromWaitingList(deletedReservation) {
      const firstWaitingReservation = this.waitingList.find((r) => r.date === deletedReservation.date && r.timeSlot === deletedReservation.timeSlot && r.type === deletedReservation.type);
      if (firstWaitingReservation) {
        this.$refs.reservationForm.reserveSlotFromWaitingList(
          firstWaitingReservation.id,
          firstWaitingReservation.name,
          firstWaitingReservation.date,
          firstWaitingReservation.timeSlot,
          deletedReservation.spot);
        this.removeFromWaitingList(firstWaitingReservation);
      }
    },
    selectReservation(reservation) {
      this.selectedReservation = reservation;
    },
    unselectReservation() {
      this.selectedReservation = null;
    }
  }
};
</script>

<style>
body {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  background-color: #222;
  color: white;
  margin: 0;
}

.app {
  max-width: 960px;
  margin: 0 auto;
  padding: 2rem;
}

h1 {
  text-align: center;
  margin-bottom: 2rem;
}

input[type="text"] {
  background-color: #333;
  color: white;
  border: 1px solid #444;
}

input[type="text"]:focus {
  outline: none;
  border: 1px solid #777;
}

button {
  background-color: #444;
  color: white;
  border: none;
  cursor: pointer;
  transition: background-color 0.2s;
}

button:hover {
  background-color: #555;
}

button:disabled {
  background-color: #333;
  cursor: default;
}
</style>
