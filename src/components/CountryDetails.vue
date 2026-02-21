<script setup>
import { ref, onMounted, computed } from "vue"
import axios from 'axios';

const result = ref([]);
// const resultLength = ref(0)
const searchPara = ref("")
const isLoading = ref(true);


const finalResult = computed(() => {
  if (searchPara.value && searchPara.value.length > 0) {
    console.log("ur fucntion")

    const matchPara = result.value.filter((x) => x.name.common.toLowerCase().includes(searchPara.value.toLowerCase()))
    return matchPara
  }
  else {
    return result.value
  }

})

onMounted(async () => {
  try {
    const response = await axios.get('https://restcountries.com/v3.1/all?fields=name,flags,maps,continents,area,timezones,population,languages,currencies,ccn3');

    result.value = response.data
    console.log("result is ", result.value)
    // resultLength.value = result.value.length
  }
  catch (error) {
    console.log(error)
  } finally {
    isLoading.value = false; // ✅ Hide loading when data is fetched
  }
})




</script>

<template>
  <div class="container-fluid m-0 p-0">
    <div class="pt-3 pb-4 mb-4 topHeader ">
      <div class="container ">
        <div class="d-flex ">
          <i class="bi bi-globe-americas text-dark pe-4" style="font-size: 40px;"></i>
          <div></div>
          <div>
            <h1 class="webTitle text-danger fw-bold">Explore Countries: <span class="text-dark">Facts & Insights</span>
            </h1>
            <p class="col-md-12 text-dark ">Get key details about different countries, including
              population, area,
              timezone, languages, and currency. Data fetched in real-time from a reliable API source.</p>
            <input v-model.trim="searchPara" class="form-control" type="text" placeholder="Search Country">
          </div>

        </div>

      </div>
    </div>
    <div class="container">
      <div class="text-center mb-4" v-if="isLoading">
        <button class="btn btn-success" type="button" disabled>
          <span class="spinner-grow spinner-grow-sm" aria-hidden="true"></span>
          <span role="status" style="margin-left: 7px;">Data is Loading...</span>
        </button>
      </div>
      <div class="text-center mb-4" v-else><span class="badge text-bg-success">{{ finalResult.length }}</span> Result
        found
      </div>
      <div class="row">
        <div class="col-6 col-sm-6 col-md-4 col-lg-3 mb-4  " v-for="cList of finalResult" :key="cList.ccn3">
          <div class="card shadow" style="width: 18rem;">
            <div class="d-flex justify-content-between px-3 pt-3">
              <div>
                <h1 class="card-title  "><a :href="cList.maps.openStreetMaps" target="_blank" class="text-dark"
                    :title="cList.name.common + ' map'">{{ cList.name.common }}</a></h1>
                <p>{{ cList.continents[0] }} <a :href="cList.maps.openStreetMaps" target="_blank" class="text-success"
                    :title="cList.name.common + ' map'">
                    <i class="bi bi-geo-alt-fill"></i></a></p>
              </div>
              <a target="_blank" :href="cList.flags.svg"><img :src="cList.flags.svg" class="card-img-top setFlag"
                  :alt="cList.name.common"></a>
            </div>

            <div class="card-body pt-0">
              <div class="card-text smallInfo">
                <div class="text-center">Area is {{ cList.area }} km<sup class="p-0">2</sup></div>
                <div class="text-center">Timezone {{ cList.timezones[0] }}</div>
                <div class="text-center">Population {{ cList.population }}</div>
              </div>


              <hr>
              <div><strong class="">Languages :</strong></div>
              <span v-for="(lang, index) in cList.languages" :key="index">
                {{ lang }},
              </span>

              <hr>
              <div>
                <strong class="text-dark">Currency :</strong>
                <span v-for="(currSymbol, index) in cList.currencies" class="badge text-bg-success mx-1" :key="index">
                  <span>{{ currSymbol.symbol }}</span>
                </span>
              </div>
              <div class="pt-2 currName">
                <span v-for="(curr, index) in cList.currencies" class="border mx-1 px-2 d-inline bg-light-subtle"
                  :key="index">
                  {{ curr.name }}
                </span>
              </div>
              <div class="mt-3">

              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>

</template>


<style scoped>
.setFlag {
  width: 50px;
  height: 30px;
  border: 1px solid #ccc;
}

.card-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 0px;
}

.smallInfo td,
.smallInfo th {
  text-align: center;
}

.currName {
  font-size: 14px !important;
}

.card {
  width: 100% !important;
  min-height: 370px;
}

a {
  text-decoration: none;
}

.webTitle {
  font-size: 25px;
}

.topHeader {
  background: #c1e0dc;
}

[v-cloak] {
  display: none;
}
</style>
