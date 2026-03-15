<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MOBILE PORTAL</title>

<script src="https://cdn.jsdelivr.net/npm/piexifjs"></script>

<style>

/* Full page background photo */
body{
  font-family:Arial, sans-serif;
  margin:0;
  min-height:100vh;
  background: url("https://i.ibb.co/YTX5jq95/ow.jpg") no-repeat center center fixed;
  background-size: cover;
  display:flex;
  justify-content:center;
  align-items:center;
  color:white;
}

/* Overlay to darken background for readability */
body::before{
  content:"";
  position:fixed;
  top:0;
  left:0;
  width:100%;
  height:100%;
  background:rgba(0,0,0,0.6);
  z-index:-1;
}

.container{
  width:100%;
  max-width:460px;
  background:rgba(15,23,42,0.85);
  backdrop-filter:blur(10px);
  padding:25px;
  border-radius:14px;
  box-shadow:0 0 25px rgba(0,0,0,0.6);
}

/* Main Title */
.title{
  display:block;
  text-align:center;
  font-size:34px;
  color:#60a5fa;
  margin:0 auto 10px auto;
  text-shadow: 1px 1px 5px black;
}

/* EXIF GeoTag Editor Title Card with photo background */
.title-card{
  text-align:center;
  padding:20px;
  border-radius:12px;
  margin-bottom:20px;
  color:white;
  background:
  linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),
  url("https://images.unsplash.com/photo-1507525428034-b723cf961d3e?auto=format&fit=crop&w=1350&q=80");
  background-size:cover;
  background-position:center;
  box-shadow: 0 4px 15px rgba(0,0,0,0.4);
}

.title-card h2{
  margin:0;
  font-size:24px;
  color:#facc15;
  text-shadow: 1px 1px 3px black;
}

.title-card p{
  margin-top:5px;
  font-size:14px;
  color:white;
  text-shadow: 1px 1px 2px black;
}

label{
  display:block;
  margin-top:12px;
  font-size:13px;
  color:#cbd5f5;
}

input{
  width:100%;
  padding:10px;
  margin-top:5px;
  border-radius:8px;
  border:none;
  background:#1e293b;
  color:white;
}

input:focus{
  outline:none;
  background:#334155;
}

.preview{
  margin-top:10px;
  border-radius:10px;
  overflow:hidden;
  border:2px dashed #475569;
}

img{
  width:100%;
  display:block;
}

