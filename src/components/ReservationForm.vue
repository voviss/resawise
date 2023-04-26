<template>
    <div>
      <h2>Reservation Form</h2>
      <form @submit.prevent="submitForm">
        <div>
          <label for="name">Name:</label>
          <input type="text" id="name" v-model="form.name" required />
        </div>
        <div>
          <label for="date">Date:</label>
          <input type="date" id="date" v-model="form.date" :min="weekStart" :max="weekEnd" required />
        </div>
        <div>
          <label for="timeSlot">Time Slot:</label>
          <select id="timeSlot" v-model="form.timeSlot" required>
            <option value="08:00-13:00">08:00-13:00</option>
            <option value="13:00-18:00">13:00-18:00</option>
          </select>
        </div>
        <button type="submit">Submit</button>
      </form>
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
              {{ isAvailable(day, timeSlot) ? 'Available' : 'Unavailable' }}
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
          timeSlot: "08:00-13:00",
          spot: null
        },
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
      }
    },
    methods: {
      submitForm() {
        if (!this.form.id) {
          this.form.id = Date.now();
        }
        this.$emit("submitReservation", { ...this.form });
        this.resetForm();
      },
      resetForm() {
        this.form.id = null;
        this.form.name = "";
        this.form.date = "";
        this.form.timeSlot = "08:00-13:00";
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
    }
  }
};
</script>

  