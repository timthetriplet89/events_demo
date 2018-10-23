<template>
  <div id="app">
    <h1>Event List</h1>

    <!-- <input type="radio" v-model="pick" v-bind:value="a"> -->

    <!-- Select filter -->
    <div>
      <!-- <form @submit.prevent="changeFilter"> -->
        <select v-on:change="changeFilter" v-model="filterSelection">
          <option disabled value="">Select Filter</option>
          <option value="all">All</option>
          <option value="search">search</option>
          <option value="category">category</option>
          <option value="date">date</option>
        </select>
        <!-- <button type="submit">Submit</button>  -->
      <!-- </form> -->
    </div>

    <!-- start off showing all events -->
    <div v-show="action === 'all'">
      <div class="events" > 
        <div class="card" v-for="(event, eventIndex) in eventData" :key="eventIndex">
          <h4>{{ event.title }}</h4>
          <p>{{ event.date }}</p>
          <p>{{ event.category }}</p>
        </div>
      </div>
    </div>

    <!-- <div v-show="action === 'date'">
      <datepicker placeholder="Select Date"></datepicker>
    </div> -->

    <div v-show="action === 'date'">
        <datepicker v-on:change="filterByDate" placeholder="Select Date" v-model="dateSelection"></datepicker>
        <h1>{{ this.dateSelection }}</h1>
    </div>

    <div class="dataFilter" v-show="action === 'category'">
      <!-- category input -->

      <!-- <form @submit.prevent="filterByCategory"> -->
      <select v-on:change="filterByCategory" v-model="categorySelection">
            <option disabled value=""></option>
            <option value="academic">academic</option>
            <option value="social">social</option>
      </select>      
        <!-- <button type="submit">Submit</button> -->
      <!-- </form> -->

      <!-- display matching category results -->
      <div class="events">  <!-- v-show="action === 'home'" -->
        <div class="card" v-for="(event, eventIndex) in eventsFilteredCategory" :key="eventIndex">
          <h4>{{ event.title }}</h4>
          <p>{{ event.date }}</p>
          <p>{{ event.category }}</p>
        </div>  
      </div>   
    </div>

    <div v-show="action === 'search'">
      <!-- search filter input  -->
      <form @submit.prevent="filterBySearch">
        <input placeholder="keyword" v-model="search.keyword"/>
        <button type="submit">Search</button>  <!-- @click="filterBySearch()" -->
      </form> 
      <!-- display search results -->
      <div class="events">  <!-- v-show="action === 'home'" --> 
        <div class="card" v-for="(event, eventIndex) in eventsFilteredSearch" :key="eventIndex"> 
          <h4>{{ event.title }}</h4> 
          <p>{{ event.date }}</p> 
          <p>{{ event.category }}</p> 
        </div>  
      </div>    
    </div>

      <!-- date input -->
      <!-- <div v-show="action === 'date'"> </div> -->
  </div>
</template>

<!-- <script src="https://unpkg.com/vue"></script>
 <script src="https://unpkg.com/vuejs-datepicker"></script> -->

<script>
  import Datepicker from 'vuejs-datepicker';

  export default {
    data () {
      return {
        action: 'all',
        eventData: [
          {title: 'Department Orientation', date: 'Thursday', category: 'academic'},
          {title: 'Dance', date: 'Friday', category: 'social'},
          {title: 'workshop', date: 'Tuesday', category: 'academic'},
          {title: 'performance', date: 'Saturday', category: 'social'}
        ],
        filterSelection: '',
        
        dateSelection: '',
        categorySelection: '',
        search: {
          keyword: ''
        },
        eventsFilteredDate: [],
        eventsFilteredCategory: [],
        eventsFilteredSearch: []
      } 
    },
    methods: { 
      changeFilter() { 
        this.action = this.filterSelection; 
      }, 
      filterByDate() { 
        this.action = 'date';
        alert("filterByDate has been called");
        // console.log(this.dateSelection);
      }, 
      filterByCategory() {
        this.action = 'category';
        this.eventsFilteredCategory = [];
        for(var event of this.eventData) {
          if (event.category === this.categorySelection) {
            this.eventsFilteredCategory.push({title: event.title, date: event.date, category: event.category});
          }
        }
      },
      filterBySearch () {
        this.action = 'search';
        this.eventsFilteredSearch = [];
        // obtain data
        var nKeyword = this.search.keyword;
        var eventText = '';
        // empty input field
        this.search.keyword = '';
        if (nKeyword !== '') {
          // find relevant events
          for (var event of this.eventData) {
            eventText = event.title + ' ' + event.date + ' ' + event.category;
            // add way tosearch all event fields
            if (eventText.toUpperCase().includes(nKeyword.toUpperCase())) {
              this.eventsFilteredSearch.push({title: event.title, date: event.date, category: event.category});
            }
          }
        }

        // console.log(this.eventsFilteredSearch);

        // clear out this.search.keyword?
        // otherwise you hit an empty search box
        // and get same search results...

        // display relevant events

      }
    }, 
    components: {
      Datepicker
    }
  }
</script>
      
<style>
* {
  text-align: center;
}

.events {
  display: block;
  width: 400px;
  margin: auto;
}

.card {
  padding: 14px;
  box-shadow: 0px 0px 2px grey;
  margin: 1rem auto;
}

.dataFilter {
  margin-top: 1rem auto;
}

input {
  margin-right: 1rem;
}

button.delete  {
  background-color: pink;
}

button {
  margin-right: 1rem;
}
</style>
