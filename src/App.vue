<script setup>
import { ref } from "vue";
import Swal from "sweetalert2";

let array = ref([]);

// Formulario
let premio = ref("");
let precioBoleta = ref("");
let tipoLoteria = ref("");
let fechaSorteo = ref("");

let fsDate;
/* let fsAño = fsDate.getFullYear();
let fsMes = fsDate.getMonth();
let fsDia = fsDate.getDate(); */
let hoy = new Date();
hoy.setHours(0, 0, 0, 0);
/* let hAño = hoy.getFullYear();
let hMes = hoy.getMonth();
let hDia = hoy.getDate(); */

let mostrarFormulario = ref(true);

let mostrarInformacion = ref(false);

let reservada = ref("red");
let boletaReservada = ref(false);
let boletaDisponible = ref(false);

let boletaSeleccionada = ref(null);
let boletasAdquirir = ref([]);
let mostrarFormularioAdquisicion = ref(false);
let resetFormularioAdquisicion = ref(true);
let botonSeleccionado = ref('si');

let boletas = ref([]);

function generarLoteria() {
  fsDate = new Date(fechaSorteo.value + "T00:00:00");

  if (
    premio.value == "" &&
    precioBoleta.value == "" &&
    tipoLoteria.value == "" &&
    fechaSorteo.value == ""
  ) {
    Swal.fire({
      text: "Los datos están vacíos",
      icon: "error",
    });
  } else if (premio.value == "") {
    Swal.fire({
      text: "El premio está vacío",
      icon: "error",
    });
  } else if (isNaN(premio.value)) {
    Swal.fire({
      text: "Solo números en premio",
      icon: "warning",
    });
  } else if (precioBoleta.value == "") {
    Swal.fire({
      text: "El valor de la boleta está vacío",
      icon: "error",
    });
  } else if (isNaN(precioBoleta.value)) {
    Swal.fire({
      text: "Solo números en premio",
      icon: "warning",
    });
  } else if (tipoLoteria.value == "") {
    Swal.fire({
      text: "Seleccione un tipo de lotería",
      icon: "error",
    });
  } else if (fechaSorteo.value == "") {
    Swal.fire({
      text: "Ingrese la fecha final",
      icon: "error",
    });
  } else if (fsDate <= hoy) {
    Swal.fire({
      text: "Ingrese una fecha final válida",
      icon: "warning",
    });
  } else {
    mostrarInformacion.value = true;
    Swal.fire({
      text: "¡Gracias por usar este Talonario!",
      icon: "success",
    });

    // Reiniciar el array de boletas
    boletas.value = [];

    for (let i = 0; i < 100; i++) {
      boletas.value.push({
        i,
        estado: 'disponible'
      });
    }

    mostrarFormulario.value = false;

    console.log(boletas.value)
  }
}

function editar() {
  mostrarFormulario.value = true;
  mostrarInformacion.value = false;
}

function modificarBoleta(_elemento, _indice) {
  if (_elemento.estado === 'disponible') {
    boletaDisponible.value = true;

    // Set the selected ball information for display
    boletaSeleccionada.value = {
      numero: _elemento.i,
      estado: _elemento.estado,
    };
  } else {
    // Reset the selected ball information when a reserved ball is clicked
    boletaSeleccionada.value = null;
  }
}

function adquirirBoleta() {
  // Hide the available ticket information
  boletaDisponible.value = false;

  // Show the acquisition form
  mostrarFormularioAdquisicion.value = true;
}

function cancelarAdquisicion() {
  // Clear the selected ball information and acquired tickets
  boletaSeleccionada.value = null;
  boletasAdquirir.value = [];
}

function confirmarAdquisicion() {
  // Validar que los campos obligatorios no estén vacíos
  const nombre = document.getElementById('nombre').value;
  const telefono = document.getElementById('telefono').value;
  const direccion = document.getElementById('direccion').value;

  if (!nombre || !telefono || !direccion) {
    // Muestra un mensaje de error o realiza alguna acción
    alert('Por favor completa todos los campos obligatorios.');
    return;
  }
  // Handle the logic for confirming the acquisition
  // You can add further processing here

  // After processing, clear the selected ball information and acquired tickets
  boletaSeleccionada.value = null;
  boletasAdquirir.value = [];

  // Hide the acquisition form
}

function seleccionarBoton(boton) {
  botonSeleccionado.value = boton;
}

function cambiarBoleta() {
  // Reset and hide the acquisition form
  resetFormularioAdquisicion.value = !resetFormularioAdquisicion.value;
  mostrarFormularioAdquisicion.value = false;

  // Puedes agregar lógica adicional aquí según tus necesidades
}

</script>

