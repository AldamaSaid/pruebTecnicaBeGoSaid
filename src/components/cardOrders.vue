<template>
  <section>
    <div class="row mt-3 mx-2 text-white">
      <div class="col-12 text-start">Order <span class="fw-bolder">{{ '' }}{{ '#' + order.order }}</span></div>
    </div>
    <div class="mt-2 main-card">
      <div class="card-header main-card-header">
        <div class="row">
          <div class="col-4 text-white text-start">
            <div v-if="order['type'] == 'FTL'">
              <img src="../img/truck.svg" alt="Icono de Resumen" class="icon-svg">
              {{ order.type }}
            </div>
            <div v-if="order['type'] == 'FCL'">
              <img src="../img/box.svg" alt="Icono de Resumen" class="icon-svg">
              {{ order.type }}
            </div>
          </div>

          <div class="col-3 text-white"></div>
          <div class="col-5">
            <div class="row">
              <div class="col-1 align-content-end">
                <!-- Your icon images here -->
              </div>
              <div class="col text-white text-end">{{ order.status }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="card text-white p-3 main-card">


        <div class="row text-start">
          <div class="col-1">
            <img src="../img/departure.svg" alt="Icono de Resumen" class="icon-svg">
          </div>
          <div class="col">
            <div>
              <span class="fw-light">PICKUP</span>
            </div>
            <div>
              <span class="fw-bold">{{ order.pickup_state }}</span>
            </div>
            <div>
              <span class="fw-light">{{ order.pickup_address }}</span>
            </div>
          </div>
          <div class="col-3">
            <div class="row  text-end">
              <span>{{ order['start_date']['days'] }}</span>
            </div>
            <div class="row text-end">
              <span class="fw-bolder">{{ order['start_date']['hours'] }}</span>

            </div>


          </div>


        </div>

        <div class="row my-3"></div>
        <div class="row text-start">
          <div class="col-1 ">
            <img src="../img/location.svg" alt="Icono de Resumen" class="icon-svg">

          </div>
          <div class="col">
            <div>
              <span class="fw-light">DROPOFF</span>
            </div>
            <div>
              <span class="fw-bold">{{ order.dropoff_state }}</span>
            </div>
            <span>{{ order.dropoff_address }}</span>
          </div>

          <div class="col-3">

            <div class="row text-end">
              <span>{{ order['end_date']['days'] }}</span>
            </div>
            <div class="row  text-end">
              <span class="fw-bolder">{{ order['end_date']['hours'] }}</span>

            </div>
          </div>


          <!--        <div class="col-2 mt-5">-->
          <!--          <button class="pickup col " disabled v-if="order.is_today">Iniciar recogida</button>-->
          <!--        </div>-->


        </div>
        <div class="actions row mt-5">
          <div class="col">
            <button style="  background-color: #ffff00;" v-if="timeRemaining == true" class="pickup col "
                    @click="startNavigation">Iniciar recogida
            </button>
            <button style="  background-color: #181a1c;" v-else-if="timeRemaining!==false" class="pickup col " disabled>
              <span class="text-white fs-6">Start pickup in </span>
              <span class="fw-bolder fs-6" style="color: yellow;">{{ timeRemaining }}</span>
            </button>

          </div>
          <div class="col d-flex flex-row-reverse">
            <button class="resume" @click="openDetailsModal">Resume <img src="../img/eye.svg" alt="Icono de Resumen"
                                                                         class="icon-svg"></button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import {computed, onMounted, ref} from "vue"

export default {
  emits: ["openDetails"],
  props: {

    order: {
      type: Object,
      required: true
    }

  },
  setup(props, {emit}) {
    onMounted(
        () => {

        }
    )
    const pickupDate = ref(props.order.stamp_start_date);
    const isPickupDate = ref(false);

    const timeRemaining = computed(() => {
      const currentDate = new Date();
      const pickupDateTime = new Date(pickupDate.value);

      // if (dateString > pickupDateTime) {
      //   // La hora de recogida ya ha pasado
      //   isPickupDate.value = true;
      //   return false;
      // }

      if (isSameDay(currentDate, pickupDateTime)) {
        const differenceInMilliseconds = currentDate - pickupDateTime;

        // if (differenceInMilliseconds < 0) {
        //   isPickupDate.value = false;
        //   return true;
        // }

        const hours = Math.floor(differenceInMilliseconds / (1000 * 60 * 60));
        const minutes = Math.floor((differenceInMilliseconds % (1000 * 60 * 60)) / (1000 * 60));
        const formattedDifference = `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}`;

        isPickupDate.value = true;
        if (minutes > 0) {
          return formattedDifference

        } else {
          return true;
        }

      } else {
        return false
      }
    });

    function isSameDay(date1, date2) {
      return (
          date1.getFullYear() === date2.getFullYear() &&
          date1.getMonth() === date2.getMonth() &&
          date1.getDate() === date2.getDate()
      );
    }

    function startNavigation() {
      console.log("Navegar")
    }

    function openDetailsModal() {
      emit("openDetails", props.order.order)
    }

    return {timeRemaining, startNavigation, openDetailsModal}
  }
}
</script>

<style scoped>
.main-card {

  background-color: #0c1113;
  border-radius: 25px;
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

.resume {
  font-weight: bold;
  height: 15%;

  width: 25%;
  font-size: small;
  padding: 8px;
  background-color: #ffff00;
  position: absolute;
  bottom: 0;
  right: 0;
  z-index: 1;
  border-radius: 25px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

.pickup {
  font-weight: bold;
  color: #0c1113;
  height: 15%;

  width: 40%;
  font-size: small;
  padding: 5px;

  position: absolute;
  bottom: 0;
  left: 0;
  z-index: 1;
  border-radius: 0 25px 25px 25px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}


</style>