<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Menú del Día - Cocina Mágica</title>

<!-- Icono para PWA -->
<link rel="icon" href="icono.png">
<link rel="manifest" href="manifest.json">

<style>
  @import url('https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@700&family=IM+Fell+English+SC&display=swap');
  body { margin:0; padding:0;  overflow-x:hidden; }
  .menu-btn { position: fixed; top: 20px; left: 20px; background: rgba(255,248,220,0.85); border: 2px solid #5d4037; border-radius: 10px; padding:10px 14px; font-size:1.6em; cursor:pointer; z-index:1000; color:#4e342e; box-shadow:2px 2px 10px rgba(0,0,0,0.3); }
  .notificacion { position: fixed; top: 20px; right: 25px; background: rgba(255,248,220,0.85); border: 2px solid #5d4037; border-radius: 50%; padding:12px; font-size:1.5em; cursor:pointer; z-index:1000; color:#4e342e; box-shadow: 2px 2px 10px rgba(0,0,0,0.3);}
  .notificacion::after { content:""; position:absolute; top:6px; right:6px; width:12px; height:12px; background:red; border-radius:50%; display:none; animation:parpadeo 1s infinite alternate; }
  .notificacion.activa::after { display:block; }
  @keyframes parpadeo { from {opacity:0.3; transform:scale(1);} to{opacity:1; transform:scale(1.2);} }

  .menu-lateral { position: fixed; top:0; left:-260px; width:250px; height:100%; background:rgba(255,248,220,0.95); border-right:3px solid #5d4037; transition:left 0.4s ease; padding-top:80px; z-index:900; box-shadow:3px 0 10px rgba(0,0,0,0.4);}
  .menu-lateral.mostrar { left:0; }
  .menu-lateral ul { list-style:none; padding:0; margin:0; text-align:center; }
  .menu-lateral li { padding:12px 0; font-size:1.2em; color:#4e342e; border-bottom:1px dashed #8d6e63; cursor:pointer; transition:background 0.3s;}
  .menu-lateral li:hover { background: rgba(139,69,19,0.15); }

  .contenedor { background: rgba(255,248,220,0.92); width:85%; max-width:600px; margin:100px auto; padding:40px; border:4px solid #5d4037; border-radius:15px; box-shadow:0 0 25px rgba(0,0,0,0.5); position:relative; }
  h1 { text-align:center; font-family:'Cinzel Decorative', serif; font-size:2.4em; margin-bottom:10px; color:#4e342e; text-shadow:0 0 5px rgba(0,0,0,0.3);}
  h2 { text-align:center; font-size:1.4em; color:#795548; border-bottom:2px dotted #8d6e63; display:inline-block; padding-bottom:5px;}
  .menu { margin-top:25px;}
  .menu-item { font-size:1.2em; margin:12px 0; display:flex; justify-content:space-between; border-bottom:1px dashed #a1887f; padding-bottom:6px;}
  #precio { text-align:center; font-size:1.4em; margin-top:25px; border-top:2px solid #8d6e63; color:#3e2723;}
  .contenedor::after { content:""; position:absolute; top:-5px; left:-5px; right:-5px; bottom:-5px; border-radius:15px; border:2px solid rgba(255,215,0,0.3); box-shadow:0 0 25px rgba(255,215,0,0.4); pointer-events:none; animation:brillo 5s infinite alternate ease-in-out;}
  @keyframes brillo { from{opacity:0.4;} to{opacity:0.8;} }

  #qr { display:block; margin:20px auto 0; width:120px; height:120px;}
</style>
</head>
<body>

<div class="menu-btn">☰</div>
<div class="notificacion" id="campana">🔔</div>
<div class="menu-lateral" id="menuLateral">
  <ul>
    <li data-dia="lunes">Lunes</li>
    <li data-dia="martes">Martes</li>
    <li data-dia="miércoles">Miércoles</li>
    <li data-dia="jueves">Jueves</li>
    <li data-dia="viernes">Viernes</li>
    <li data-dia="sábado">Sábado</li>
    <li data-dia="domingo">Domingo</li>
  </ul>
</div>

<div class="contenedor">
  <h1>🍲 Cocina Económica Mágica</h1>
  <h2 id="titulo-dia">Menú del Día</h2>
  <div class="menu">
    <div class="menu-item"><span>Sopa:</span> <span id="sopa"></span></div>
    <div class="menu-item"><span>Guisado:</span> <span id="guisado"></span></div>
    <div class="menu-item"><span>Acompañamiento:</span> <span id="acompanamiento"></span></div>
    <div class="menu-item"><span>Agua:</span> <span id="agua"></span></div>
    <div class="menu-item"><span>Postre:</span> <span id="postre"></span></div>
  </div>
  <div id="precio">💰 Menú completo: $70</div>
  <div id="qr"></div>
</div>

<script src="https://cdn.jsdelivr.net/npm/qrious/dist/qrious.min.js"></script>
<script>
  // --- Menú ---
  const menuSemana = {
    lunes: { sopa:"Fideos", guisado:"Pollo en mole", acompanamiento:"Frijoles y arroz", agua:"Jamaica", postre:"Flan", precio:70 },
    martes: { sopa:"Lentejas", guisado:"Bistec a la mexicana", acompanamiento:"Puré de papa", agua:"Tamarindo", postre:"Gelatina", precio:70 },
    miércoles: { sopa:"Sopa de verduras", guisado:"Tinga de pollo", acompanamiento:"Nopales con jitomate", agua:"Horchata", postre:"Plátano con crema", precio:70 },
    jueves: { sopa:"Caldo de pollo", guisado:"Enchiladas verdes", acompanamiento:"Verduras al vapor", agua:"Limón con chía", postre:"Arroz con leche", precio:70 },
    viernes: { sopa:"Arroz con verduras", guisado:"Pescado empanizado", acompanamiento:"Ensalada fresca", agua:"Jamaica", postre:"Gelatina de mosaico", precio:70 },
    sábado: { sopa:"Sopa de coditos", guisado:"Carne guisada", acompanamiento:"Arroz blanco", agua:"Horchata", postre:"Pastelito de chocolate", precio:70 },
    domingo: { sopa:"Crema de elote", guisado:"Barbacoa", acompanamiento:"Frijoles refritos", agua:"Agua de piña", postre:"Flan napolitano", precio:70 }
  };

  const mostrarMenu = dia => {
    const datos = menuSemana[dia];
    document.getElementById("titulo-dia").textContent = "Menú del " + dia.charAt(0).toUpperCase() + dia.slice(1);
    document.getElementById("sopa").textContent = datos.sopa;
    document.getElementById("guisado").textContent = datos.guisado;
    document.getElementById("acompanamiento").textContent = datos.acompanamiento;
    document.getElementById("agua").textContent = datos.agua;
    document.getElementById("postre").textContent = datos.postre;
    document.getElementById("precio").textContent = `💰 Menú completo: $${datos.precio}`;
    const prevVersion = localStorage.getItem("menuActual");
    const nuevaVersion = JSON.stringify(datos);
    if(prevVersion !== nuevaVersion && JSON.parse(localStorage.getItem("notificaciones"))) {
      // Enviar notificación push
      if("serviceWorker" in navigator && Notification.permission==="granted") {
        navigator.serviceWorker.ready.then(registration => {
          registration.showNotification("🍲 Menú actualizado", { body:`El menú del ${dia} ha cambiado.`, icon:"icono.png"});
        });
      }
      document.getElementById("campana").classList.add("activa");
    }
    localStorage.setItem("menuActual", nuevaVersion);
  };

  // Preguntar notificaciones
  if(localStorage.getItem("notificaciones")===null) {
    if(confirm("¿Deseas recibir notificaciones cuando el menú se actualice?")) {
      localStorage.setItem("notificaciones", true);
      Notification.requestPermission();
    } else { localStorage.setItem("notificaciones", false); }
  }

  // Mostrar menú actual por defecto
  const dias = ["domingo","lunes","martes","miércoles","jueves","viernes","sábado"];
  const hoy = dias[new Date().getDay()];
  mostrarMenu(hoy);

  // Menú lateral
  const menuBtn = document.querySelector(".menu-btn");
  const menuLateral = document.getElementById("menuLateral");
  menuBtn.addEventListener("click",()=>menuLateral.classList.toggle("mostrar"));
  document.querySelectorAll(".menu-lateral li").forEach(item=>{
    item.addEventListener("click", e=>{
      const dia = e.target.getAttribute("data-dia");
      mostrarMenu(dia);
      menuLateral.classList.remove("mostrar");
    });
  });

  // Generar QR dinámico
  const qr = new QRious({ element: document.getElementById('qr'), value: window.location.href, size:120 });

  // Registrar Service Worker
  if('serviceWorker' in navigator){
    navigator.serviceWorker.register('sw.js').then(()=>console.log("Service Worker registrado"));
  }
</script>
</body>
</html>
