<template>
    <div class="additems-container">
        <div class="what-kind">
            <br><br><br><br>
            <h1> What kind of {{ this.type }} is this? </h1>
            <br>
            <div id="types" v-if="this.type=='footwear'">
                <ul style="list-style-type:none;">
                    <li><img src="https://i.postimg.cc/rFPmdQSR/Canvas-Low-Sneakers.png" class="img" v-on:click="chooseType('Canvas Low Sneakers')"></li>
                    <li><img src="https://i.postimg.cc/vBSC2z6V/Hiking-Sneakers.png" class="img" v-on:click="chooseType('Hiking Sneakers')"></li>
                    <li><img src="https://i.postimg.cc/9MKCwVbK/Old-Schools.png" class="img" v-on:click="chooseType('Old Schools')"></li>
                    <li><img src="https://i.postimg.cc/vZqjZh3S/Athletic-Sneakers.png" class="img" v-on:click="chooseType('Athletic Sports Sneakers')"></li>
                </ul>
            </div>
        </div>
        <div class="upload-image">
            <upload v-on:upload="onUpload"></upload>
            <br><br><br>
            <form @submit.prevent="pressed">
                <label for="name">Name*:</label><br>
                <input type="text" id="name" name="name" size="60" v-model="name" required><br><br>
                <label for="detail">Details*:</label><br>
                <textarea name="detail" rows="3" cols="60" v-model="detail" required></textarea><br><br>
                <label for="notes">Notes*:</label><br>
                <textarea name="notes" rows="6" cols="60" v-model="notes" required></textarea><br><br>
                <button type="submit">Add</button>
            </form>

    
        </div>
    </div>
</template>

<script>
import firebase from "firebase/app";
import "firebase/firestore";
import upload from "./Upload.vue"
export default {
    mounted() {
        this.setupFirebase();
        this.tagListeners();
    },

    methods:{
        setupFirebase() {
            firebase.auth().onAuthStateChanged(user => {
                if (user) {
                    // User is signed in.
                    this.loggedIn = true;
                    this.currentUser = firebase.auth().currentUser;
                } else {
                    // No user is signed in.
                    this.loggedIn = false;
                    this.currentUser = false;
                }
            });
        },
        onUpload(image) {
            this.image = image;
        },
        pressed() {
            var db = firebase.firestore();
            var countDocRef = db.collection(this.currentUser.uid).doc("Count");
            countDocRef.get().then((doc) => {
                if (doc.exists) {
                    var oldCount = doc.data().count;
                    db.collection(this.currentUser.uid).doc("Count").update({
                        count: oldCount + 1
                    })
                } else {
                    db.collection(this.currentUser.uid).doc("Count").set({
                        count: 0
                    })
                }
            });
            //var count = db.collection(this.currentUser.uid).doc("Count").get().then
            var counter = countDocRef.get().then((doc) => {
                doc.data().count
            });
            db.collection('marketplace').add({
                name: this.name,
                detail: this.detail,
                notes: this.notes,
                imageRef: 'uploads/'+this.currentUser.uid+'/' + this.image,
                user: this.currentUser.uid,
                type: this.itemType,
                count: 0
            });
            let docRef = db.collection(this.currentUser.uid).doc();
            db.collection(this.currentUser.uid).add({
                name: this.name,
                detail: this.detail,
                notes: this.notes,
                imageRef: 'uploads/'+this.currentUser.uid+'/' + this.image,
                type: this.itemType,
                id: docRef.id
            }).then(() => this.$router.push('/profile'));
        },
        chooseType(string) {
            this.itemType = string;
        },
        tagListeners() {
            var header = document.getElementById("types");
            var imgs = header.getElementsByClassName("img");
            for (var i = 0; i < imgs.length; i++) {
                imgs[i].addEventListener("click", function() {
                    var current = document.getElementsByClassName("active");
                    if (current.length > 0) { 
                        current[0].className = current[0].className.replace(" active", "");
                    }
                    this.className += " active";
                });
            } 
        }
    },
    data(){
        return {
            loggedIn: false,
            currentUser: false,
            name: '',
            detail: '',
            notes: '',
            imageRef: false,
            type: this.$route.query.id,
            itemType: ''
        }
    },
    components:{
        upload
    }
}
</script>

<style scoped>
h1{
    text-decoration: underline #EC6041;
    font-size: 30px;
    margin-left: 70px;
}
.additems-container{
    display:flex;
    justify-content: space-between;
    margin-left: 160px;
}
.upload-image{
    margin-right: 150px;
    margin-top: 50px;
}

.search-bar{
    margin-left: 250px;
}
button{
    background: #EC6041;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    box-shadow: 4px 4px 0px #F1876F, 8px 8px 0px #F5AE9E;
    color: white;
    width: 450px;
}
ul {
    columns:2;
}
img {
    cursor: pointer;    
}
.active {
    border: 2px dotted coral;
}
</style>