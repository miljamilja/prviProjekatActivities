
<template>
  <div id="activityApp">
    <nav class="navbar is-white topNav">
      <div class="container">
        <div class="navbar-brand">
          <h1> {{ fullAppName }} </h1>
          <!-- <h1>{{ watchedAppName }}</h1> -->
        </div>
      </div>
    </nav>
    <TheNavbar @filterSelected="setFilter"/>
    <section class="container">
      <div class="columns">
        <div class="column is-3">
          <!--Activity create form -->
          <ActivityCreate :categories="categories"/>
          <!--Activity form end -->
        </div>
        <div class="column is-9">
          <div class="box content"
               :class="{fetching: isFetching, 'has-error': error}">
            <div v-if="error">
              {{ error }}
            </div>
            <div v-else>
              <div v-if="isFetching">
                Loading...
              </div>
              <div v-if="isDataLoaded">
                <ActivityItem 
                  v-for="activity in filteredActivities" 
                  v-bind:key="activity.id" 
                  :activity="activity" 
                  :categories="categories" /> 
              </div>
            </div>
            <div v-if="!isFetching && error==null">
              <div class="activity-length">
                Currently {{ activityLength }} activities
              </div>
              <div class="activity-motivation">
                {{ activityMotivation }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Vue from 'vue'
import store from "./store"
import ActivityItem from "./components/activityItem"
import fakeApi from "@/lib/fakeApi.js";
import ActivityCreate from "./components/activityCreate";
import TheNavbar from './components/theNavbar';


export default 
{    /*eslint-disable no-console*/
  name: 'App',
  components: {ActivityItem, ActivityCreate, TheNavbar} ,
  data() {
    const {state:{activities,categories}} = store
    return {
      creator: "Milja Pejcic",
      appName: "Activity planner",
      watchedAppName: "Activity planner by Milja Pejcic",
      isFetching: false,
      error: null,
      user: {},
      activities, //ovo je izvuceno iz const {state:{activities,categories}} = store
      categories,  //ovo je izvuceno iz const {state:{activities,categories}} = store
      filter: 'all'
    }
  },
  computed:{
    filteredActivities(){
      let filteredActivities = {};
      let condition;

      if(this.filter==="all"){
        return this.activities;
      }
      //ovo tehnicki radi al je ruzno
      // else if(this.filter==="inprogress"){
      // filteredActivities = Object.values(this.activities).filter(activity=>{
      //     return activity.progress > 0 && activity.progress < 100
      // })
      // }
      // else if(this.filter==="finished"){
      // filteredActivities = Object.values(this.activities).filter(activity=>{
      //     return activity.progress === "100"
      // })
      // }
      // else if(this.filter==="notstarted"){
      // filteredActivities = Object.values(this.activities).filter(activity=>{
      //     return activity.progress === 0
      // })
      // }

      if(this.filter==="inprogress"){
        condition = (value) => value > 0 && value < 100
      }
      else if(this.filter==="finished"){
        condition = (value) => value === 100
      }
      else if(this.filter==="notstarted"){
        condition = (value) => value === 0
      }

      filteredActivities = Object.values(this.activities).filter(activity => {
           return condition(activity.progress)
      })


      return filteredActivities;
    },
    fullAppName(){
      return this.appName + " by " + this.creator;
    },
    activityLength(){
      const activitiesKeyArray = Object.keys(this.activities);
      const activityLength = activitiesKeyArray.length;
      return activityLength;
    },
    activityMotivation(){
      if(this.activityLength && this.activityLength < 5)
      {
        return "Nice to see some activities.";
      }
      else if(this.activityLength >= 5)
      {
        return "So many activities, good job.";
      }
      else
      {
        return "No activities, so sad."
      }
    },
    activitiesLength(){
      return Object.keys(this.activities).length //Object.keys vraca niz kljuceva iz activities objekta
    },
    categoriesLength(){
      return Object.keys(this.categories).length
    },
    isDataLoaded(){
      return this.activityLength && this.categoriesLength;
    }
  },
  // watch:{
  //   creator(val){
  //     debugger;
  //     console.log(val);
  //      this.watchedAppName = this.appName + " by " + val;
  //   },
  //   appName(val){
  //     debugger;
  //     console.log(val);
  //      this.watchedAppName = val + " by " + this.creator;
  //   }
  // },
  beforeCreate() {
    console.log("beforeCreated");
  },
  created() {

    //fakeApi.fillDB() //ovo treba da se izvrsi jednom jer se samo prvi put smestaju podaci u local storage

    this.isFetching = true;
    store.fetchActivities()
    .then((activities) =>{
      // this.activities = activities; ovo nam ne treba jer smo u data izvukli activities iz store
      this.isFetching = false;

    })
    .catch(err => {
      this.error = err;
      this.isFetching = false;

    })

    store.fetchCategories().then((categories) =>{
      // this.categories = categories; ovo nam ne treba jer smo u data izvukli categories iz store
    })
    this.user = store.fetchUser()
  },
  methods:{
    setFilter(filterOption){
      this.filter = filterOption;
    }
  }
}
</script>

<style>
#activityApp {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.activity-motivation {
float:right;
}

.activity-length {
display:inline-block;
}

html,body {
  font-family: 'Open Sans', serif;
  background: #F2F6FA;
}
footer {
  background-color: #F2F6FA !important;
}

.fetching{
  border: 2px solid orange;
}

.has-error{
  border: 2px solid red;
}
.topNav {
  border-top: 5px solid #3498DB;
}
.topNav .container {
  border-bottom: 1px solid #E6EAEE;
}
.container .columns {
  margin: 3rem 0;
}
.navbar-menu .navbar-item {
  padding: 0 2rem;
}
aside.menu {
  padding-top: 3rem;
}
aside.menu .menu-list {
  line-height: 1.5;
}
aside.menu .menu-label {
  padding-left: 10px;
  font-weight: 700;
}
.button.is-primary.is-alt {
  background: #00c6ff;
  background: -webkit-linear-gradient(to bottom, #0072ff, #00c6ff);
  background: linear-gradient(to bottom, #0072ff, #00c6ff);
  font-weight: 700;
  font-size: 14px;
  height: 3rem;
  line-height: 2.8;
}
.media-left img {
  border-radius: 50%;
}
.media-content p {
  font-size: 14px;
  line-height: 2.3;
  font-weight: 700;
  color: #8F99A3;
}
article.post {
  margin: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #E6EAEE;
}
article.post:last-child {
  padding-bottom: 0;
  border-bottom: none;
}
.menu-list li{
  padding: 5px;
}

.navbar-brand > h1 {
  font-size: 31px;
  padding: 20px;
}

.example-wrapper
{
  margin-left:30px;
}

</style>
