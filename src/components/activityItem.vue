
<template>
  <div>
    <ActivityItemUpdate v-if="isUpdateActive"
                        :activity="activity"
                        :categories="categories"
                        @toggleUpdate="changeUpdateState"/>
    <ActivityItemDetail v-else
                        :activity="activity"
                        :categories="categories" 
                        @toggleUpdate="changeUpdateState"/>
  </div>
</template>

<script>
import ActivityItemDetail from "./activityItemDetail"
import ActivityItemUpdate from "./activityItemUpdate"
    export default {
        components:{
          ActivityItemDetail,
          ActivityItemUpdate
        },
        props:{
          activity:{
            type:Object,
            required:true
          },
          categories:{
            type:Object,
            required:true
          }
        },
        data(){
          return {
            isUpdateActive: false
          }
        },
        methods:{
          changeUpdateState(isUpdate) {
            this.isUpdateActive = isUpdate;
          }
        }
    }
</script>

<style  scoped>
/* ove tri klase nam ne trebaju kad koristimo :style umesto :class
/* .color-red{
color:red;
}

.color-orange{
  color:orange;

}

.color-green{
  color:green;

} */

/* .post .title{
  margin-bottom: 5px;
}

.activity-title {
  margin-bottom: 5px;
  display: inline-block;
} */
/* .activity-settings {
  float: right;
  font-size: 22px;
  &:hover {
    cursor: pointer;
  }
}
.activity-controll {
  margin: 20px 0 0 0;
  a {
    margin-right: 5px;
  }
} */

/* .activity-settings {
  float: right;
  font-size: 22px;
}

.activity-settings:hover{
  cursor: pointer;
}

.activity-controll{
  margin: 20px 0 0 0;
}

.activity-controll > a{
  margin-right: 5px;
} */

</style>