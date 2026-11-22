
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Gold Case Simulator</title>
<style>
body{background:#101522;color:white;font-family:Arial;text-align:center}
h1{color:gold}
.gold{font-size:30px;color:gold}
.cases{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}
.case,.item{background:#252f45;padding:15px;border-radius:12px}
button{background:gold;padding:10px;border:0;border-radius:8px;cursor:pointer;font-weight:bold}
#result{margin:20px;padding:20px;background:#252f45;border-radius:12px}
#inventory{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
</style>
</head>
<body>

<h1>🎁 GOLD CASE</h1>
<h2>Твоя голда:</h2>
<div class="gold" id="gold">1000</div>

<h2>МАГАЗИН КЕЙСОВ</h2>
<div class="cases" id="cases"></div>

<div id="result">Открой свой первый кейс!</div>

<h2>🎒 ИНВЕНТАРЬ</h2>
<div id="inventory"></div>

<script>
let gold=1000;
let inventory=[];
let id=0;

let prices=[
100,1000,10000,50000,100000,
500000,1000000,5000000,10000000,
25000000,50000000,100000000,
250000000,500000000,1000000000,
2500000000,5000000000,10000000000,
25000000000,50000000000
];

let rarities=[
{name:"Редкий",chance:55,color:"#55aaff",mult:0.65},
{name:"Суперредкий",chance:25,color:"#bb66ff",mult:1.15},
{name:"Эпический",chance:13,color:"#ff55dd",mult:2.2},
{name:"Мифический",chance:5.5,color:"#ff8844",mult:5},
{name:"Легендарный",chance:1.5,color:"#ffcc33",mult:14}
];

let skins=[
"AK-47 | Неоновый гонщик",
"AWP | Ледяной дракон",
"M4A1-S | Тёмная вода",
"USP-S | Каменный холод",
"Glock-18 | Пиксельный огонь",
"Desert Eagle | Золотой вихрь",
"P90 | Космическая пыль",
"★ Нож | Звёздный клинок",
"AK-47 | Красная линия",
"AWP | Солнечный удар"
];

function fmt(n){
return Math.floor(n).toLocaleString("ru-RU");
}

function update(){
document.getElementById("gold").textContent=fmt(gold);

document.getElementById("cases").innerHTML=
prices.map((p,i)=>`
<div class="case">
<h2>📦 Кейс ${i+1}</h2>
<p>${fmt(p)} голды</p>
<button onclick="openCase(${i})" ${gold<p?"disabled":""}>
ОТКРЫТЬ
</button>
</div>`).join("");

drawInventory();
}

function randomRarity(){
let r=Math.random()*100;
let sum=0;
for(let x of rarities){
sum+=x.chance;
if(r<sum)return x;
}
return rarities[0];
}

function openCase(i){
let price=prices[i];
if(gold<price)return;

gold-=price;
let rarity=randomRarity();
let skin=skins[Math.floor(Math.random()*skins.length)];

let value=Math.max(10,Math.round(
price*rarity.mult*(0.72+Math.random()*0.75)
));

let item={
id:++id,
name:skin,
rarity:rarity.name,
color:rarity.color,
value:value
};

inventory.push(item);

document.getElementById("result").innerHTML=`
<h2>🎉 ТЕБЕ ВЫПАЛО!</h2>
<h2>${skin}</h2>
<h2 style="color:${rarity.color}">${rarity.name}</h2>
<p>Цена: ${fmt(value)} голды</p>
`;

update();
}

function drawInventory(){
let el=document.getElementById("inventory");

if(inventory.length===0){
el.innerHTML="Пока нет скинов";
return;
}

el.innerHTML=inventory.map(x=>`
<div class="item">
<h3>${x.name}</h3>
<p style="color:${x.color}">${x.rarity}</p>
<p>${fmt(x.value)} голды</p>
<button onclick="sell(${x.id})">ПРОДАТЬ</button>
</div>
`).join("");
}

function sell(id){
let index=inventory.findIndex(x=>x.id===id);
if(index<0)return;

gold+=inventory[index].value;
inventory.splice(index,1);
update();
}

update();
</script>
</body>
</html>
