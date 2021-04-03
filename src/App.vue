<script>
import axios from "axios";


export default {
  name: "App",
 
  data() {
    return {
      countries: {},
      data: {},
      selectedCountry: "Global",
      confirmed:0,
      deaths:0,
      recovered:0
    };
  },
  filters: {
    numberFormat(num) {
      return num.toLocaleString();
    },
  },
  methods: {
    getCountries() {
      axios.get("https://api.covid19api.com/countries").then((res) => {
        this.countries = res.data;
      });
    },
    getSummary() {
      axios.get("https://api.covid19api.com/summary").then((res) => {
        const data= res.data
        this.data = data;
        // console.log(this.data)
        this.confirmed=data.Global.TotalConfirmed
        this.deaths=data.Global.TotalDeaths
        this.recovered=data.Global.TotalRecovered

      });
    },
    getLocation(){
      axios
      .get("https://ipapi.co/json/")
      .then((response) => {
        let res = response.data;

        const data = this.data.Countries.filter(item=>item.Slug==res.country_name.toLowerCase())
      // console.log(data)
        this.selectedCountry=res.country_name
        this.confirmed=data[0].TotalConfirmed
        this.deaths=data[0].TotalDeaths
        this.recovered=data[0].TotalRecovered
       
        // setlocation(res.country_name.toLowerCase());

        // const newdata = data.filter(
        //   (item) => item.Slug === res.country_name.toLowerCase()
        // );

       
      })
      .catch((error) => {
        console.log(error);
      });
    },
    getCountryInfo(){
      const data = this.data.Countries.filter(item=>item.Slug==this.selectedCountry)
      // console.log(data)
        this.confirmed=data[0].TotalConfirmed
        this.deaths=data[0].TotalDeaths
        this.recovered=data[0].TotalRecovered
    }

  },
  mounted() {
    this.getCountries(), this.getSummary();
  },
};
</script>

<template>
  <div id="app">
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <div class="container">
      <div class="row">
        <select
          v-model="selectedCountry"
          class="mt-2 form-control form-control-sm"
          @change="getCountryInfo"
        >
          <option value="Global">Global</option>
          <option
            :value="country.Slug"
            v-for="country in countries"
            :key="country.Slug"
          >
            {{ country.Country }}
          </option>
        </select>
      </div>
      <h1 class="display-4">{{selectedCountry.toUpperCase()}}</h1>
       <div
          class="badge badge-primary p-3"
          style="cursor: pointer"
          @click="getLocation"
        >
          Get your Country Information
        </div>
      <div class="mt-4 row d-flex justify-content-between main-container">
        <span class="badge badge-primary d-flex flex-column"
          ><span> Confirmed</span>
          <span class="span mt-3">{{
            confirmed | numberFormat
          }}</span></span
        >
        <span class="badge badge-success d-flex flex-column"
          ><span> Deaths</span>
          <span class="span mt-3">{{ deaths | numberFormat }}</span></span
        >
        <span class="badge badge-danger d-flex flex-column"
          ><span> Recovered</span>
          <span class="span mt-3">{{ recovered | numberFormat}}</span></span
        >
      </div>
      <div class="row mt-5">
        <table class="table table-dark">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">Country</th>
              <th scope="col">Confirmed</th>
              <th scope="col">Deaths</th>
              <th scope="col">Recovered</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(countryinfo,index) in data.Countries" :key="countryinfo.index">
              <th scope="row">{{index+1}}</th>
              <td>{{ countryinfo.Country }}</td>
              <td>{{ countryinfo.TotalConfirmed }}</td>
              <td>{{ countryinfo.TotalDeaths }}</td>
              <td>{{ countryinfo.TotalRecovered }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>



<style>
html,
body {
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background: #2c3e50;
  /* margin-top: 60px; */
}
.span {
  font-size: 1rem;
}
.main-container > span {
  width: 25%;
  height: 4rem;
}
h1{
  color: #fff;
}
</style>
