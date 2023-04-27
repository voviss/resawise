<template>
    <div>
      <h2>Reservations</h2>
      <table>
        <thead>
          <tr>
            <!-- <th>ID</th> -->
            <th>Name</th>
            <th>Date</th>
            <th>Time Slot</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(reservation) in reservations.filter(l => l.type === 'burger')" :key="reservation.id">
            <!-- <td>{{ reservation.id }}</td> -->
            <td>{{ reservation.name }}</td>
            <td>{{ reservation.date }}</td>
            <td>{{ reservation.timeSlot }}</td>
            <td>
              <!-- <button @click="editReservation(reservation)">Edit</button> -->
              <button @click="cancelReservation(reservation)">Cancel</button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <h2>Waiting List</h2>
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Date</th>
            <th>Time Slot</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="reservation in waitingList.filter(l => l.type === 'burger')" :key="reservation.id">
            <td>{{ reservation.name }}</td>
            <td>{{ reservation.date }}</td>
            <td>{{ reservation.timeSlot }}</td>
            <td>
              <button @click="cancelWaiting(reservation)">Cancel</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: "SportReservationList",
    props: {
      reservations: Array,
      waitingList: Array
    },
    methods: {
      cancelReservation(reservation) {
        this.$emit("removeReservation", reservation);
      },
      cancelWaiting(reservation) {
        //alert("cancelWaiting");
        this.$emit("removeWaitingList", reservation);
      }
    }
    
  };
  </script>

  
<style scoped>
button.cancel {
  background-color: #f44336;
  cursor: not-allowed;
}
</style>