button{
  width:100%;
  padding:12px;
  margin-top:20px;
  border:none;
  border-radius:10px;
  background:linear-gradient(90deg,#3b82f6,#2563eb);
  color:white;
  font-size:15px;
  cursor:pointer;
  transition:0.2s;
}

button:hover{
  transform:scale(1.03);
  background:linear-gradient(90deg,#2563eb,#1d4ed8);
}

input[type=range]{
  margin-top:10px;
}

@media(max-width:600px){

  .container{
    margin:15px;
    padding:18px;
  }

  .title{
    font-size:26px;
  }

  .title-card h2{
    font-size:20px;
  }

}

</style>
</head>

<body>

<div class="container">

  <h1 class="title">MOBILE PHONE</h1>

  <div class="title-card">
    <h2>WELCOME PO!!!</h2>
    <p>PORTAL NI LEANDRO</p>
  </div>

  <label>Upload Photo</label>
  <input type="file" id="upload">

  <div class="preview">
    <img id="preview">
  </div>

  <label>File Name</label>
  <input id="filename">

  <label>Camera Make</label>
  <input id="make">

  <label>Camera Model</label>
  <input id="model">

  <label>Software</label>
  <input id="software">

  <label>Date Taken</label>
  <input id="date">

  <label>Latitude</label>
  <input id="lat">

  <label>Longitude</label>
  <input id="lng">

  <label>Image Description</label>
  <input id="desc">

  <label>Output Quality (KB Control)</label>
  <input type="range" min="0.2" max="1" step="0.05" value="0.9" id="quality">

  <button onclick="downloadImage()">Download Edited Image</button>

</div>

<script>

let imageData=""

document.getElementById("upload").addEventListener("change",function(e){

  let file=e.target.files[0]

  document.getElementById("filename").value=file.name.replace(/\.[^/.]+$/,"")

  let reader=new FileReader()

  reader.onload=function(event){

    imageData=event.target.result

    document.getElementById("preview").src=imageData

    loadExif(imageData)

  }

  reader.readAsDataURL(file)

})

function dmsToDeg(dms){
  return dms[0][0]/dms[0][1]+
  dms[1][0]/dms[1][1]/60+
  dms[2][0]/dms[2][1]/3600
}

function degToDmsRational(deg){
  let abs=Math.abs(deg)
  let d=Math.floor(abs)
  let m=Math.floor((abs-d)*60)
  let s=Math.round(((abs-d)*60-m)*60*100)
  return [[d,1],[m,1],[s,100]]
}

function loadExif(data){
  try{
    let exif=piexif.load(data)

    document.getElementById("make").value=
    exif["0th"][piexif.ImageIFD.Make]||""

    document.getElementById("model").value=
    exif["0th"][piexif.ImageIFD.Model]||""

    document.getElementById("software").value=
    exif["0th"][piexif.ImageIFD.Software]||""

    document.getElementById("desc").value=
    exif["0th"][piexif.ImageIFD.ImageDescription]||""

    document.getElementById("date").value=
    exif["Exif"][piexif.ExifIFD.DateTimeOriginal]||""

    if(exif["GPS"][piexif.GPSIFD.GPSLatitude]){
      let lat=dmsToDeg(exif["GPS"][piexif.GPSIFD.GPSLatitude])
      let lng=dmsToDeg(exif["GPS"][piexif.GPSIFD.GPSLongitude])

      document.getElementById("lat").value=lat
      document.getElementById("lng").value=lng
    }

  }catch(e){
    console.log("No EXIF found")
  }
}

function downloadImage(){
  let make=document.getElementById("make").value
  let model=document.getElementById("model").value
  let software=document.getElementById("software").value
  let date=document.getElementById("date").value
  let lat=parseFloat(document.getElementById("lat").value)
  let lng=parseFloat(document.getElementById("lng").value)
  let desc=document.getElementById("desc").value
  let filename=document.getElementById("filename").value
  let quality=parseFloat(document.getElementById("quality").value)

  let zeroth={}
  let exif={}
  let gps={}

  zeroth[piexif.ImageIFD.Make]=make
  zeroth[piexif.ImageIFD.Model]=model
  zeroth[piexif.ImageIFD.Software]=software
  zeroth[piexif.ImageIFD.ImageDescription]=desc

  exif[piexif.ExifIFD.DateTimeOriginal]=date

  if(!isNaN(lat)&&!isNaN(lng)){
    gps[piexif.GPSIFD.GPSLatitudeRef]=lat>=0?"N":"S"
    gps[piexif.GPSIFD.GPSLatitude]=degToDmsRational(lat)

    gps[piexif.GPSIFD.GPSLongitudeRef]=lng>=0?"E":"W"
    gps[piexif.GPSIFD.GPSLongitude]=degToDmsRational(lng)
  }

  let exifObj={"0th":zeroth,"Exif":exif,"GPS":gps}
  let exifBytes=piexif.dump(exifObj)

  let img=new Image()
  img.onload=function(){
    let canvas=document.createElement("canvas")
    canvas.width=img.width
    canvas.height=img.height
    let ctx=canvas.getContext("2d")
    ctx.drawImage(img,0,0)
    let compressed=canvas.toDataURL("image/jpeg",quality)
    let finalImage=piexif.insert(exifBytes,compressed)
    let link=document.createElement("a")
    link.href=finalImage
    link.download=filename+".jpg"
    link.click()
  }
  img.src=imageData
}

</script>

</body>
</html>
