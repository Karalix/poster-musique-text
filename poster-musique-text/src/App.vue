<template>
  <div id="app">
        <img src="./assets/spinner.gif" class="spinner" v-if="notLoaded"/>
        <div v-else>
          <h1>{{pageName}}</h1>
          <p>{{pageDesc}}</p>
          <h2>Evénements</h2>
          <div v-for="event of events" :key="event.Nom">
            <a :href="event.get('Url agenda')"  class="event">
            <div class="event-left">
              <h3>{{event.get('Nom')}}</h3>
              <p>{{(new Date(event.get('Date'))).toLocaleString()}}</p>
            </div>
            <div class="event-right">
              <img :src="event.get('Image')[0].url"/>
            </div>
            </a>
          </div>
          <h2>Notre Sélection</h2>
          <div v-for="link of links" :key="link.Nom" class="event">
            <div class="event-left">
              <h3>{{link.get('Nom')}}</h3>
              <p><a :href="link.get('Url')">{{link.get('Url')}}</a></p>
            </div>
            <div class="event-right">
              <img :src="link.get('Image')[0].url"/>
            </div>
          </div>
        </div>
  </div>
</template>

<script>
import Airtable from 'airtable'

export default {
  name: 'App',
  data: function () {
    return {
      notLoaded: true,
      pageName: '',
      pageDesc: '',
      events: [],
      links: []
    }
  },
  mounted: function () {
    let page = parseInt(window.location.hash.slice(1))
    if (!isNaN(page)) {
      Airtable.configure({
        endpointUrl: 'https://api.airtable.com',
        apiKey: 'keyHLqvz6NRbhxNy4'
      })
      var base = Airtable.base('appzie6SWUMPaqojZ')

      base('page').select({
          maxRecords: 20,
          view: 'Grid view',
          filterByFormula: "({Page} = 1)"
      }).firstPage((err, records) => {
          if (err) { console.error(err); return; }
          records.forEach((record) => {
              this.notLoaded = false
              console.log('Retrieved', record.get('Nom'))
              this.pageName = record.get('Nom')
              this.pageDesc = record.get('Description')
          })
      })

      base('evenement').select({
          maxRecords: 20,
          view: 'Grid view',
          filterByFormula: "({Page} = 1)"
      }).firstPage((err, records) => {
          if (err) { console.error(err); return; }
          records.forEach((record) => {
            this.events.push(record)
          })
      })

      base('lien').select({
          maxRecords: 20,
          view: 'Grid view',
          filterByFormula: "({Page} = 1)"
      }).firstPage((err, records) => {
          if (err) { console.error(err); return; }
          records.forEach((record) => {
            this.links.push(record)
          })
      })
    }
  }
}
</script>

<style>
#app {
  font-family: Jost, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
  margin: 50px;
  color: white;
}

.spinner {
  margin-left: 50%;
  margin-right: 50%;
  margin-top: 25%;
  transform: translate(-50%, 0);
  width: 40px;
}

html {
  background: linear-gradient(180deg, #2B4E71 0%, #000000 100%);
  background-attachment: fixed;
}

.event {
  display: grid;
  grid-template-columns: 70% 1fr;
}

.event-right img {
  border-radius: 20px;
  width: 80px;
  height: 80px;
  object-fit: cover;
}

.event-right {
  background-color: white;
  border-radius: 20px;
  width: 80px;
  height: 80px;
  margin-top: 20px;
}

a {
  color: white;
}

</style>
