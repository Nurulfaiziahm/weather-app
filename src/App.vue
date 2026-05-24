<template>
  <div class="container">
    <h1>Prakiraan Cuaca Jakarta</h1>

    <div v-for="(group, date) in groupedData" :key="date" class="date-block">
      <h2>{{ formatDate(date) }}</h2>

      <table>
        <thead>
          <tr>
            <th>Jam</th>
            <th>Suhu</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(item, index) in group" :key="index">
            <td>
              {{
                new Date(item.time).toLocaleString('en-US', {
                  hour: 'numeric',
                  minute: '2-digit',
                  hour12: true,
                })
              }}
            </td>

            <td :class="item.temperature > 30 ? 'temp-hot' : 'temp-cold'">
              {{ item.temperature }} °C
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      weatherData: [],
    }
  },

  computed: {
    groupedData() {
      const groups = {}

      this.weatherData.forEach((item) => {
        const date = item.time.split('T')[0]

        if (!groups[date]) {
          groups[date] = []
        }

        groups[date].push(item)
      })

      return groups
    },
  },

  methods: {
    formatDate(date) {
      return new Date(date).toLocaleDateString('id-ID', {
        weekday: 'long',
        day: 'numeric',
        month: 'long',
        year: 'numeric',
      })
    },
  },

  mounted() {
    fetch(
      'https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m',
    )
      .then((response) => response.json())
      .then((data) => {
        this.weatherData = data.hourly.time.map((time, index) => ({
          time: time,
          temperature: data.hourly.temperature_2m[index],
        }))
      })
      .catch((error) => {
        console.log(error)
      })
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: linear-gradient(to right, #4facfe, #00f2fe);
  min-height: 100vh;
  padding: 30px;
}

.container {
  max-width: 950px;
  margin: auto;
  background: white;
  padding: 30px;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
}

h1 {
  text-align: center;
  margin-bottom: 35px;
  color: #333;
  font-size: 34px;
}

.date-block {
  margin-bottom: 40px;
}

.date-block h2 {
  margin-bottom: 15px;
  color: #007bff;
  padding-bottom: 8px;
  border-bottom: 3px solid #007bff;
  font-size: 24px;
}

table {
  width: 100%;
  border-collapse: collapse;
  overflow: hidden;
  border-radius: 15px;
}

thead {
  background: #007bff;
  color: white;
}

th {
  padding: 15px;
  font-size: 18px;
}

td {
  padding: 14px;
  text-align: center;
  border-bottom: 1px solid #ddd;
  font-size: 16px;
}

tbody tr {
  transition: 0.3s;
}

tbody tr:nth-child(even) {
  background: #f8f9fa;
}

tbody tr:hover {
  background: #d6ecff;
  transform: scale(1.01);
}

.temp-hot {
  color: red;
  font-weight: bold;
}

.temp-cold {
  color: blue;
  font-weight: bold;
}

@media (max-width: 600px) {
  .container {
    padding: 15px;
  }

  h1 {
    font-size: 24px;
  }

  .date-block h2 {
    font-size: 20px;
  }

  th,
  td {
    font-size: 14px;
    padding: 10px;
  }
}
</style>
