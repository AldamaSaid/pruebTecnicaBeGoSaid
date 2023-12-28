<template>
  <div style=" width: 50%" class="row justify-content-center text-center my-4">
    <div v-if="activeTabId=='Upcoming'" class="col">
    </div>
    <div
        v-else
        class="col"
        tabindex="0"
        @click="activeTabId='Upcoming'"
        @keyup.enter="activeTabId='Upcoming'"
    ><img src="../img/navigate_before24dp.svg" alt="Icono de Resumen"
          class="icon-svg">
    </div>
    <div class="col-5 text-white"><h6> {{ cargo_info }}</h6></div>
    <div class="col"><img src="../img/bell.svg" alt="Icono de Resumen" class="icon-svg">
    </div>

  </div>
  <div style="width: 40%" class="my-2">
    <ul class="nav nav-pills justify-content-center">
      <li
          v-for="tab in tabs"
          :key="tab.id"
          class="nav-item"
          :class="{ 'active': activeTabId === tab.id }"
      >
        <a
            class="nav-link"
            @click="setActiveTab(tab.id)"
            href="#"
            style="position: relative; color: yellow"
        >
          {{ tab.label }}
          <div
              v-if="activeTabId === tab.id"
              class="underline"
              :style="{ width: `${tab.label.length * 10}px` }"
          ></div>
        </a>
      </li>
    </ul>
  </div>
  <!--    <div class="row" style="width: 100%">-->
  <!--      <div class="col">.</div>-->
  <!--      <div class="col-6"><h6> Cargo Orders</h6></div>-->
  <!--      <div class="col">Bell</div>-->
  <!--    </div>-->

  <div class="container">
    <div class="card mt-1 p-2 nav-bar-color">
      <div class="row justify-content-center">
        <div v-if="activeTabId === 'Upcoming'" class="col-md-4">
          <input class="main-card search d-flex flex-row-start" v-model="searchTerm" type="text"
                 placeholder="Search...">

        </div>

      </div>

      <div class="row justify-content-center">
        <div v-if="activeTabId === 'Upcoming'" class="col-md-4">
          <card-orders :order="order" v-for="order in filteredOrders"
                       :key="order.id" @openDetails="openDetails"></card-orders>

        </div>
        <div v-else-if="activeTabId === 'cargo'" class="col-md-4">

          <cargo-details :order_detail="order_detail" :cargo="cargo_details"
                         @closeDetails="activeTabId='Upcoming'"></cargo-details>

        </div>
      </div>
    </div>
  </div>


</template>


<script>
import axios from "axios";
// import axios from "../common/axios.js"
import {onBeforeMount, ref, computed, watch} from "vue";
import cardOrders from "@/components/cardOrders.vue";
import cargoDetails from "@/components/cargoDetails.vue";