<template>
  <div id="all">

    <div id="header">
      <h1>TALONARIO</h1>
    </div>

    <div id="footer">
      <footer>©️ Copyright 2024 - Todos los derechos reservados</footer>
    </div>

    <div id="boletaDesplegableReserva" v-if="boletaReservada == true">
      <h3>Boleta 00</h3>
      <p>Estado: <span>Reservada <span>{{ reservada == 'red' ? '🔴' : '🔵' }}</span></span></p>
      <div>
        <button>Ver datos del participante</button>
        <button>Liberar boleta</button>
        <button>Marcar como pagada</button>
      </div>
    </div>

    <div id="boletaDesplegableDisponible" v-if="boletaDisponible == true">
      <h3>Boleta {{ boletaSeleccionada.numero < 10 ? '0' + boletaSeleccionada.numero : boletaSeleccionada.numero }}</h3>
          <p>Estado: <span>Disponible ⚫</span></p>
          <div>
            <button @click="adquirirBoleta()">🤝 Adquirir boleta</button>
          </div>
    </div>

    <div id="formularioAdquisicion" v-if="mostrarFormularioAdquisicion == true">
      <div>
        <div class="header">
          <h3>Diligencia la Información</h3>
          <p>Boletas a adquirir: {{ boletasAdquirir.join(', ') }}</p>
        </div>

        <div class="content" v-if="!mostrarFormularioAdquisicion">
          <div>
            <label for="nombre"></label>
            <input type="text" id="nombre" placeholder=" 🤔 Nombre" required>
          </div>
          <div>
            <label for="telefono"></label>
            <input type="text" id="telefono" placeholder=" 📞 Teléfono" required>
          </div>
          <div>
            <label for="direccion"></label>
            <input type="text" id="direccion" placeholder=" ↙️ Dirección" required>
          </div>
        </div>


        <div class="footerrrr">
          <p>Paga ya:</p>
          <div class="hijoFooter">
            <input type="button" @click.prevent="seleccionarBoton('si')"
              :style="{ 'background-color': botonSeleccionado === 'si' ? 'blue' : '' }"
              style="border-radius: 50%; width: 18px; height: 18px;">
            <label for="si">Si</label>

            <input type="button" @click.prevent="seleccionarBoton('no')"
              :style="{ 'background-color': botonSeleccionado === 'no' ? 'blue' : '' }"
              style="border-radius: 50%; width: 18px; height: 18px;">
            <label for="no">No</label>
          </div>
          <div class="linea"></div>
          <button style="background-color: blue; color: white; font-size: 12px; margin-bottom: 20px;">ADQUIRIR</button>
        </div>

      </div>
    </div>


  </div>



  <main>

    <section id="informacion">
      <h2>INFORMACIÓN</h2>
      <div id="infoContenido">
        <p>
          🏆<span>{{ mostrarInformacion == true ? premio : "" }}</span>
        </p>
        <p>
          💰<span>{{ mostrarInformacion == true ? precioBoleta : "" }}</span>
        </p>
        <p>
          🏦<span>{{ mostrarInformacion == true ? tipoLoteria : "" }}</span>
        </p>
        <p>
          🗓️<span>{{ mostrarInformacion == true ? fechaSorteo : "" }}</span>
        </p>
        <button @click="editar()">Editar ✏️</button>
      </div>
    </section>

    <section id="loteria">
      <button v-for="(e, i) in boletas" :key="i" @click="modificarBoleta(e, i); cambiarBoleta();">{{ e.i < 10 ? '0' + e.i
        : e.i }}</button>
    </section>

    <section id="accion">
      <h2>ACCIONES</h2>
      <div id="accContenido">
        <button id="estadoB">ESTADO</button>
        <button>LISTAR BOLETAS</button>
        <button>PERSONALIZAR TALONARIO WEB</button>
        <button>GENERAR ARCHIVO DE DATOS</button>
      </div>
    </section>

    <div id="fondo" v-if="mostrarFormulario == true"></div>

    <section id="formulario" v-if="mostrarFormulario == true">
      <h2>CONFIGURA TU TALONARIO</h2>
      <div id="forContenido">
        <input type="text" placeholder="Premio" v-model="premio" />
        <input type="text" placeholder="Valor Boleta" v-model="precioBoleta" />
        <select v-model="tipoLoteria">
          <option disabled value="">
            Selecciona una lotería
          </option>
          <option value="Baloto" label="Baloto"></option>
          <option value="Lotería de Boyacá" label="Lotería de Boyacá"></option>
          <option value="Lotería de Medellín" label="Lotería de Medellín"></option>
          <option value="Lotería de Bogotá" label="Lotería de Bogotá"></option>
          <option value="Lotería del Valle" label="Lotería del Valle"></option>
          <option value="Lotería del Tolima" label="Lotería del Tolima"></option>
          <option value="Super Astro" label="Super Astro"></option>
          <option value="Lotería del Huila" label="Lotería del Huila"></option>
          <option value="La Culona" label="La Culona"></option>
        </select>
        <input type="date" id="editDate" v-model="fechaSorteo" />
        <button type="button" @click="generarLoteria()">GUARDAR</button>
      </div>
    </section>

  </main>
</template>

<style scoped>
body {
  width: 100% !important;
  background-color: white;
}

#header {
  position: fixed;
  top: 0;
  left: 50%;
  transform: translate(-50%);
  background-color: blue;
  font-size: 10px;
  padding: 5px 0 5px 0;
  width: 100%;
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.26);
}

