<template>
    <div>
        <div class="my-wishlist">
            <h1> My Wishlist </h1>
        </div>
        <div class="wishlist-container">
            <div class="wishlist-item">
                <h2>placeholder</h2>
            </div>
            <div class="marketplace-text">
                <h2>WANT MORE ITEMS?</h2>
                <br>
                <h3>GO TO OUR MARKETPLACE OR SIMPLY WISH</h3> <br> <h3>FOR MORE!</h3>
                <br><br><br>
                <router-link to="/additem" tag="button-marketplace" exact>WISHING FOR MORE?</router-link>
            </div>
        </div>
    </div>

</template>

<script>
import firebase from "firebase/app";
export default {
    created() {
        this.setupFirebase();
    },
    methods:{
        setupFirebase() {
            firebase.auth().onAuthStateChanged(user => {
                if (user) {
                    // User is signed in.
                    this.loggedIn = true;
                    this.currentUser = firebase.auth().currentUser;
                    var listRef = firebase.storage().ref('uploads/' + this.currentUser.uid);
                    listRef.listAll().then((res) => {
                        res.items.forEach((itemRef) => {
                            itemRef.getDownloadURL().then((url) => this.items.push(url))
                        }
                    )})
                } else {
                    // No user is signed in.
                    this.loggedIn = false;
                    this.currentUser = false;
                }
            });
        },
        remove(item){
            var pictureRef = firebase.storage().refFromURL(item);
            pictureRef.delete().then(()=> location.reload());
            //alert("Item removed successfully")
        }
    },
    data(){
        return {
            loggedIn: false,
            currentUser: false,
            items: []
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.wishlist-container{
    display: flex;
    margin-top: 100px;
}
.my-wishlist{
    text-align: center;
    margin-top: 70px;
}
.wishlist-item{
    width: 40%;
    margin-left: 100px;
}
.marketplace-text{
    width: 60%;
}
h1{
    text-decoration: underline #EC6041;
    font-size: 70px;
    text-align: center;
}
h2{
    font-size:38px;
}
h3{
    font-size:20px;
}
button-marketplace {
    font-size: 20px;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    color: white;
    background: #EC6041;
    box-shadow: 4px 4px 0px #F1876F, 8px 8px 0px #F5AE9E;
    padding: 20px 24px;
    cursor: pointer;
}
</style>
