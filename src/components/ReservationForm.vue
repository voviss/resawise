<template>
    <div class="reservation-form">
      <h2>{{ form.id ? "Edit Reservation" : "New Reservation" }}</h2>
      <div>
        <label for="name">Name:</label>
        <input
          type="text"
          id="name"
          v-model="storedName"
          @input="storeUserName(storedName)"
          placeholder="Enter your name"
        />
      </div>
      <table class="time-slots">
        <thead>
          <tr>
            <th></th>
            <th v-for="weekday in weekdays" :key="weekday">{{ weekday }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="timeSlot in timeSlots" :key="timeSlot">
            <td>{{ timeSlot }}</td>
            <td v-for="weekday in weekdays" :key="weekday + '-' + timeSlot">
              <button
                :class="{ 'unavailable': !isAvailable(weekday, timeSlot) }"
                @click="reserveSlot(weekday, timeSlot)"
                :disabled="!storedName"
              >
                {{ isAvailable(weekday, timeSlot) ? "Reserve" : "Add to Waiting List" }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: "ReservationForm",
    data() {
      return {
        storedName: localStorage.getItem("userName") || "",
        weekdays: ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"],
        timeSlots: ["08:00-13:00", "13:00-18:00"]
      };
    },
    methods: {
      storeUserName(name) {
        localStorage.setItem("userName", name);
      },
      isAvailable(weekday, timeSlot) {
        const date = this.getDateForDay(weekday);
        const availableSpots = this.getAvailableSpots(date, timeSlot);
        return availableSpots.length > 0;
      },
      getDateForDay(day) {
        const currentDay = new Date().getDay();
        const targetDay = this.weekdays.indexOf(day) + 1;
        const diff = targetDay - currentDay;
        const targetDate = new Date();
        targetDate.setDate(targetDate.getDate() + diff);
        return targetDate.toISOString().slice(0, 10);
      },
      getAvailableSpots() {
        // This function should be updated to get the available spots based on the current reservations
        const allSpots = [1, 2];
        const reservedSpots = [];
        return allSpots.filter((spot) => !reservedSpots.includes(spot));
      },
      reserveSlot(weekday, timeSlot) {
        if (!this.storedName) {
          alert("Please enter your name before reserving a slot.");
          return;
        }
        const date = this.getDateForDay(weekday);
        const availableSpots = this.getAvailableSpots(date, timeSlot);
        if (availableSpots.length > 0) {
          const reservation = {
            id: Date.now(),
            name: this.storedName,
            date: date,
            timeSlot: timeSlot,
            spot: availableSpots[0]
          };
          this.$emit("submitReservation", reservation);
        } else {
          const waitingListEntry = {
            name: this.storedName,
            date: date,
            timeSlot: timeSlot
          };
          this.$emit("addToWaitingList", waitingListEntry);
}
}
},
computed: {
reservationsByDate() {
return this.reservations.reduce((accumulator, reservation) => {
const date = reservation.date;
if (!accumulator[date]) {
accumulator[date] = [];
}
accumulator[date].push(reservation);
return accumulator;
}, {});
},
waitingList() {
return this.waitingListEntries;
}
},
props: {
form: {
type: Object,
default: () => ({})
},
reservations: {
type: Array,
default: () => []
},
waitingListEntries: {
type: Array,
default: () => []
}
}
};
</script>

<style scoped>
.reservation-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 2rem;
}

label {
  display: block;
  margin-bottom: 0.5rem;
}

input {
  width: 100%;
  padding: 0.5rem;
  margin-bottom: 1rem;
}

.time-slots {
  width: 100%;
  border-collapse: collapse;
}

.time-slots th,
.time-slots td {
  border: 1px solid #ccc;
  padding: 0.5rem;
  text-align: center;
}

.time-slots th {
  background-color: #444;
  font-weight: bold;
}

button {
  background-color: #4caf50;
  color: white;
  padding: 0.5rem;
  font-size: 1rem;
  border: none;
  cursor: pointer;
  width: 100%;
}

button:hover {
  background-color: #45a049;
}

button.unavailable {
  background-color: #f44336;
  cursor: not-allowed;
}
</style>