export default {


  components: {
    cardOrders,
    cargoDetails
  },


  setup() {
    const activeTabId = ref('Upcoming');

    const tabs = [
      {id: 'Upcoming', label: 'Upcoming'},
      {id: 'Completed', label: 'Completed'},
      {id: 'Past', label: 'Past'},

    ];

    const setActiveTab = (tabId) => {
      activeTabId.value = tabId;
    };

    const orders = ref([
      {
        id: 1,
        number: '7804GNZ',
        status: 'Asignada',
        origin: 'New York',
        destination: 'Ginebra',
        pickup_date: '2023-12-27T10:45:00.000Z',
        delivery_date: '2023-12-27T17:30:00.000Z',
      },

    ]);


    const searchTerm = ref("");

    const filteredOrders = computed(() => {
      const term = searchTerm.value.toLowerCase();
      return orders.value.filter(order => order["order"].toLowerCase().includes(term));
    });

    const orders_complete_data = ref([])
    const upcoming = ref(null)


    function convertTimestampToNormalDate(timestamp) {
      let date = new Date(timestamp);

      let day = date.getDate();
      let month = date.getMonth() + 1;
      let year = date.getFullYear();
      let hours = date.getHours();
      let minutes = date.getMinutes();
      let seconds = date.getSeconds();
      let data_array = {};

      data_array["days"] = `${day < 10 ? '0' + day : day}/${month < 10 ? '0' + month : month}/${year}`;
      data_array["hours"] = `${hours < 10 ? '0' + hours : hours}:${minutes < 10 ? '0' + minutes : minutes}`;

      return data_array
    }

    function getDataForCards(data) {
      let _list = data.map(element => {
        const {
          type,
          order_number: order,
          is_today,
          destinations,
          start_date,
          status_string: status,
          route,
          end_date,
          pickup_state,
          dropoff_state,

        } = element;

        function getState(address) {
          let postalCodeMatch = address.match(/\b\d{5}\b/);

          let postalCode = postalCodeMatch ? postalCodeMatch[0] : null;
          let postalCodeIndex = postalCodeMatch ? postalCodeMatch.index : -1;


          let cityName = postalCodeIndex !== -1 ? address.substring(postalCodeIndex + postalCode.length).trim() : address;
          let state = cityName.split(',')[0]
          return state
        }


        const pickup_address = destinations[0]?.address || '';
        const dropoff_address = destinations[1]?.address || '';
        const stamp_start_date = start_date
        const stamp_end_date = end_date


        return {
          stamp_start_date,
          stamp_end_date,
          type,
          order,
          is_today,
          pickup_address,
          start_date: convertTimestampToNormalDate(start_date),
          status,
          dropoff: {state: dropoff_state},
          end_date: convertTimestampToNormalDate(end_date),
          dropoff_address,
          pickup_state: getState(pickup_address),
          dropoff_state: getState(dropoff_address)

        };
      });
      orders.value = _list
      order_detail.value = _list

    }

    async function getFirstApi() {
      let _list = []
      upcoming.value = await axios.get("https://129bc152-6319-4e38-b755-534a4ee46195.mock.pstmn.io/orders/upcoming")
      getDataForCards(upcoming.value["data"]["result"])
      let response = await axios.get("https://129bc152-6319-4e38-b755-534a4ee46195.mock.pstmn.io/orders")
      if (typeof (response.data.result) == "object") {
        _list.push(response.data.result)
        orders_complete_data.value = _list
      } else {
        orders_complete_data.value = response.data.result
      }
      // pickupDate.value = orders_complete_data.value["pickup"]["date"]

      // console.log(upcoming.value)

    }

    const cargo_details = ref({})
    const order_detail = ref({})

    function openDetails(order_number) {
      {
        order_detail.value = order_detail.value.find(element => {
          return element["order"] === order_number;
        });


        cargo_details.value = orders_complete_data.value.find(element => {
          return element["order_number"] === order_number;
        });
      }
      if (order_detail.value && cargo_details.value) {
        activeTabId.value = "cargo"
      } else {
        console.log("No Data Available")
      }


    }

    const cargo_info = computed(() => {

          return activeTabId.value == "Upcoming" ? "Cargo Orders" : "Cargo Details"
        }
    )
    watch(
        () => activeTabId.value,
        async (newVal) => {
          if (newVal == "Upcoming") {
            await getFirstApi()
          }
        }
    )
    onBeforeMount(
        async () => {
          await getFirstApi()
        }
    )

    return {
      searchTerm,
      filteredOrders,
      orders,
      activeTabId,
      tabs,
      setActiveTab,

      openDetails,
      cargo_details,
      order_detail,
      cargo_info
    };
  }
}

</script>
<style scoped>
.card {
  border-width: 0;

}

.main-card {
  background-color: #0c1113;

}

.nav-bar-color {
  background-color: #080c0f;
}

.nav-bar-header {

}

.search {
  width: 60%;
  border: none;
  border-bottom: 1px solid #ccc;
  outline: none;
  background-color: transparent;
  transition: border-bottom-color 0.3s ease;
  color: #fff;
  padding-left: 10%;
  background-repeat: no-repeat;
  background-image: url('../img/search.svg');
}
.underline {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  height: 2px;
  background-color: yellow;
  /* Adjust the color as needed */
}

</style>