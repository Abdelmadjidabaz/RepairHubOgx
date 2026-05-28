<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>RepairHub</title>
<script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="bg-gray-100 min-h-screen p-5">

<!-- LOGIN SCREEN -->
<div id="loginPage" class="fixed inset-0 bg-black flex items-center justify-center z-50">
  <div class="bg-white p-6 rounded-3xl w-full max-w-sm space-y-4">
    <h2 class="text-2xl font-bold text-center">Login</h2>

    <input id="username" placeholder="Username" class="w-full border p-3 rounded-xl">
    <input id="password" type="password" placeholder="Password" class="w-full border p-3 rounded-xl">

    <button onclick="login()" class="w-full bg-black text-white py-3 rounded-xl">
      دخول
    </button>

    <p id="loginError" class="text-red-500 text-sm text-center"></p>
  </div>
</div>

<div class="max-w-6xl mx-auto hidden" id="app">

  <!-- HEADER -->
  <div class="bg-black text-white rounded-3xl p-6 shadow-xl flex items-center justify-between">
    <div>
      <h1 class="text-4xl font-bold">RepairHub</h1>
      <p class="text-gray-300 mt-2">Phone Repair Management</p>
    </div>

    <button onclick="openModal()"
      class="bg-green-500 w-14 h-14 rounded-full text-3xl font-bold flex items-center justify-center">
      +
    </button>
  </div>

  <!-- LIST -->
  <div id="list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mt-8"></div>

</div>

<!-- MODAL -->
<div id="modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4">

  <div class="bg-white w-full max-w-md rounded-3xl p-6 space-y-4">

    <h2 class="text-2xl font-bold">Repair</h2>

    <input id="customer" placeholder="Customer" class="w-full border p-3 rounded-xl"/>
    <input id="device" placeholder="Device" class="w-full border p-3 rounded-xl"/>
    <input id="issue" placeholder="Issue" class="w-full border p-3 rounded-xl"/>
    <input id="price" placeholder="Price" class="w-full border p-3 rounded-xl"/>

    <div class="flex gap-3">
      <button onclick="saveRepair()" class="bg-green-500 text-white w-full py-3 rounded-xl font-bold">
        Save
      </button>
      <button onclick="closeModal()" class="bg-gray-300 w-full py-3 rounded-xl font-bold">
        Cancel
      </button>
    </div>

  </div>
</div>

<script>

/* LOGIN */
const USER = "admin";
const PASS = "1234";

function login(){
  let u = document.getElementById("username").value;
  let p = document.getElementById("password").value;

  if(u === USER && p === PASS){
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("app").classList.remove("hidden");
  } else {
    document.getElementById("loginError").innerText = "خطأ في البيانات";
  }
}

/* DATA */
let repairs = JSON.parse(localStorage.getItem("repairs")) || [];
let editIndex = null;

/* MODAL */
const modal = document.getElementById("modal");

function openModal(){
  modal.classList.remove("hidden");
}

function closeModal(){
  modal.classList.add("hidden");

  document.getElementById("customer").value = "";
  document.getElementById("device").value = "";
  document.getElementById("issue").value = "";
  document.getElementById("price").value = "";

  editIndex = null;
}

/* SAVE / EDIT */
function saveRepair(){

  let customer = document.getElementById("customer").value;
  let device = document.getElementById("device").value;
  let issue = document.getElementById("issue").value;
  let price = document.getElementById("price").value;

  if(!customer || !device || !issue || !price){
    alert("Fill all fields");
    return;
  }

  let data = { customer, device, issue, price };

  if(editIndex === null){
    repairs.push(data);
  } else {
    repairs[editIndex] = data;
  }

  localStorage.setItem("repairs", JSON.stringify(repairs));

  closeModal();
  render();
}

/* DELETE */
function deleteRepair(i){
  repairs.splice(i,1);
  localStorage.setItem("repairs", JSON.stringify(repairs));
  render();
}

/* EDIT */
function editRepair(i){
  let r = repairs[i];

  document.getElementById("customer").value = r.customer;
  document.getElementById("device").value = r.device;
  document.getElementById("issue").value = r.issue;
  document.getElementById("price").value = r.price;

  editIndex = i;
  openModal();
}

/* RENDER */
function render(){

  let list = document.getElementById("list");
  list.innerHTML = "";

  repairs.forEach((r,i)=>{

    list.innerHTML += `
      <div class="bg-white p-5 rounded-3xl shadow">

        <h3 class="font-bold">${r.customer}</h3>
        <p>${r.device}</p>
        <p class="text-gray-500">${r.issue}</p>

        <div class="mt-2 font-bold">${r.price} DA</div>

        <div class="flex gap-2 mt-3">
          <button onclick="editRepair(${i})" class="text-blue-500">Edit</button>
          <button onclick="deleteRepair(${i})" class="text-red-500">Delete</button>
        </div>

      </div>
    `;
  });
}

render();

</script>

</body>
</html>
