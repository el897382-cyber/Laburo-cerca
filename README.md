<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Laburo Cerca</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f1f5f9;color:#172033}
header{background:#087f5b;color:white;padding:28px 16px;text-align:center}
header h1{margin:0;font-size:32px}
header p{margin-bottom:0}
main{max-width:800px;margin:auto;padding:16px}
.panel,.trabajo{background:white;padding:18px;border-radius:14px;margin-bottom:16px;box-shadow:0 2px 8px #0000000c}
h2{margin-top:0;font-size:21px}
input,select,textarea,button{width:100%;padding:12px;margin:6px 0;border-radius:8px;font-size:16px}
input,select,textarea{border:1px solid #cbd5e1;background:white}
button{border:0;background:#087f5b;color:white;font-weight:bold;cursor:pointer}
button:hover{opacity:.9}
.trabajo h3{margin:0 0 8px;color:#087f5b}
.etiqueta{display:inline-block;background:#d1fae5;padding:5px 9px;border-radius:20px;font-size:13px;margin:3px 0}
.mensaje{color:#475569;font-size:14px}
footer{text-align:center;padding:22px;color:#64748b}
</style>
</head>
<body>
<header>
<h1>📍 Laburo Cerca</h1>
<p>Encontrá trabajo y changas cerca de vos 🇦🇷</p>
</header>

<main>
<section class="panel">
<h2>🔎 Buscar trabajo</h2>
<input id="buscar" placeholder="¿Qué trabajo buscás? Ej. albañil">
<input id="localidad" placeholder="Tu localidad. Ej. Don Torcuato">
<select id="filtro">
<option value="">Todos los oficios</option>
<option>Construcción</option>
<option>Limpieza</option>
<option>Reparto</option>
<option>Gastronomía</option>
<option>Otros</option>
</select>
<button onclick="mostrar()">Buscar ofertas</button>
<button onclick="limpiar()" style="background:#64748b">Ver todas las ofertas</button>
</section>

<section class="panel">
<h2>📢 Ofertas de trabajo</h2>
<p class="mensaje">Estas ofertas iniciales son ejemplos para mostrar cómo funciona la página.</p>
<div id="lista"></div>
</section>

<section class="panel">
<h2>➕ Publicar una changa</h2>
<p class="mensaje">Completá los datos para agregar una oferta en este navegador.</p>
<form id="formulario">
<input id="puesto" placeholder="¿Qué trabajo ofrecés?" required>
<select id="rubro" required>
<option value="">Elegí el oficio</option>
<option>Construcción</option>
<option>Limpieza</option>
<option>Reparto</option>
<option>Gastronomía</option>
<option>Otros</option>
</select>
<input id="zona" placeholder="Localidad o barrio" required>
<textarea id="descripcion" placeholder="Descripción del trabajo" required rows="3"></textarea>
<input id="telefono" type="tel" placeholder="WhatsApp con código de país (ej. 54911...)" required>
<button type="submit">Publicar oferta</button>
</form>
</section>
</main>

<footer>Laburo Cerca 🇦🇷 · Conectando personas con oportunidades</footer>

<script>
const ejemplos=[
{puesto:"Ayudante de construcción",rubro:"Construcción",zona:"Don Torcuato",descripcion:"Ejemplo demostrativo de una oferta para ayudante de obra.",telefono:""},
{puesto:"Personal de limpieza",rubro:"Limpieza",zona:"Tigre",descripcion:"Ejemplo demostrativo de trabajo de limpieza.",telefono:""},
{puesto:"Ayudante de cocina",rubro:"Gastronomía",zona:"San Fernando",descripcion:"Ejemplo demostrativo para ayudante de cocina.",telefono:""}
];

let ofertas=[];
try{
 ofertas=JSON.parse(localStorage.getItem("laburoCercaOfertas"))||[];
}catch(e){ofertas=[];}

function guardar(){
 localStorage.setItem("laburoCercaOfertas",JSON.stringify(ofertas));
}

function mostrar(){
 const texto=document.getElementById("buscar").value.toLowerCase().trim();
 const zona=document.getElementById("localidad").value.toLowerCase().trim();
 const rubro=document.getElementById("filtro").value;
 const lista=document.getElementById("lista");
 const todas=[...ofertas,...ejemplos];
 const resultados=todas.filter(t=>
  (t.puesto+" "+t.descripcion+" "+t.rubro).toLowerCase().includes(texto)&&
  t.zona.toLowerCase().includes(zona)&&
  (!rubro||t.rubro===rubro)
 );
 lista.innerHTML="";
 if(!resultados.length){
  lista.innerHTML="<p>No encontramos ofertas con esos filtros. Probá con otra búsqueda.</p>";
  return;
 }
 resultados.forEach(t=>{
  const tarjeta=document.createElement("article");
  tarjeta.className="trabajo";
  const titulo=document.createElement("h3");
  titulo.textContent=t.puesto;
  const etiqueta=document.createElement("span");
  etiqueta.className="etiqueta";
  etiqueta.textContent=t.rubro;
  const lugar=document.createElement("p");
  lugar.textContent="📍 "+t.zona;
  const desc=document.createElement("p");
  desc.textContent=t.descripcion;
  tarjeta.append(titulo,etiqueta,lugar,desc);
  if(t.telefono){
   const boton=document.createElement("button");
   boton.textContent="📲 Contactar por WhatsApp";
   boton.onclick=()=>{
    const numero=t.telefono.replace(/\D/g,"");
    if(numero) window.open("https://wa.me/"+numero,"_blank");
   };
   tarjeta.appendChild(boton);
  }else{
   const nota=document.createElement("p");
   nota.className="mensaje";
   nota.textContent="Oferta de ejemplo: todavía no tiene contacto real.";
   tarjeta.appendChild(nota);
  }
  lista.appendChild(tarjeta);
 });
}

document.getElementById("formulario").addEventListener("submit",function(e){
 e.preventDefault();
 const nueva={
  puesto:document.getElementById("puesto").value.trim(),
  rubro:document.getElementById("rubro").value,
  zona:document.getElementById("zona").value.trim(),
  descripcion:document.getElementById("descripcion").value.trim(),
  telefono:document.getElementById("telefono").value.trim()
 };
 if(!nueva.puesto||!nueva.zona||!nueva.descripcion||!nueva.telefono){
  alert("Completá todos los campos.");
  return;
 }
 ofertas.unshift(nueva);
 guardar();
 this.reset();
 mostrar();
 alert("¡Oferta agregada en este navegador!");
 document.getElementById("lista").scrollIntoView({behavior:"smooth"});
});

function limpiar(){
 document.getElementById("buscar").value="";
 document.getElementById("localidad").value="";
 document.getElementById("filtro").value="";
 mostrar();
}

mostrar();
</script> 
<section style="max-width:800px;margin:20px auto;padding:24px;background:#fff;border-radius:16px;text-align:center;font-family:Arial,sans-serif;box-shadow:0 2px 10px #ddd">
  <h2>⭐ Destacá tu oferta</h2>
  <p>Conseguí más visibilidad para tu publicación en Laburo Cerca.</p>
  <h3>$10.000 ARS · 7 días</h3>
  <p>La publicación destacada se activa después de verificar el pago.</p>
  <a href="https://mpago.la/21zd4XH" target="_blank" rel="noopener noreferrer" style="display:inline-block;padding:14px 22px;background:#009ee3;color:white;text-decoration:none;border-radius:10px;font-weight:bold">
    Pagar con Mercado Pago
  </a>
</section>
</body>
</html>
