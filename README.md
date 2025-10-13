<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cálculo de Nómina</title>
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      background: linear-gradient(135deg, #e1bee7, #f8bbd0);
      color: #4a148c;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      padding: 20px;
    }

    h1 {
      color: #6a1b9a;
      margin-bottom: 20px;
      text-shadow: 1px 1px 2px #fff;
    }

    form {
      background: #fff;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      width: 100%;
      max-width: 400px;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    label {
      font-weight: bold;
      color: #6a1b9a;
    }

    input[type="text"],
    input[type="email"],
    input[type="number"] {
      padding: 8px;
      border-radius: 10px;
      border: 1px solid #ccc;
      outline: none;
      transition: 0.2s;
    }

    input:focus {
      border-color: #ba68c8;
    }

    .checkbox {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    button {
      background: #ba68c8;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-weight: bold;
      transition: 0.3s;
    }

    button:hover {
      background: #8e24aa;
    }

    .resultado {
      margin-top: 25px;
      background: white;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      text-align: center;
      max-width: 400px;
      display: none;
      animation: aparecer 0.5s ease-in-out;
    }

    @keyframes aparecer {
      from {opacity: 0; transform: translateY(10px);}
      to {opacity: 1; transform: translateY(0);}
    }

    .resultado h2 {
      color: #6a1b9a;
    }
  </style>
</head>
<body>
  <h1>💼 Calculadora de Nómina</h1>

<form action ="nomina.php" method="POST">
  <form id="formNomina">
    <label>Correo:</label>
    <input type="email" id="correo" required>

    <label>Nombre:</label>
    <input type="text" id="nombre" required>

    <label>Apellido:</label>
    <input type="text" id="apellido" required>

    <label>Días trabajados:</label>
    <input type="number" id="dias" min="0" required>

    <label>Pago por día:</label>
    <input type="number" id="pagoDia" min="0" required>

    <label>Sueldo inicial:</label>
    <input type="number" id="sueldoInicial" min="0" required>

    <div class="checkbox">
      <input type="checkbox" id="hijos">
      <label for="hijos">¿Tiene hijos?</label>
    </div>

    <button type="submit">Calcular Sueldo Neto</button>
  </form>

  <div class="resultado" id="resultado">
    <h2>Resultado de Nómina</h2>
    <p id="infoEmpleado"></p>
    <p id="sueldoNeto"></p>
  </div>

 

<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
<script type="text/javascript">
  (function(){
    emailjs.init("p6_WKjZLqXZQFx5Oo"); // 💌 Reemplaza TU_USER_ID por tu ID de EmailJS
  })();
</script>
<script>
    document.getElementById("formNomina").addEventListener("submit", function(e) {
  e.preventDefault();

  const correo = document.getElementById("correo").value;
  const nombre = document.getElementById("nombre").value;
  const apellido = document.getElementById("apellido").value;
  const dias = parseFloat(document.getElementById("dias").value);
  const pagoDia = parseFloat(document.getElementById("pagoDia").value);
  const sueldoInicial = parseFloat(document.getElementById("sueldoInicial").value);
  const tieneHijos = document.getElementById("hijos").checked;

  // Calcular bono
  let bono = 0;
  if (sueldoInicial <= 1500) bono = sueldoInicial * 0.10;
  else if (sueldoInicial <= 3000) bono = sueldoInicial * 0.20;
  else bono = sueldoInicial * 0.25;

  if (tieneHijos) bono *= 2;

  const sueldoNeto = sueldoInicial + bono;

  // Mostrar resultado en pantalla
  document.getElementById("infoEmpleado").innerHTML = `
    <strong>${nombre} ${apellido}</strong><br>
    📧 ${correo}<br>
    Días trabajados: ${dias}<br>
    Pago por día: $${pagoDia.toFixed(2)}<br>
    Bono aplicado: $${bono.toFixed(2)}
  `;
  document.getElementById("sueldoNeto").innerHTML = <strong>Sueldo Neto: $${sueldoNeto.toFixed(2)}</strong>;
  document.getElementById("resultado").style.display = "block";

  // Enviar correo con EmailJS
  const templateParams = {
    nombre,
    apellido,
    correo,
    dias,
    pagoDia: pagoDia.toFixed(2),
    bono: bono.toFixed(2),
    sueldoNeto: sueldoNeto.toFixed(2),
  };

  // Enviar al administrador
  emailjs.send("service_onc2yzj", "template_gzatylx", {
    ...templateParams,
    to_email: "estrellanhl1109@gmail.com" // Reemplaza con tu correo de admin
  });

  // Enviar al empleado
  emailjs.send("service_onc2yzj", "template_0owvsiv", {
    ...templateParams,
    to_email: correo
  });
});
  </script>
  </form>

</body>
</html>
