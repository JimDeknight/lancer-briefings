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
        <h1>Briefing</h1>
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
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Dawnbreaker",
          "status": "start"
        },
        {
          "slug": "002",
          "name": "Exp14710n",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Mojave",
          "alias": "Leamon 'Lemon' Mara Amoa",
          "code": "a2a03dc0-f77c-4a21-a326-474eba20066a//NDL-C-STOLEN-OCTOBER//a2a03dc0-f77c-4a21-a326-474eba20066a",
          "corpro": "SSC",
          "frame": "Orchis",
          "mech": "Goose on the Loose"
        },
        {
          "callsign": "Supercell",
          "alias": "Odunita Latendresse",
          "code": "76d8668-1d4c-44ac-b2aa-9aa7cff55895//NDL-C-SIGMA-TELLURION//d76d8668-1d4c-44ac-b2aa-9aa7cff55895",
          "corpro": "SSC",
          "frame": "Viceroy",
          "mech": "Typhoon"
        },
        {
          "callsign": "Siscon",
          "alias": "Paolo",
          "code": "35769445-14f1-45d1-8c67-72770ec409ba//NDL-C-RHO-MUSE//35769445-14f1-45d1-8c67-72770ec409ba",
          "corpro": "SSC",
          "frame": "Comet",
          "mech": "Jollibee"
        },
        {
          "callsign": "Revenant",
          "alias": "Koschei",
          "code": "f2430fde-6751-43b3-8083-c553294a91ec//NDL-C-DARK-GLYPH//f2430fde-6751-43b3-8083-c553294a91ec",
          "corpro": "Horus",
          "frame": "Pegasus",
          "mech": "Familiar Face"
        },
      ],
      "header": {
        "planet": "MSCV God's Kennel",
        "year": "5014u",
        "system": "Traveling",
        "gate": "Andrah-Sintara Starway",
        "ring": "variable",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "671st Detachment",
        "subheaderSubtitle": "HellsHounds",
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
