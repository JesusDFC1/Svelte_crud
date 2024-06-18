<script>
  import { onMount } from 'svelte';
  let baul = [];
  let datosbaul = {
    id_baul: null,
    Plataforma: "",
    Usuario: "",
    Clave: "",
  };
  const mostrarempleados = async () => {
    try {
      const respuesta = await fetch("http://127.0.0.1:5000/");
      const datosRespuesta = await respuesta.json();
      if (datosRespuesta.baul && Array.isArray(datosRespuesta.baul)){
      baul = datosRespuesta.baul;
    }else{
      console.error('Error: datosRespuesta no es un array', datosRespuesta)
    }
      datosbaul = {
        id_baul: null,
        Plataforma: "",
        Usuario: "",
        Clave: "",
      };
      console.log(baul);
    } catch (error) {
      console.error('Error al obtener empleados:', error);
    }
  };

  const Agregarempleado = async () => {
    const nuevoempleado = {
      id: datosbaul.id,
      plataforma: datosbaul.Plataforma,
      usuario: datosbaul.Usuario,
      clave: datosbaul.Clave,
    };
    console.log(nuevoempleado);

    try {
      const respuesta = await fetch("http://127.0.0.1:5000/registro/", {
        method: "POST",
        body: JSON.stringify(nuevoempleado),
        headers: {
          "Content-Type": "application/json",
        },
      });
      const datosRespuesta = await respuesta.json();
      console.log(datosRespuesta);
      mostrarempleados();
    } catch (error) {
      console.error('Error al agregar empleado:', error);
    }
  };
///////////
const borrarusuario = (id) => {
        const url = "http://127.0.0.1:5000/eliminar/"+id;
        fetch(url, { method: 'DELETE' })
            .then(response => response.json())
            .then(res => {
                if (res.mensaje === "Éxito") {
                    mostrarempleados();
                } else {
                    console.error('Error al eliminar:', res.mensaje);
                }
            })
            .catch(error => console.error('Error al eliminar:', error));
    };
////////
const editarEmpleado = (empleado) => {
  datosbaul = {
    id_baul: empleado.id_baul,
    Plataforma: empleado.Plataforma,
    Usuario: empleado.usuario,
    Clave: empleado.clave
  };
};
//////////

const Actualizar = async () => {
  try {
    const url = `http://127.0.0.1:5000/actualizar/${datosbaul.id_baul}`;
    const datosActualizar = {
      id: datosbaul.id_baul,
      plataforma: datosbaul.Plataforma,
      usuario: datosbaul.Usuario,
      clave: datosbaul.Clave,
    };
    const respuesta = await fetch(url, {
      method: "PUT",
      body: JSON.stringify(datosActualizar),
      headers: {
        "Content-Type": "application/json",
      },
    });
    const datosRespuesta = await respuesta.json();
    console.log(datosRespuesta);
    mostrarempleados();
  } catch (error) {
    console.error('Error al actualizar:', error);
  }
};
  onMount(() => {
    mostrarempleados();
  });



</script>

<main>
  <div class="container">
    <div class="row">
      <div class="col-md-6">
        <div class="card">
          <div class="card-header">Formulario De Usuarios</div>
          <div class="card-body">
            <form>
              <div class="mb-3">
                <label for="plataforma" class="form-label">Plataforma</label>
                <input
                  bind:value={datosbaul.Plataforma}
                  type="text"
                  class="form-control"
                  id="plataforma"
                  placeholder="Plataforma"
                />
              </div>
              <div class="mb-3">
                <label for="usuario" class="form-label">Usuario</label>
                <input
                  bind:value={datosbaul.Usuario}
                  type="text"
                  class="form-control"
                  id="usuario"
                  placeholder="Usuario"
                />
              </div>
              <div class="mb-3">
                <label for="clave" class="form-label">Clave</label>
                <input
                  bind:value={datosbaul.Clave}
                  type="text"
                  class="form-control"
                  id="clave"
                  placeholder="Clave"
                />
              </div>
              <div class="btn-group">
                <button type="button" class="btn btn-primary" on:click|preventDefault={Agregarempleado}>Agregar Empleado</button>
                <button type="button" class="btn btn-success" on:click|preventDefault={Actualizar}>Actualizar datos</button>
              </div>
            </form>
          </div>
        </div>
      </div>

      <div class="col-md-6">
        <div class="card">
          <div class="card-header">Usuarios</div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-primary">
                <thead>
                  <tr>
                    <th>ID</th>
                    <th>Plataforma</th>
                    <th>Usuario</th>
                    <th>Clave</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  {#each baul as empleado}
                  <tr>
                    <td>{empleado.id_baul}</td>
                    <td>{empleado.Plataforma}</td>
                    <td>{empleado.usuario}</td>
                    <td>{empleado.clave}</td>
                    <td>
                      <div class="btn-group">
                        <button type="button" class="btn btn-primary" on:click|preventDefault={() => editarEmpleado(empleado)}>Modificar</button>
                        <button type="button" class="btn btn-danger" on:click|preventDefault={() => borrarusuario(empleado.id_baul)}>Eliminar</button>
                      </div>
                    </td>
                  </tr>
                  {/each}
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</main>


<style>

.container {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
  padding: 20px;
}

.row {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.col-md-6 {
  flex: 0 0 50%;
  box-sizing: border-box;
}

.card {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.card-header {
  font-size: 1.25em;
  font-weight: bold;
  margin-bottom: 20px;
}

.card-body {
  flex: 1;
}

.form-label {
  font-weight: bold;
}

.form-control {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

.btn-group {
  display: flex;
  gap: 10px;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.btn-primary {
  background-color: #007bff;
  color: #fff;
}

.btn-success {
  background-color: #28a745;
  color: #fff;
}

.btn-danger {
  background-color: #dc3545;
  color: #fff;
}

.table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  border-bottom: 1px solid #ddd;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

.table-responsive {
  overflow-x: auto;
}
</style>