<template>
  <div id="app" :data-theme="theme">
    <button @click="toggleTheme">{{ theme === "light" ? "Dark Mode" : "Light Mode" }}</button>
    <reservation-form @submitReservation="addReservation" :reservation="selectedReservation"></reservation-form>
    <reservation-list :reservations="reservations" @selectReservation="selectReservation" @deleteReservation="deleteReservation"></reservation-list>
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
      theme: "light",
      reservations: [],
      selectedReservation: null
    };
  },
  methods: {
    toggleTheme() {
      this.theme = this.theme === "light" ? "dark" : "light";
    },
    addReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id);
      if (index > -1) {
        this.reservations.splice(index, 1, reservation);
      } else {
        this.reservations.push(reservation);
      }
      this.selectedReservation = null;
    },
    selectReservation(reservation) {
      this.selectedReservation = reservation;
    },
    deleteReservation(reservation) {
      const index = this.reservations.findIndex((r) => r.id === reservation.id);
      if (index > -1) {
        this.reservations.splice(index, 1);
      }
    }
  }
};
</script>

<style>
  :root {
    --background-light: #ffffff;
    --background-dark: #121212;
    --text-light: #000000;
    --text-dark: #ffffff;
    --accent: #4caf50;
    --border-light: rgba(0, 0, 0, 0.12);
    --border-dark: rgba(255, 255, 255, 0.12);
  }

  [data-theme="dark"] {
    --background: var(--background-dark);
    --text: var(--text-dark);
    --border: var(--border-dark);
  }

  [data-theme="light"] {
    --background: var(--background-light);
    --text: var(--text-light);
    --border: var(--border-light);
  }

  body {
    background-color: var(--background);
    color: var(--text);
    font-family: "Roboto", sans-serif;
    margin: 0;
    padding: 0;
  }

  input,
  select,
  button {
    font-family: inherit;
  }

  h2 {
    margin-bottom: 1rem;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  table {
    border-collapse: collapse;
    width: 100%;
  }

  th,
  td {
    border: 1px solid var(--border);
    padding: 0.5rem;
    text-align: center;
  }

  th {
    background-color: var(--accent);
    color: var(--text-dark);
  }
</style>
