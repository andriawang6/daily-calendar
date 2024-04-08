<template>
  <div>
    <div id="external-events">
      <p>
        <strong>Events</strong>
      </p>
      <div id="addEventModal" :class="{ 'modal': true }">
      <div class="modal-content">
        <label>Event Name:</label>
        <input type="text" v-model="newEventName" />
        <label>Image URL:</label>
        <input type="text" v-model="newEventImageUrl" />
        <button @click="handleAddEvent">Add Event</button>
      </div>
    </div>

    <div
      v-for="(value, key) in eventsMap"
      :key="key" 
      class="fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event"
    >
      <div class="event-container">
        <button @click="handleDeleteEvent(value[0])">X</button>
        <div class="fc-event-main">{{ value[0] }}</div>
      </div>
      <img :src="value[1]" /> 
    </div>
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
    // localStorage.clear();
    const eventsMap = ref(new Map());
    const newEventName = ref('');
    const newEventImageUrl = ref('');
    const calendarOptions = ref({
      eventColor: "#fab4d9",

      initialView: 'timeGridDay',
      slotDuration: '00:05:00',
      snapDuration: '00:05:00',
      eventClick: function(info) {
        info.event.remove();
      },
      
      eventContent: function(arg) {
        let titleEl = document.createElement('h3')
        titleEl.textContent = arg.event.title.substring(2)

        let imageSource = arg.event.extendedProps.image;
        // let imgEl = { html: `<img src="${imageSource}" alt="Event 1" class="fc-event-image">` };
        let imgEl = document.createElement("img")
        imgEl.src = imageSource;
        imgEl.height = 50;
        imgEl.width = 50;

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
    function updateLocalStorage() {
      localStorage.setItem("eventsMap", JSON.stringify([...eventsMap.value]));
      console.log("updating local storage with: ", localStorage.getItem("eventsMap"));
    }

    function handleAddEvent() {
      eventsMap.value.set(newEventName.value, newEventImageUrl.value);
      console.log("adding event: " + eventsMap.value.get(newEventName.value));
      newEventName.value = '';
      newEventImageUrl.value = '';
      updateLocalStorage();
    }
    function handleDeleteEvent(key) {
      eventsMap.value.delete(key);;
      updateLocalStorage();
    }

    function initializeExternalEvents() {
      new Draggable(document.getElementById("external-events"), {
        itemSelector: ".fc-event",
        eventData: (eventEl) => ({
          title: eventEl.innerText,
          image: eventEl.querySelector("img").getAttribute("src"),
        }),
      });
    }

    onMounted(() => {
      initializeExternalEvents();
      const storedEventsMap = localStorage.getItem("eventsMap");
      if (storedEventsMap) {
        const parsedEventsMap = JSON.parse(storedEventsMap);
        Object.entries(parsedEventsMap).forEach(([key, value]) => {
          eventsMap.value.set(value[0], value[1]);

        });
      } else {
        eventsMap.value.set("Speech", "https://d18vdu4p71yql0.cloudfront.net/libraries/noun-project/speech_10_g.svg");
        eventsMap.value.set("OT", "https://ecdn.teacherspayteachers.com/thumbitem/OT-Heart-Clip-Art-5046926-1604703499/original-5046926-1.jpg");
        eventsMap.value.set("School", "https://d18vdu4p71yql0.cloudfront.net/libraries/mulberry/school.svg.varianted-skin.svg");
        eventsMap.value.set("Music", "https://d18vdu4p71yql0.cloudfront.net/libraries/noun-project/Music-124ac7f50d.svg");
        eventsMap.value.set("Swimming Lesson", "https://t3.ftcdn.net/jpg/03/29/13/12/360_F_329131295_X8OOg1RI8iO0F2NSrixHEpiBh7hRegt2.webp");
        eventsMap.value.set("Get Dressed", "https://static.vecteezy.com/system/resources/thumbnails/016/059/247/small_2x/cute-little-boy-wearing-clothes-get-dressed-daily-routine-activity-vector.jpg");
        eventsMap.value.set("Shower", "https://d18vdu4p71yql0.cloudfront.net/libraries/arasaac/shower.png");
        eventsMap.value.set("Put on Jammies", "https://as1.ftcdn.net/v2/jpg/04/34/49/42/1000_F_434494262_C98IB4jYPzt9Ml9CTKJuRuehIfVyZifc.jpg");
      }
    });

    function handleEventDrop(info) {
      // if (removeAfterDrop.value) {
      //   info.draggedEl.parentNode.removeChild(info.draggedEl);
      // }
    }

    return {
      // removeAfterDrop,
      calendarOptions,
      newEventName,
      newEventImageUrl,
      eventsMap,
      calendarOptions,
      handleAddEvent,
      handleDeleteEvent,
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
  height: 100%;
}

body > div,
body > div > div,
.fc {
  height: 100%;
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
  overflow-y: auto; /* Make the sidebar scrollable */
  max-height: calc(100vh - 40px); /* Set maximum height to fit the viewport minus top and bottom margin */
}

#external-events .fc-event {
  cursor: move;
  margin: 3px 0;
}

#external-events .fc-event img {
  max-width: 50%; /* Ensure images don't exceed their container width */
  height: auto; /* Maintain aspect ratio */
}

/* Scale the images within calendar events */
.fc-event .fc-event-image {
  max-width: 100px; /* Set maximum width for the image */
  height: auto; /* Maintain aspect ratio */
}



#calendar-container {
  position: relative;
  z-index: 1;
  margin-left: 200px;
  height: 100%;
}

#calendar {
  max-width: 10;
  margin: 20px auto;
}

/* Scale the images within calendar events */
#calendar-container .fc-event .fc-event-image img {
  max-width: 100px; /* Set maximum width for the image */
  height: auto; /* Maintain aspect ratio */
}

.event-container {
  display: flex;
  align-items: center; /* Center vertically */
}

.event-container button {
  margin-right: 5px; /* Adjust the spacing between the button and the event name */
}

/* Adjust vertical alignment of event titles */
.fc-event-title {
  margin-top: 10000; /* Remove any top margin */
  line-height: 1; /* Set line height to 1 for tight vertical alignment */
}

.fc-daygrid-block-event {
  background-color: #fab4d9;
  border: solid 1px #fab4d9;
}

.fc-event-main {
  color: #000000 !important;
}


</style>
