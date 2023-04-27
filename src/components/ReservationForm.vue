<template>
  <div>
    <h2>Reservation Form</h2>
    <div>
      <label for="name">Name:</label>
      <input type="text" id="name" v-model="storedName" required class="input-name" />
    </div>
    <h2>Time Slots</h2>
    <table>
      <thead>
        <tr>
          <th>Time Slot</th>
          <th v-for="day in weekdays" :key="day">{{ day }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="timeSlot in timeSlots" :key="timeSlot">
          <td>{{ timeSlot }}</td>
          <td v-for="day in weekdays" :key="day + timeSlot">
            <button :disabled="false" @click="reserveSlot(day, timeSlot)"
              :class="{ 'unavailable': !isAvailable(day, timeSlot) }">
              {{ isAvailable(day, timeSlot) ? "Available" : "Unavailable" }}
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
  props: {
    reservation: Object
  },
  data() {
    return {
      form: {
        id: null,
        name: "",
        date: "",
        timeSlot: null,
        spot: null
      },
      storedName: localStorage.getItem("userName") || "",
      weekdays: ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"],
      timeSlots: ["08:00-13:00", "13:00-18:00"]
    };
  },
  computed: {
    weekStart() {
      const date = new Date();
      const day = date.getDay();
      const diff = date.getDate() - day + (day === 0 ? -6 : 1);
      return new Date(date.setDate(diff)).toISOString().split("T")[0];
    },
    weekEnd() {
      const date = new Date(this.weekStart);
      date.setDate(date.getDate() + 4);
      return date.toISOString().split("T")[0];
    }
  },
  watch: {
    reservation(newVal) {
      if (newVal) {
        this.form = { ...newVal };
      }
    },
    storedName(newName) {
      localStorage.setItem("userName", newName);
    }
  },
  methods: {
    submitForm() {
      this.$emit("submitReservation", { ...this.form });
      this.resetForm();
    },
    submitToWaitingList() {
      if (!this.form.id) {
        this.form.id = Date.now();
      }
      this.form.name = this.storedName;
      this.$emit("addToWaitingList", { ...this.form });
      this.resetForm();
    },
    resetForm() {
      this.form.id = null;
      this.form.name = "";
      this.form.date = "";
      this.form.timeSlot = "";
      this.form.spot = null;
    },
    isAvailable(day, timeSlot) {
      const date = this.getDateForDay(day);
      const availableSpots = this.getAvailableSpots(date, timeSlot);
      return availableSpots.length > 0;
    },
    getDateForDay(day) {
      const date = new Date(this.weekStart);
      const dayIndex = this.weekdays.indexOf(day);
      date.setDate(date.getDate() + dayIndex);
      return date.toISOString().split("T")[0];
    },
    getAvailableSpots(date, timeSlot) {
      const spots = [1, 2];
      this.$root.reservations
        .filter((r) => r.date === date && r.timeSlot === timeSlot)
        .forEach((r) => {
          const index = spots.indexOf(r.spot);
          if (index > -1) {
            spots.splice(index, 1);
          }
        });
      return spots;
    },
    reservationExists(name, date, timeSlot, reservations) {
      return reservations.some(form => {
        return (
          form.name === name &&
          form.date === date &&
          form.timeSlot === timeSlot
        );
      });
    },
    reserveSlot(day, timeSlot) {
      if (!this.storedName) {
        alert("Please enter your name before reserving a slot.");
        return;
      }
      if (!this.form.id) {
        this.form.id = Date.now();
      }
      this.form.name = this.storedName;
      this.form.date = this.getDateForDay(day);
      this.form.timeSlot = timeSlot;
      console.log("test");
      if (this.reservationExists(this.form.name, this.form.date, this.form.timeSlot, this.$root.reservations)) {
        alert("A reservation on your name is already done for the given date and time slot");
        return;
      }
      if (this.reservationExists(this.form.name, this.form.date, this.form.timeSlot, this.$root.waitingList)) {
        alert("You are in the waiting list already for the given date and time slot");
        return;
      }
      const availableSpots = this.getAvailableSpots(this.form.date, timeSlot);
      if (availableSpots.length === 0) {
        this.submitToWaitingList();
      } else {
        this.form.spot = availableSpots[0];
        this.submitForm();
      }

    },
    reserveSlotFromWaitingList(id, name, date, timeSlot, spot) {
      this.form.id = id;
      this.form.name = name;
      this.form.date = date;
      this.form.timeSlot = timeSlot;
      this.form.spot = spot;
      this.submitForm();
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

.input-name {
  width: 200px;
}
</style>