#footer {
  position: fixed;
  bottom: 0;
  left: 50%;
  transform: translate(-50%);
  background-color: blue;
  padding: 5px 0 5px 0;
  width: 100%;
}

main {
  padding: 90px 0 0 0;
  display: flex;
  flex-direction: row;
  justify-content: center;
}

#informacion {
  margin-top: 10rem;
  color: black;
  background-color: rgb(211, 211, 211);
  border-radius: 20px;
  padding: 0 15px 15px 15px;
  width: 250px;
  height: 290px;
}

#informacion h2 {
  font-weight: 400;
  font-size: 15px;
}

#infoContenido {
  text-align: start;
  background-color: white;
  box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  padding: 5px 15px 15px 15px;
  border-radius: 5px;
}

#informacion button {
  position: relative;
  left: 50%;
  transform: translate(-50%);
  background-color: blue;
  font-size: 14px;
  font-weight: 400;
  padding: 5px 10px 7px 10px;
}

#accion {
  margin-top: 10rem;
  color: #000000;
  background-color: rgb(211, 211, 211);
  border-radius: 20px;
  padding: 0 15px 15px 15px;
  width: 250px;
  height: 290px;
}

#accion h2 {
  font-weight: 400;
  font-size: 15px;
}

#accContenido {
  text-align: center;
  background-color: white;
  box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  padding: 5px 15px 15px 15px;
  border-radius: 5px;
  font-size: 12px;
}

#accion button {
  width: 140px;
  margin-bottom: 5px;
  border-radius: 6px;
  background-color: blue;
}

#accion #estadoB {
  margin-top: 21px;
  margin-bottom: 10px;
  background-color: blueviolet;
  border-radius: 2px;
}

#fondo {
  position: absolute;
  background-color: #95a5a656;
  width: 100%;
  height: 100%;
  top: 0;
}

#formulario {
  display: flex;
  flex-direction: column;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  align-items: center;
  background-color: aliceblue;
  border: 1px solid black;
}

#formulario h2 {
  background-color: blue;
  padding: 5px 40px;
  font-size: 15px;
  position: relative;
  bottom: 12.2px;
}

#forContenido {
  display: flex;
  flex-direction: column;
}

#forContenido input[type="text"] {
  width: 202px;
  padding: 5px 10px 5px 10px;
  background-color: white;
  border: 1px solid gray;
  color: black;
  margin-bottom: 20px;
}

#forContenido select {
  width: 224px;
  padding: 5px 10px 5px 10px;
  background-color: white;
  border: 1px solid gray;
  color: black;
  margin-bottom: 20px;
}

#forContenido input[type="date"] {
  width: 202px;
  padding: 5px 10px 5px 10px;
  background-color: white;
  border: 1px solid gray;
  color: black;
  margin-bottom: 20px;
}

#forContenido button {
  background-color: blue;
  font-size: 13px;
  width: 100px;
  padding: 5px 10px 5px 10px;
  border-radius: 0;
  position: relative;
  left: 50%;
  transform: translate(-50%);
  margin-bottom: 20px;
}

#editDate {
  width: 201px;
  padding: 5px 10px 5px 10px;
}

#loteria {
  width: 40rem;
  height: 30rem;
  margin-left: 40px;
  margin-right: 40px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 10px;
  overflow-y: auto;
}

::-webkit-scrollbar {
  width: 6px;
}

::-webkit-scrollbar-thumb {
  background-color: #95a5a6;
  border-radius: 3px;
}

::-webkit-scrollbar-track {
  background-color: #ecf0f1;
}

#loteria button {
  border-radius: 50%;
  width: 1px;
  height: 1px;
  padding: 25px;
  display: flex;
  justify-content: center;
  align-items: center;
}

#boletaDesplegableReserva {
  display: flex;
  flex-direction: column;
  background-color: #fff;
  color: black;
  width: 18rem;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translate(-50%);
}

#boletaDesplegableReserva h3 {
  margin: 10px 0 5px 0;
}

#boletaDesplegableReserva p {
  margin: 5px 0 10px 0;
}

#boletaDesplegableReserva div {
  display: flex;
  flex-direction: column;
}

#boletaDesplegableReserva div button {
  background-color: #fff;
  color: #000;
  text-align: left;
}

#boletaDesplegableDisponible {
  display: flex;
  flex-direction: column;
  background-color: #fff;
  color: black;
  width: 18rem;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translate(-50%);
}

#boletaDesplegableDisponible h3 {
  margin: 10px 0 5px 0;
}

#boletaDesplegableDisponible p {
  margin: 5px 0 10px 0;
}

#boletaDesplegableDisponible div {
  display: flex;
  flex-direction: column;
}

#boletaDesplegableDisponible div button {
  background-color: #fff;
  color: #000;
  text-align: left;
}

.hijoFooter {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-top: -10px;
  padding-bottom: 10px;
}

#formularioAdquisicion {
  width: 18rem;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translate(-50%);
  border: 1px solid red;
  background-color: white;
  color: black;
  z-index: 999;
  margin-bottom: 10px;
}
</style>