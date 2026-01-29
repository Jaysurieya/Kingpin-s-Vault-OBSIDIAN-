[[My Tasks]]
for about us ---> Animated Testimonials from aceternity ui
landing page background --->  background beams
place holder scroll
side bar
tracing beam

bentogrid
comet card 
expandable cards
flip words
floating dock
focus cards
link preview
loader
moving border
multi step loader
stateful button
sticky banner
sticky scroll reveal
svg mask reveal
time line   
typre writer
modal dialog - framer


documentation:

1st august ---> dataset preparation
2nd august ---> dataset preprocessing :

doing data augumentation for classes having less images
doing undersampling for the classes having more images

these r not done by locally saving this is done by "on the fly approach"

model changed to efficientnet-b0
and it took about 5 hours of training

planned about the next steps [and also studied for tomorrows exam]

.... 

running ml model ---> create a virtual environment
venv
activate venv scripts
flask install 
model and classes in the same folder
python app.py
check for the port in the backend and use different port for backend and app.py

code for accessing the camera: ---> navigator.mediaDevices.getUserMedia({ video: true });
------------------------------

import React, { useRef } from "react";

export default function CameraComponent() {
  const videoRef = useRef(null);

  const openCamera = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true });
      if (videoRef.current) {
        videoRef.current.srcObject = stream;
        videoRef.current.play();
      }
    } catch (error) {
      console.error("Camera access denied or not available:", error);
      alert("Unable to access camera. Please allow camera permissions.");
    }
  };

  const stopCamera = () => {
    if (videoRef.current && videoRef.current.srcObject) {
      let tracks = videoRef.current.srcObject.getTracks();
      tracks.forEach((track) => track.stop()); // stop all tracks
      videoRef.current.srcObject = null;
    }
  };

  return (
    <div>
      <video ref={videoRef} width="400" autoPlay></video>
      <br />
      <button onClick={openCamera}>Open Camera</button>
      <button onClick={stopCamera}>Stop Camera</button>
    </div>
  );
}

////////



tasks to be completed:
- [x] water tracker's details should also be added to the mongo db ✅ 2025-12-18
- [x] calendar click with history of details ✅ 2025-12-19 ^dwgx
- [x] daily reset + today's items should be still ✅ 2025-12-18
- [x] Add Agent ✅ 2025-12-18
- [ ] Laoding animation during Login
- [ ] index.html problem
- [ ] handle chatbot api eposure and chat bot dimension problem

adhirasam
aloo_cutlet
aloo_methi
appam
basundi
boondi
bread_toast
bread_with_omlete
burrito
cake
Cauliflower
chana_masala
chapati
chappati
cheesecake
chicken-biryani
chicken_curry
Chicken_soup
chicken_tikka
chicken_tikka_masala
chicken_wings
chikki_bar
chocolate_cake_brownie
cup_cakes
daal_puri
dal_makhani
deviled_eggs
dhal
donuts
dosa
Duck
dumplings
fish_gravy
french_fries
fried_rice
fried_squid
garlic_bread
grilled_cheese_sandwich
grilled_Fish
gulab_jamun
halwa
hamburger
hot_and_sour_soup
hot_dog
ice_cream
idiyappam
idli
jalebi
kachayam
kadai_paneer
kesari
kulfi
lobster_roll_sandwich
macaroni_and_cheese
macarons
masal_dosa
meduvadai
modak
momos
mutton-biriyani
mysore_pak
naan-butter_naan
nachos
New folder
non_veg_patty
noodles
omelette
onion_rings
oysters
paani_puri
pakoda
palak_paneer
pancakes
paneer_butter_masala
paniyaram
parotta
pav_bhaji
pizza
poori-chole_poori
poornalu
pootharekulu
pork_chop
potato-gravy
potato_gravy
Pudding
pulled_pork_sandwich
ramen
rasgulla
ras_malai
red_velvet_cake
roti
Salad
samosa
sandwich
shrimp_and_grits
sohan_papdi
spaghetti_bolognese
spaghetti_carbonara
spring_rolls
steak
strawberry_shortcake
sushi
sweet_paniyaram
tacos
tandoori chicken
tea-chai
upma
vada pav
ven pongal
waffles


problems faced during rendering:
case mismatch in the import statements - for checking this there is an inbuilt node script
redirection to index.html
all place use useNavigate for navigation
seperate services for seperate folders like frontend , backend , ml_model
frontend - static website
backend and mlmodel - web service
