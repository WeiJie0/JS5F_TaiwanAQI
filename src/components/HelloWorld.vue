<template>
  <div>
    <loading loader="bars" :active.sync="isLoading" backgroundColor="black" color="#4717F6"></loading>
    <div class="wrap bg-dark">
      <div class="container">
        <nav class="navbar navbar-expand-sm navbar-viod bg-dark">
          <div class="d-flex flex-column mx-auto mx-md-0">
            <h2 class="text-primary">空氣品質指標 (AQI)</h2>
            <div class="form-group citySelect">
              <select
                id="inputState"
                class="form-control rounded-0 bg-dark text-white"
                v-model="city"
              >
                <option v-for="city in cityItem" :key="city">{{city}}</option>
              </select>
            </div>
          </div>
          <div class="legend d-none d-md-flex ml-auto">
            <table class="table table-sm table-bordered text-center" style="height: 100px;">
              <tr>
                <th class="align-middle text-success">0~50</th>
                <th class="align-middle text-warning">51~100</th>
                <th class="align-middle text-orange">101~150</th>
                <th class="align-middle text-danger">151~200</th>
                <th class="align-middle text-primary">201~300</th>
                <th class="align-middle text-purple">301~400</th>
              </tr>
              <tr>
                <td class="align-middle text-white">良好</td>
                <td class="align-middle text-white">普通</td>
                <td class="align-middle text-white">
                  對敏感族群
                  <br>不健康
                </td>
                <td class="align-middle text-white">
                  對所有族群
                  <br>不健康
                </td>
                <td class="align-middle text-white">非常不健康</td>
                <td class="align-middle text-white">危害</td>
              </tr>
            </table>
          </div>
          <div class="mobileLegend flex-column d-md-none mx-auto">
            <button
              class="btn btn-outline-info"
              type="button"
              data-toggle="modal"
              data-target="#exampleModal"
            >空氣品質指標說明</button>
          </div>
          <div
            class="modal"
            id="exampleModal"
            tabindex="-1"
            role="dialog"
            aria-labelledby="exampleModalLabel"
            aria-hidden="true"
          >
            <div class="modal-dialog modal-dialog-centered animated fadeIn bg-dark" role="document">
              <div class="modal-content border-0">
                <div class="modal-body bg-dark">
                  <table class="table table-sm text-center">
                    <tr>
                      <th class="text-info">AQI係數</th>
                      <th class="text-info">說明</th>
                    </tr>
                    <tr>
                      <td class="align-middle text-success">0~50</td>
                      <td class="align-middle text-white">良好</td>
                    </tr>
                    <tr>
                      <td class="align-middle text-warning">51~100</td>
                      <td class="align-middle text-white">普通</td>
                    </tr>
                    <tr>
                      <td class="align-middle text-orange">101~150</td>
                      <td class="align-middle text-white">對敏感族群不健康</td>
                    </tr>
                    <tr>
                      <td class="align-middle text-danger">151~200</td>
                      <td class="align-middle text-white">對所有族群不健康</td>
                    </tr>
                    <tr>
                      <td class="align-middle text-primary">201~300</td>
                      <td class="align-middle text-white">非常不健康</td>
                    </tr>
                    <tr>
                      <td class="align-middle text-purple">301~400</td>
                      <td class="align-middle text-white">危害</td>
                    </tr>
                  </table>
                </div>
              </div>
            </div>
          </div>
        </nav>
        <div class="container">
          <div class="d-flex-column d-md-flex justify-content-between align-items-end">
            <h1 class="text-primary">{{city}}</h1>
            <h5 class="text-primary">更新時間為 {{updata}}</h5>
          </div>

          <div class="row mt-5">
            <div class="col-md-4">
              <div class="row text-center" :class="aqiColor(siteName.AQI)">
                <div class="col-8 h1">{{siteName.SiteName}}</div>
                <div class="col-4 h1">{{siteName.AQI}}</div>
              </div>
              <table class="table border-0 table-hover siteDetail border text-white">
                <tbody>
                  <tr>
                    <td>
                      臭氧
                      <small>O3 (ppb)</small>
                    </td>
                    <td class="text-center">{{siteName.O3}}</td>
                  </tr>
                  <tr>
                    <td>
                      懸浮微粒
                      <small>PM10 (μg/m³)</small>
                    </td>
                    <td class="text-center">{{siteName.PM10}}</td>
                  </tr>
                  <tr>
                    <td>
                      細懸浮微粒
                      <small>PM2.5 (μg/m³)</small>
                    </td>
                    <td class="text-center">{{siteName['PM2.5']}}</td>
                  </tr>
                  <tr>
                    <td>
                      一氧化碳
                      <small>CO (ppm)</small>
                    </td>
                    <td class="text-center">{{siteName.CO}}</td>
                  </tr>
                  <tr>
                    <td>
                      二氧化硫
                      <small>SO2 (ppb)</small>
                    </td>
                    <td class="text-center">{{siteName.SO2}}</td>
                  </tr>
                  <tr>
                    <td>
                      二氧化氮
                      <small>NO2 (ppb)</small>
                    </td>
                    <td class="text-center">{{siteName.NO2}}</td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="col-md-8">
              <div class="row justify-content-center">
                <div
                  class="col-md-4 section card mx-3 bg-transparent"
                  v-for="item in citySection[nowPage]"
                  @click.prevent="detailSelect(item)"
                >
                  <div class="card-body border row py-2 my-2">
                    <table class="table table-borderless m-0 p-0 text-center">
                      <tr>
                        <th class="siteName h4 text-white align-middle">{{item.SiteName}}</th>
                        <th
                          width="50"
                          class="siteAQI h3 align-middle"
                          :class="aqiColor(item.AQI)"
                          v-if="item.AQI"
                        >{{item.AQI}}</th>
                        <th width="60" class="siteAQI h3 text-danger" v-else>X</th>
                      </tr>
                    </table>
                  </div>
                </div>
              </div>
              <nav class="d-flex justify-content-center my-3" v-if="citySection.length > 1 ">
                <ul class="pagination border-0">
                  <li
                    class="page-item"
                    v-for="page in citySection.length"
                    :key="page"
                    :class="{'active':nowPage == page-1}"
                  >
                    <a class="page-link" href="#" @click.prevent="nowPage = page - 1">{{page}}</a>
                  </li>
                </ul>
              </nav>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      isLoading: false,
      city: "基隆市",
      data: [],
      siteName: [],
      nowPage: 0,
      updata: ""
    };
  },
  methods: {
    getData() {
      let vm = this;
      const api =
        "https://cors-anywhere.herokuapp.com/http://opendata2.epa.gov.tw/AQI.json";
      vm.isLoading = true;
      this.$http.get(api).then(response => {
        vm.isLoading = false;
        vm.data = response.data;
        vm.siteName = vm.detailCity();
        vm.updata = vm.getTime();
        // console.log(response.data);
      });
    },
    getTime() {
      const now = new Date();
      let timeStr = `${now.getFullYear()}-${now.getMonth()}-${now.getDate()} 
      ${now.getHours()}:${now.getMinutes()}`;
      return timeStr;
    },
    aqiColor(aqi) {
      let className = "";
      switch (true) {
        case aqi <= 50:
          return (className = "text-success");
          break;
        case aqi >= 51:
          return (className = "text-warning");
          break;
        case aqi > 101:
          return (className = "text-orange");
          break;
        case aqi > 151:
          return (className = "text-danger");
          break;
        case aqi > 201:
          return (className = "text-primary");
          break;
        case aqi > 301:
          return (className = "text-purple");
          break;
        default:
          return className;
          break;
      }
    },
    detailCity(city = "基隆") {
      let vm = this;
      return vm.data.find(item => {
        return item.SiteName === city;
      });
    },
    detailSelect(site) {
      console.log(site);
      let vm = this;
      vm.siteName = site;
      vm.getTime();
      // vm.getData();
    }
  },
  computed: {
    cityItem() {
      let vm = this;
      const newCity = [];
      for (let i = 0; i < vm.data.length; i++) {
        newCity[i] = vm.data[i].County;
      }
      return newCity.filter((item, index, arr) => {
        return arr.indexOf(item) === index;
      });
    },
    citySection() {
      let vm = this;
      let newCity = [];
      if (vm.data) {
        newCity = vm.data.filter(item => {
          return item.County == vm.city;
        });
      }

      let newPage = [];
      newCity.forEach((item, index) => {
        if (index % 8 === 0) {
          newPage.push([]);
        }
        let page = parseInt(index / 8);
        newPage[page].push(item);
      });
      return newPage;
    }
  },
  created() {
    this.getData();
    this.getTime();
  }
};
</script>

<style>
.wrap {
  height: 100vh;
  width: 100vw;
}
.legend th {
  height: 54px;
  width: 121px;
}
.legend td {
  height: 56px;
}
.section :hover {
  background: radial-gradient(circle, #0e0b16, #4717f6);
}
.section .card-body :hover {
  background: transparent !important;
}
</style>

