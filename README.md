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

<img src="./file_000000006be0820e87a9e58fd4ed056f.png" alt="Logo Laburo Cerca" style="width:80px; height:auto;">
  <h1>Laburo Cerca</h1>
</div>

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

<section style="max-width:600px;margin:30px auto;padding:20px;border-radius:12px;background:#f0fdf4;text-align:center;font-family:Arial,sans-serif;">
  <h2>📲 Publicá tu oferta en Laburo Cerca</h2>
  <p>¿Buscás empleados? Enviá los datos de tu oferta por WhatsApp.</p>

  
<form id="formOferta">
  <input name="puesto" placeholder="Puesto de trabajo" required
    style="width:90%;padding:12px;margin:6px;"><br>

  <input name="localidad" placeholder="Localidad" required
    style="width:90%;padding:12px;margin:6px;"><br>

  <textarea name="descripcion" placeholder="Descripción de la oferta" required
    style="width:90%;padding:12px;margin:6px;"></textarea><br>

  <input name="contacto" placeholder="Teléfono de contacto del empleador" required
    style="width:90%;padding:12px;margin:6px;"><br>

  <button type="submit"
    style="padding:12px 20px;background:#16a34a;color:white;border:0;border-radius:8px;font-weight:bold;">
    Enviar oferta
  </button>

  <p id="estadoOferta" role="status"></p>
</form>

<script>
const estadoOferta = document.getElementById("estadoOferta");

formOferta.addEventListener("submit", async function(event) {
  event.preventDefault();

  const boton = formOferta.querySelector('button[type="submit"]');
  boton.disabled = true;
  estadoOferta.textContent = "Enviando oferta...";

  const datos = new FormData(formOferta);

  const oferta = {
    puesto: datos.get("puesto").trim(),
    localidad: datos.get("localidad").trim(),
    descripcion: datos.get("descripcion").trim(),
    contacto: datos.get("contacto").trim()
  };

  try {
    await guardarOferta(oferta);
    estadoOferta.textContent =
      "¡Oferta enviada correctamente! Quedará pendiente de aprobación.";
    formOferta.reset();
  } catch (error) {
    console.error("Error al publicar:", error);
    estadoOferta.textContent =
      "No se pudo publicar: " + error.message;
  } finally {
    boton.disabled = false;
  }
});
</script>

<script>
document.getElementById("formOferta").addEventListener("submit", async function(event) {
  event.preventDefault();

  const formulario = this;
  const estado = document.getElementById("estadoOferta");
  const datos = new FormData(formulario);

  estado.textContent = "Enviando oferta...";

  try {
    const guardar = window.guardarOferta;
if (typeof guardar !== "function") {
  throw new Error("No se encontró la función guardarOferta. Revisá el bloque script de Supabase.");
}
await guardar({
      puesto: datos.get("puesto").trim(),
      localidad: datos.get("localidad").trim(),
      descripcion: datos.get("descripcion").trim(),
      contacto: datos.get("contacto").trim()
    });

    estado.textContent = "¡Oferta enviada! Quedó pendiente de revisión.";
    formulario.reset();
  

} catch (error) {
  estado.textContent = error.message || "No se pudo enviar.";
  console.error(error);
}
});
</script>


  <p><strong>⭐ Destacá tu oferta por $10.000 durante 7 días.</strong></p>
  <p>El envío de la oferta no confirma el pago. La publicación destacada se coordina después de verificarlo.</p>
</section>


<script>
const SUPABASE_URL = "https://qjtdxoupxcbbolwjugco.supabase.co";
const SUPABASE_KEY = "sb_publishable_gF3nfe2FZvsjIsZN32BK1w_IkOX26eV";

async function guardarOferta(oferta) {
  const respuesta = await fetch(
    `${SUPABASE_URL}/rest/v1/ofertas`,
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "apikey": SUPABASE_KEY,
        "Prefer": "return=minimal"
      },
      body: JSON.stringify({
        id: crypto.randomUUID(),
        puesto: oferta.puesto,
        localidad: oferta.localidad,
        descripcion: oferta.descripcion,
        contacto: oferta.contacto,
        estado: "pendiente"
      })
    }
  );

  if (!respuesta.ok) {
    const detalle = await respuesta.text();
    throw new Error(
      "Error " + respuesta.status + ": " + detalle
    );
  }

  return true;
}
</script>


 </body>
</html>
