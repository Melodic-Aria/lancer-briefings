<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "001",
      "events": "001",
      "missions": [
        {
          "slug": "001",
          "name": "The Drop",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Taco Tuesday",
          "alias": "Paulie Burkin",
          "code": "Burkin.Paulie:62c69927-1423-4756-849a-4beffee08b7c//NDL-C-ALPHA-WILL",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "Unleaded Florescence"
        },
        {
          "callsign": "Rough Rider",
          "alias": "Teddy R",
          "code": "R.Teddy:662d36e2-6d9e-460e-aa6c-d3df96c3647e//NDL-C-BLUE-MANTLE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Hey There"
        },
        {
          "callsign": "Decaf",
          "alias": "Tomoko Viscarra",
          "code": "Viscarra.Tomoko:7e0db6ec-83e9-41db-b3c0-632e3425736b//NDL-C-STEEL-GLYPH ",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "AL Cappuccino"
        },
        {
          "callsign": "Ember",
          "alias": "Lesley Cria",
          "code": "Cria.Lesly:3bda0bf0-652d-429b-b490-c1c43f0ad9f0//NDL-C-NULL-SEA ",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Lesser Evil"
        },
        {
          "callsign": "Angel",
          "alias": 'Yuan Qing Cliche',
          "code": "Cliche.Yuan.Qing:5302fc8d-e024-4671-a9b8-00c0e7cd255f//NDL-C-SECOND-HINDSIGHT ",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Faster Than Walking"
        },
      ],
      "header": {
        "planet": "Cressidium",
        "year": "5016u",
        "system": "Edinburgh",
        "gate": "Red Pike",
        "ring": "Cascade-Line",
        "headerTitle": "Union",
        "headerSubtitle": "Naval Department",
        "subheaderTitle": "Rapid Response",
        "subheaderSubtitle": "27th Squad",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
