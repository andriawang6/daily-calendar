<template>
  <div>
    <div id="external-events">
      <p>
        <strong>Draggable Events</strong>
      </p>


      <div
        v-for="(value, key) in eventsMap"
        :key="key"
        class="fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event"
        :data-event="{title: key, image: value}"
      >
        <div class="fc-event-main">{{ value[0] }}</div>
        <img :src="value[1]" :alt="value[0]" class="fc-event-image" />

      </div>

      <p>
        <input type="checkbox" v-model="removeAfterDrop" />
        <label>Remove after drop</label>
      </p>
    </div>

    <div id="calendar-container">
      <FullCalendar ref="calendar" :options="calendarOptions" />
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import FullCalendar from "@fullcalendar/vue3";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import interactionPlugin, { Draggable } from "@fullcalendar/interaction";



export default {
  components: {
    FullCalendar,
  },
  setup() {
    const removeAfterDrop = ref(false);

    const eventsMap = ref(new Map());
    eventsMap.value.set("School", "https://source.unsplash.com/BnavvlQCU1Q/100x100");
    eventsMap.value.set("Lunch", "https://source.unsplash.com/BnavvlQCU1Q/100x100");
    eventsMap.value.set("Park", "https://source.unsplash.com/BnavvlQCU1Q/100x100");
    eventsMap.value.set("Playground", "https://source.unsplash.com/BnavvlQCU1Q/100x100");
    eventsMap.value.set("Hospital", "https://source.unsplash.com/yE60zo2odas/100x100");
    eventsMap.value.keys();


    const calendarOptions = ref({
      eventContent: function(arg) {
        let titleEl = document.createElement('h3')
        titleEl.textContent = arg.event.title



        let italicEl = document.createElement('i')

        if (arg.event.extendedProps.isUrgent) {
          italicEl.innerHTML = 'urgent event'
        } else {
          italicEl.innerHTML = 'normal event'
        }

        let imageSource = arg.event.extendedProps.image;
        // let imgEl = { html: `<img src="${imageSource}" alt="Event 1" class="fc-event-image">` };
        let imgEl = document.createElement("img")
        imgEl.src = imageSource;

        imgEl.alt = ""; // alt text
        imgEl.class = "fc-event-image";

        let arrayOfDomNodes = [ titleEl, imgEl ]
        return { domNodes: arrayOfDomNodes }
      },
      plugins: [dayGridPlugin, timeGridPlugin, interactionPlugin],
      headerToolbar: {
        left: "prev,next today",
        center: "title",
        right: "dayGridMonth,timeGridWeek,timeGridDay",
      },
      editable: true,
      droppable: true,
      eventDrop: handleEventDrop,
    });

    onMounted(() => {
      initializeExternalEvents();
    });

    function initializeExternalEvents() {
      new Draggable(document.getElementById("external-events"), {
        itemSelector: ".fc-event",
        eventData: (eventEl) => ({
          title: eventEl.innerText,
          image: eventEl.querySelector("img").getAttribute("src"),
        }),
      });
    }

    function handleEventDrop(info) {
      if (removeAfterDrop.value) {
        info.draggedEl.parentNode.removeChild(info.draggedEl);
      }
    }

    return {
      removeAfterDrop,
      eventsMap,
      calendarOptions,
    };
  },
};
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}

#external-events {
  position: fixed;
  z-index: 2;
  top: 20px;
  left: 20px;
  width: 150px;
  padding: 0 10px;
  border: 1px solid #ccc;
  background: #eee;
}

#external-events .fc-event {
  cursor: move;
  margin: 3px 0;
}

#calendar-container {
  position: relative;
  z-index: 1;
  margin-left: 200px;
}

#calendar {
  max-width: 1100px;
  margin: 20px auto;
}
</style>