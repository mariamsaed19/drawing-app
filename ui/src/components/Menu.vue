<template>
  <nav>
      <!-- button new -->
    <button id="myBtn" class="btn" @click="openmodal(0)">NEW</button>
     <div id="myModal" class="modal">
  <div class="modal-content">
    <span class="close" @click="close(0)">&times;</span>
     <button class="open" @click="savecurrentFile()">Save current folder</button><br><br>
     <button class="open" @click="newfile()">Discard current folder</button>
  </div></div>

     <!-- button save -->
    <button id="myBtn1" class="btn" @click="openmodal(1)">Save</button>
    <div id="myModal1" class="modal">
  <div class="modal-content">
    <span class="close" @click="close(1)">&times;</span>
    <h1>Save file</h1>
    <label for="fname">File name:</label><br>
  <input type="text" id="fname" name="fname" required autocomplete="off"><br><br>
   <input type="radio" id="json" name="type" value="json" checked>
  <label for="json">.json</label><br>
  <input type="radio" id="xml" name="type" value="xml">
  <label for="xml">.xml</label><br><br>
  <label for="fpath">Enter the path of the folder to be saved in:</label><br>
  <input type="text" id="fpath" name="fpath" required autocomplete="off"><br><br>
  <button class="open" @click="save()">Save file</button>

  </div>
</div>

 <!-- button open -->
<button id="myBtn2" class="btn" @click="openmodal(2)">OPEN</button>
<div id="myModal2" class="modal">
  <div class="modal-content">
  <span class="close" @click="close(2)">&times;</span>
  
    <label for="fname">File name:</label><br>
  <input type="text" id="fname1" name="fname" required autocomplete="off"><br><br>
   <label for="fpath">Enter the path of the folder which the file is saved  in:</label><br>
  <input type="text" id="fpath1" name="fpath" required autocomplete="off"><br><br>
    <button class="open" @click="open()">open file</button>
  </div></div>
 <!-- button undo -->
    <button id="undo" class="btn" @click="undo()"><span style='font-weight:bold;'>&#8630;</span> UNDO</button>

     <!-- button redo -->
    <button id="redo" class="btn" @click="redo()"><span style='font-weight:bold;'>&#8631;</span> REDO</button>
  </nav>

</template>

<script>
import Shapes from "@/components/Shapes";
import axios from 'axios';
var newf=false;
export default {
  name: "Menu",
  methods: {
    undo(){
      axios.get("http://localhost:8085/undo")
          .then(function (response) {
             if(response.data != ''){
               Shapes.methods.set_list(response.data);
             }
          })

    },
    redo(){
        axios.get("http://localhost:8085/redo")
          .then(function (response) {
             if(response.data != ''){
               Shapes.methods.set_list(response.data);
             }
          })
    },

    savecurrentFile(){
      newf=true;
      document.getElementById("myBtn1").click();
      document.getElementsByClassName("close")[0].click();
    },

    newfile(){
         Shapes.methods.clear(); 
         // send to backend axios
         axios.get("http://localhost:8085/new")


         if(newf==false){
         document.getElementsByClassName("close")[0].click();}

    },

    close(number){
      if(number==0){
      document.getElementById("myModal").style.display="none";
      }
      else if(number==1){
      document.getElementById("myModal1").style.display="none";
      }else{
         document.getElementById("myModal2").style.display="none";
      }
    },
    save(){
    var ext;
    if( document.getElementById("json").checked){
      ext=".json";
    }else{
      ext=".xml"
    }
   var filepath= document.getElementById("fpath").value;
    //backend axios
    axios.get("http://localhost:8085/save", {
        params: {
          name:document.getElementById("fname").value,
          path:filepath+'\\',
          extension:ext
        }
      })
    if(newf){
      newf=false;
      this.newfile();

    }

     document.getElementsByClassName("close")[1].click();

  },
  open(){
    Shapes.methods.clear();
    var res = document.getElementById("fname1").value.split(".");
    //axios
    var filepath = document.getElementById("fpath1").value;
    axios.get("http://localhost:8085/load", {
        params: {
          name:String(res[0]),
          path:String(filepath+'\\'),
          extension:String('.'+res[1])
        }
      }).then(function (response) {
            Shapes.methods.set_list(response.data);
            console.log(response.data);

          })
    
    document.getElementsByClassName("close")[2].click();

  },


    openmodal(number){
        if(number==0){
      document.getElementById("myModal").style.display = "block";
      }
      else if(number==1){
      document.getElementById("myModal1").style.display="block";
      }else{
         document.getElementById("myModal2").style.display="block";
      }
    }
    
  },

  
  
}
</script>

<style scoped>
button{
  padding: 15px 25px;
  margin: 4px 10px;
  transition-duration: 0.4s;
  font-size: 18px;
  cursor: pointer;
  text-align: center;
  text-decoration: none;
  outline: none;
  color: rgb(17, 17, 17);
  background-color: #e9f3e9;
  border: none;
  border-radius: 15px;
  box-shadow: 0 9px #999;
}
.open{
  font-size: 14px;
  border-radius: 20px;
    border: 1px solid #888;
    box-shadow: none;
  padding: 5px 5px;
}
.btn:hover, .open:hover {
  color: #e9f3e9;
  background-color: rgb(74, 5, 104);
}





.modal {
  display: none; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  padding-top: 100px; /* Location of the box */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}

/* Modal Content */
.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 30%;
}
/* The Close Button */
.close {
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}


</style>