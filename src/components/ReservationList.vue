<template>
    <div>
      <h2>Reservations</h2>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Date</th>
            <th>Time Slot</th>
            <th>Spot</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="reservation in reservations" :key="reservation.id">
            <td>{{ reservation.id }}</td>
            <td>{{ reservation.name }}</td>
            <td>{{ reservation.date }}</td>
            <td>{{ reservation.timeSlot }}</td>
            <td>{{ reservation.spot }}</td>
            <td>
              <button @click="editReservation(reservation)">Edit</button>
              <button @click="cancelReservation(reservation)">Cancel</button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <h2>Waiting List</h2>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Date</th>
            <th>Time Slot</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="reservation in waitingList" :key="reservation.id">
            <td>{{ reservation.id }}</td>
            <td>{{ reservation.name }}</td>
            <td>{{ reservation.date }}</td>
            <td>{{ reservation.timeSlot }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: "ReservationList",
    props: {
      reservations: Array,
      waitingList: Array
    },
    methods: {
      editReservation(reservation) {
        this.$emit("editReservation", reservation);
      },
      cancelReservation(reservation) {
        this.$emit("cancelReservation", reservation);
      }
    }
  };
  </script>