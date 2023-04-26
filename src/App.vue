<template>
  <div id="app">
    <h1>Car Parking Reservation</h1>
    <ReservationForm @submitReservation="handleReservation" :reservation="selectedReservation" />
    <ReservationList @editReservation="editReservation" @cancelReservation="cancelReservation" :reservations="reservations" :waitingList="waitingList" />
  </div>
</template>

<script>
import ReservationForm from './components/ReservationForm.vue';
import ReservationList from './components/ReservationList.vue';

export default {
  name: 'App',
  components: {
    ReservationForm,
    ReservationList
  },
  data() {
    return {
      reservations: [],
      waitingList: [],
      selectedReservation: null
    };
  },
  methods: {
    handleReservation(newReservation) {
      if (this.selectedReservation) {
        // Edit existing reservation
        const index = this.reservations.findIndex((r) => r.id === this.selectedReservation.id);
        this.reservations.splice(index, 1, newReservation);
        this.selectedReservation = null;
      } else {
        // Add new reservation
        const availableSpots = this.getAvailableSpots(newReservation.date, newReservation.timeSlot);
        if (availableSpots.length > 0) {
          newReservation.spot = availableSpots[0];
          this.reservations.push(newReservation);
        } else {
          this.waitingList.push(newReservation);
        }
      }
    },
    getAvailableSpots(date, timeSlot) {
      const spots = [1, 2];
      this.reservations
        .filter((r) => r.date === date && r.timeSlot === timeSlot)
        .forEach((r) => {
          const index = spots.indexOf(r.spot);
          if (index > -1) {
            spots.splice(index, 1);
          }
        });
      return spots;
    },
    editReservation(reservation) {
      this.selectedReservation = reservation;
    },
    cancelReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id);
      this.reservations.splice(index, 1);

      // Move the first person from the waiting list to the reservations
      if (this.waitingList.length > 0) {
        const nextReservation = this.waitingList.shift();
        const availableSpots = this.getAvailableSpots(nextReservation.date, nextReservation.timeSlot);
        if (availableSpots.length > 0) {
          nextReservation.spot = availableSpots[0];
          this.reservations.push(nextReservation);
        }
      }
    }
  }
};
</script>
