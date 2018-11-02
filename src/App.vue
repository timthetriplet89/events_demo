<template>
  <div id="app">
    <h1>Event List</h1>

    <!-- <input type="radio" v-model="pick" v-bind:value="a"> -->

    <!-- Select filter -->
    <div class="input">
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
          <p>{{ event.content }}</p>
          <p>{{ event.category }}</p>          
          <p>{{ event.pubDate }}</p>
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
            <option value="Activities/Service">Service</option>
            <option value="Activities/Sports">Sports</option>
            <option value="Activities/Talent">Talent</option>
            <option value="Activities/Fitness">Fitness</option>
            <option value="Activities/Social">Social</option>
            <option value="Activities/Outdoor">Outdoor</option>
            <option value="Activities/Wellness">Wellness</option>
            <option value="Activities/Life Skills">Life Skills</option>
      </select>      
        <!-- <button type="submit">Submit</button> -->
      <!-- </form> -->

      <!-- display matching category results -->
      <div class="events">  <!-- v-show="action === 'home'" -->
        <div class="card" v-for="(event, eventIndex) in eventsFilteredCategory" :key="eventIndex">
          <h4>{{ event.title }}</h4>
          <p>{{ event.content }}</p>
          <p>{{ event.category }}</p>          
          <p>{{ event.pubDate }}</p>
        </div>  
      </div>   
    </div>

    <div class="input" v-show="action === 'search'">
      <!-- search filter input  -->
      <form @submit.prevent="filterBySearch">
        <input placeholder="keyword" v-model="search.keyword"/>
        <button type="submit">Search</button>  <!-- @click="filterBySearch()" -->
      </form> 

      <!-- display search results -->
      <div class="events">  <!-- v-show="action === 'home'" --> 
        <div class="card" v-for="(event, eventIndex) in eventsFilteredSearch" :key="eventIndex"> 
          <h4>{{ event.title }}</h4>
          <p>{{ event.content }}</p>
          <p>{{ event.category }}</p>          
          <p>{{ event.pubDate }}</p>
        </div>  
      </div>    
    </div>

      <!-- date input -->
      <!-- <div v-show="action === 'date'"> </div> -->
  </div>
</template>

<script>
  import Datepicker from 'vuejs-datepicker';
  const rssAPI = 'https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.sandbox.auth0-extend.com/getRss?url=';
  const colors = ["indigo","blue","cyan","light-blue","teal","light-green","blue-grey"];

  export default {
    data () {
      return {
        action: 'all',
        eventData: [],
        filterSelection: '',
        dateSelection: '',
        categorySelection: '',
        search: {
          keyword: ''
        },
        eventsFilteredDate: [],
        eventsFilteredCategory: [],
        eventsFilteredSearch: [],
        msg: 'Welcome to Your Vue.js App',
        noArray: [],
        drawer:true,
			  showIntro:false,
			  addFeedDialog:false,
			  addURL: 'https://calendar.byui.edu/RSSFeeds.aspx?data=FBqjK%2fSDoNoXjLLIGz5Kea2SyIk%2bK80m9T8RR%2bZnYumBTZxQgj8tnUyxXbSmZzvpAtLMsRTG%2bPcnabTM4oKMbg%3d%3d',
			  urlError:false,
			  urlRules:[],
			  feeds:[],
			  selectedFeed:null
      } 
    }, 
    created() {
		  // do single file components use the created hook?  
		  // what is 'this', at this point in the code?
		  this.loadData();
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
        console.log("filterByCategory called");
        console.log(this.categorySelection);  
        this.action = 'category';
        this.eventsFilteredCategory = [];
        for(var event of this.eventData) {
          //console.log(event.categories[0]);
          if (event.categories[0] === this.categorySelection) {
            this.eventsFilteredCategory.push({title: event.title, content: event.content, category: event.categories[0], pubDate: event.pubDate });
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
            eventText = event.title + ' ' + event.pubDate + ' ' + event.content;
            // add way tosearch all event fields
            if (eventText.toUpperCase().includes(nKeyword.toUpperCase())) {
              this.eventsFilteredSearch.push({title: event.title, content: event.content, category: event.category, pubDate: event.pubDate });
            }
          }
        }

        // console.log(this.eventsFilteredSearch);

        // clear out this.search.keyword?
        // otherwise you hit an empty search box
        // and get same search results...

        // display relevant events

      },
      loadData() {
			  this.urlError = false;
			  this.urlRules = [];
			  // code purposely left out from tutorial 

        fetch(rssAPI+encodeURIComponent(this.addURL)) 
        .then(res => res.json())
				.then(res => {
					// ok for now, assume no error, cuz awesome
					this.addURL = '';

					//assign a color first
					res.feed.color = colors[this.feeds.length % (colors.length-1)];

					// ok, add the items (but we append the url as a fk so we can filter later)
					res.feed.items.forEach(item => {
						item.feedPk = this.addURL;
            item.feedColor = res.feed.color;
            //console.log(item);
						this.eventData.push(item);
					});

					// delete items
					delete res.feed.items;

					// add the original rss link
					res.feed.rsslink = this.addURL;

					this.feeds.push(res.feed);
					this.addFeedDialog = false;

					//always hide intro
					this.showIntro = false;

					//persist the feed, but not the items
					this.storeFeeds();
				});
      },
      storeFeeds() {
			  console.log('calling storeFeeds');
			  localStorage.setItem('feeds', JSON.stringify(this.feeds));
		  }
    }, 
    components: {
      Datepicker
    }
  }
</script>
      
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
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
  border-style: solid;
}

.input {
  margin: 28px
}

.input {
  margin: 28px
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
