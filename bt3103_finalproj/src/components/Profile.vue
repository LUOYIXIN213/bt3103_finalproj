<template>
    <div id="profile">
        <div id="user">
            <img src="https://image.freepik.com/free-vector/man-profile-cartoon_18591-58482.jpg">
            <h1> {{this.name}} </h1>
            <p> {{'MEMBER SINCE '+ this.date}} </p> <br> <br>
            <form id="fm">
                <h1> Add Friends </h1>
                <label>Friend's Username </label>
                <input type="text" v-model.lazy="fname" required/><br>
                <button v-on:click.prevent="addFriend">Add Friend </button> 
            </form>
        </div>
        <div id="bar">
            <h1 id="thisweek">This Week </h1>
        </div>
        <div id="pie1">
            <doughnut-chart :height="230"></doughnut-chart>
        </div>
        <div id="lifetime">
            <h1 id="l">Lifetime Statistics</h1> <br>
            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTqrsTh0P06Y5o3PSd-4PKbuo-eS-ttZAtp8Us6brIytrxhq4WD15hlZzOOqD8vk7W6HmE&usqp=CAU" id="img2">
            <br><br><h1> {{this.friends + ' FRIENDS'}} </h1>
        </div>
        <div id="plastic">
            <img src="https://image.flaticon.com/icons/png/512/2639/2639818.png" id="img1"> 
            <h1> {{this.totalplastic + 'G PLASTIC SAVED'}}</h1>
        </div>
    </div>
</template>

<script>
import doughnut from '../profile_doughnut.js'
import database from '../firebase.js'
import firebase from "firebase"
import fs from 'firebase/app'

export default {
    components:{
        'doughnut-chart':doughnut
    },
  
  data(){
    return{
        user: [],
        userid: "",
        name: "",
        date: "",
        friends: 0,
        totalplastic: 0,
        fname: "",
        flist: [],
        fid: "",
        exsit: 'no'
        }
  },
  methods:{
    fetchItems:function(){
      database.collection('users').get().then((querySnapShot)=>{
        let item={}
        querySnapShot.forEach(doc=>{
            item=doc.data()
            item.id=doc.id
            this.user.push(item) 
            })      })
        this.userid = firebase.auth().currentUser.uid;    

        database.collection('users').doc(this.userid).get().then(snapshot => {
                this.date = snapshot.data().startdate
                this.name = snapshot.data().username
                this.friends = snapshot.data().list_friend.length
                this.totalplastic = snapshot.data().totalplastic
            });
    },
    addFriend:function(){
        for (let i = 0; i < this.user.length; i++) {
            if (this.fname == this.user[i].username) {
                this.exsit ='yes'
                this.fid = this.user[i].id
                i = this.user.length-1
                break;
            } else {
                this.exsit ='no'
                continue;
            }
        }
        if (this.exsit === 'yes') {
                database.collection("users").doc(this.userid).update({
                    list_friend: fs.firestore.FieldValue.arrayUnion(this.fname)
                })
                database.collection("users").doc(this.fid).update({
                    list_friend: fs.firestore.FieldValue.arrayUnion(this.name)
                })
                alert(this.fname + " added to your friend list!");
        } else {
            alert("The username is not found. Please key in valid username.");
        }
        this.fname=""
        this.exsit='no'
        window.location.reload()
    },   
    },
    created(){
      this.fetchItems()    
      }
}


</script>
<style scoped>
#thisweek,#l,#h {
    text-align: left;
}
#user{
    height:200px;
    float:left;
    width:50%;
    padding:30px;
}
p {
    font-weight: bold;
}

#bar{
    width: 25%;
    height: 50%;
    float: right;
    position: absolute;
    top: 25%;
    left: 50%;
}

#pie1{
    width: 25%;
    float: right;
    position: absolute;
    top: 25%;
    left: 70%;
}

#lifetime{
    width: 25%;
    height: 50%;
    float: right;
    position: absolute;
    top: 55%;
    left: 50%;
}

#plastic {
    float: right;
    position: absolute;
    top: 62%;
    left: 75%;
}
#img1{
    width: 280px;
    height: 280px;
}
#img2{
    width: 270px;
    height: 270px;
}

ul{
    list-style-type: none;
    font-size: 25px;
}

form{
    background: #bdf5bd;
    padding:30px;
}
</style>