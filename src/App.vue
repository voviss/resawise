<template>
  <div id="app" class="app">
    <h1>ResaWise</h1>
    <ReservationForm ref="reservationForm" :reservation="selectedReservation" @submitReservation="addReservation"
      @addToWaitingList="addWaitingList" />
    <ReservationList :reservations="reservations" :waitingList="waitingList" @removeReservation="removeReservation"
      @editReservation="selectReservation"
      @removeWaitingList="removeFromWaitingList"
       />
  </div>
</template>

<script>
import ReservationForm from "./components/ReservationForm.vue";
import ReservationList from "./components/ReservationList.vue";

export default {
  name: "App",
  components: {
    ReservationForm,
    ReservationList
  },
  data() {
    return {
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

        // Compare ids
        return a.id - b.id;
      });

      return forms; // Return the sorted array
    },
    addReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id);
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
    removeReservation(id) {
      const index = this.reservations.findIndex((r) => r.id === id);
      const deletedReservations = this.reservations.splice(index, 1);
      this.updateFromWaitingList(deletedReservations[0]);
    },
    removeFromWaitingList(id) {
      const index = this.waitingList.findIndex((r) => r.id === id);
      this.waitingList.splice(index, 1);
    },
    updateFromWaitingList(deletedReservation) {
      const firstWaitingReservation = this.waitingList.find((r) => r.date === deletedReservation.date && r.timeSlot === deletedReservation.timeSlot);
      if (firstWaitingReservation) {
        this.$refs.reservationForm.reserveSlotFromWaitingList(
          firstWaitingReservation.id,
          firstWaitingReservation.name,
          firstWaitingReservation.date,
          firstWaitingReservation.timeSlot,
          deletedReservation.spot);
        this.removeFromWaitingList(firstWaitingReservation.id);
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
