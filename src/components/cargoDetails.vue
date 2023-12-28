<template>
  <section>
    <div class="row mt-3 mx-2 text-white">
      <div class="col-12 text-start">Referencia <span class="fw-bolder">{{ '' }}{{
          '#' + cargo['reference_number']
        }}</span></div>
    </div>
    <div class="row mt-3 mx-2 text-white">
      <div class="col-12 text-start">Order <span class="fw-bolder">{{ '' }}{{ '#' + order_detail.order }}</span></div>
    </div>
    <div class="mt-2 mb-4 main-card">

      <div class="card text-white p-3 main-card">


        <div class="row text-start">
          <div class="col-1">
            <img style="background-color: yellow; border-radius: 25px;" src="../img/departure_black.svg"
                 alt="Icono de Resumen" class="icon-svg">
          </div>
          <div class="col">
            <div>
              <span class="fw-light">PICKUP</span>
            </div>
            <div>
              <span class="fw-bold">{{ order_detail.pickup_state }}</span>
            </div>
            <div>
              <span class="fw-light">{{ order_detail.pickup_address }}</span>
            </div>
          </div>


        </div>

        <div class="row my-3"></div>
        <div
            class="row text-start"
            tabindex="0"
            @click="toggleCollapse"
            @keyup.enter="toggleCollapse"
        >
          <div class="col-1">
            <img src="../img/circle.svg" alt="Icono de Resumen" class="icon-svg">
          </div>
          <div class="col">
            <div>
              <span class="fw-light">DROPOFF</span>
            </div>
            <div>
              <span class="fw-bold">{{ order_detail.dropoff_state }}</span>
            </div>
            <span>{{ order_detail.dropoff_address }}</span>
          </div>
        </div>
      </div>

    </div>
    <div style="margin-top: 80px"></div>

    <div class="mt-4 track">

      <div class="card text-white p-3 track">
        <div class="mb-3 text-center">
          <img v-if="order_detail.photo" :src="order_detail.photo"
               alt="Icono de Resumen" class="icon-svg">
          <img v-else src="../img/avatar48.svg"
               alt="Icono de Resumen" class="icon-svg">
        </div>
        <div class="row ">

          <div class="row mb-5">
            <div class="col-md">
              <div class="row" v-for="(pickupStatus, index) in status_list.pickup" :key="index">
                <div class="col text-end">
                  <img :class="{ 'completed': pickupStatus.active }" src="../img/circle.svg" alt="Icono de Resumen"
                       class="icon-svg">
                </div>
                <div class="col text-start">
                  <span>{{ pickupStatus.status }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="card-footer text-center" style="border-color: white">
          <button class="text-white" v-if="status_list.pickup[2]['active']" :disabled="false"
                  style="background-color:#0c1113;border: none;">Track order
          </button>
          <button v-else :disabled="true"
                  class="fs-5 fw-bolder"
                  style="background-color:#0c1113;border: none; border-color: grey; color: grey">Track order
          </button>

        </div>
      </div>

    </div>
    <div class="card text-white mt-5 p-3 track">
      <div class=" mt-3">
        <div class="row">
          <div class="col text-start">
            <button style="background-color:#0c1113;border: none;" @click="toggleCollapse"
                    class="btn btn-primary text-startfs-5 fw-bolder">
              Pickup Data
            </button>
          </div>
          <div v-if="is_openning_pickup_data" class="col text-end">
            <img src="../img/expand.svg" alt="Icono de Resumen"
                 class="icon-svg">
          </div>
          <div v-else class="col text-end">
            <img src="../img/collapse.svg" alt="Icono de Resumen"
                 class="icon-svg">
          </div>
        </div>

        <div :class="{ 'collapse': !is_openning_pickup_data }" id="collapseExample">
          <div class="card card-body main-card text-white text-start" style="border: none">

            <p> {{ order_detail["pickup_address"] }}</p>
            <p> {{ order_detail["start_date"]["days"] + '  ' }}    {{ '' + order_detail["start_date"]["hours"] }} </p>
            <p> {{ cargo["destinations"][0]["contact_info"]["telephone"] }}</p>
            <p> {{ cargo["destinations"][0]["contact_info"]["email"] }}</p>


          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import {computed, onMounted, onBeforeUnmount, ref, watch} from "vue"

export default {
  emits: ["closeDetails"],
  props: {
    order_detail: {
      type: Object,
      required: true
    },
    cargo: {
      type: Object,
      required: true
    }

  },
  setup(props, {emit}) {
    const toggleCollapse = () => {
      is_openning_pickup_data.value = !is_openning_pickup_data.value;
    };
    const is_openning_pickup_data = ref(false)
    const status_list = ref({})
    status_list.value["pickup"] = props.cargo.status_list["pickup"]

    onMounted(
        () => {
          console.log(props.cargo)
        }
    )
    onBeforeUnmount(
        () => {
          emit("closeDetails", true)
        }
    )

    watch(
        () => is_openning_pickup_data.value,
        () => {
          console.log(is_openning_pickup_data.value)
        }
    )
    return {status_list, is_openning_pickup_data, toggleCollapse}
  }
}
</script>

<style scoped>
.btn_disabled {
  background-color: yellow;
}

.track {

  border-top: none;
  background-color: #0c1113;
  border-radius: 0 0 0 0;
  border-bottom-color: #202225;
  border-left-color: #202225;
  border-right-color: #202225;
  /* Ajusta el valor según tu preferencia, 1 es completamente visible, 0 es completamente transparente */;


}

.main-card {
  border-top: none;
  background-color: #0c1113;
  border-radius: 0 0 0 25px;
  border-bottom-color: #202225;
  border-left-color: #202225;
  border-right-color: #202225;
  /* Ajusta el valor según tu preferencia, 1 es completamente visible, 0 es completamente transparente */;

}

.main-card-header {
  background-color: #0c1113;
  border-radius: 25px;
  border-color: #202225;

  /* Ajusta el valor según tu preferencia, 1 es completamente visible, 0 es completamente transparente */;

}

.order-step {
  width: 30px;
  height: 30px;
  background-color: #ddd;
  border-radius: 50%;
  margin-bottom: 20px;
  display: flex;
  align-items: start;
  justify-content: center;
  position: relative;
  margin-left: 0;
}

.order-step span {
  font-size: 14px;
  color: #333;
}

.order-step.active {
  background-color: #5bc0de;
}


.order-step:first-child:before {
  display: none;
}

.completed {
  border-radius: 25px;
  background-color: yellow;
}

.text-end {
  margin-left: -120px;
}
</style>