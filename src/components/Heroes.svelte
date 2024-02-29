<script>
    import { onMount } from 'svelte';
    import { Link } from 'svelte-routing';
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import HeroeModel from '../models/Heroe.js';
    import { FontAwesomeIcon } from '@fortawesome/svelte-fontawesome';
  
    export let heroes = [];
  
    onMount(() => {
      getHeroes();
    });
  
    function getHeroes() {
      axios.get('https://crud-frameworks-default-rtdb.europe-west1.firebasedatabase.app/heroes.json')
        .then(res => {
          if (res.data) {
            heroes = heroesArray(res.data);
          }
        });
    }
  
    function heroesArray(heroesObj) {
      const heroesData = [];
  
      if (heroesObj === null) {
        return [];
      }
  
      Object.keys(heroesObj).forEach(key => {
        let heroe = HeroeModel;
        heroe = heroesObj[key];
        heroe.id = key;
        heroesData.push(heroe);
      });
  
      return heroesData;
    }
  
    function borrarHeroe(idHeroe) {
      Swal.fire({
        title: "¿Estás seguro?",
        text: "Una vez eliminado, no podrás recuperar este archivo.",
        icon: "warning",
        buttons: true,
        dangerMode: true,
      }).then((result) => {
          if (result.isConfirmed) {
            axios.delete(`https://crud-frameworks-default-rtdb.europe-west1.firebasedatabase.app/heroes/${idHeroe}.json`)
              .then(() => {
                getHeroes();
              });
          } else {
            Swal.fire({
              position: "top-end",
              icon: "success",
              title: "Tu archivo está seguro.",
              showConfirmButton: false,
              timer: 1500
            });
          }
        });
    }
  </script>
  
  <div>
    <h1>Listado de Heroes</h1>
    <hr>
    <div class="row">
      <div class="col text-right">
        <Link to="/heroe/nuevo" class="btn btn-outline-primary" title="Alta">
          <FontAwesomeIcon icon="plus" /> Nuevo
        </Link>
      </div>
    </div>
  
    <table aria-describedby="tabla-heroes" class="table mt-3">
      <thead class="bg-custom">
        <tr>
          <th scope="col">Nombre</th>
          <th scope="col">Poder</th>
          <th scope="col">Estado</th>
          <th scope="col" colspan="2" class="text-center">Opciones</th>
        </tr>
      </thead>
      <tbody>
        {#if heroes.length > 0}
          {#each heroes as heroe (heroe.id)}
            <tr>
              <td>{heroe.nombre}</td>
              <td>{heroe.poder}</td>
              <td>
                {#if heroe.estado}
                  <FontAwesomeIcon icon="thumbs-up" size="2x" class="text-success" title="Vivo" />
                {:else}
                  <FontAwesomeIcon icon="thumbs-down" size="2x" class="text-danger" title="Muerto" />
                {/if}
              </td>
              <td class="text-center">
                <Link to={`/heroe/${heroe.id}`} class="btn btn-outline-warning mr-1" title="Modificar">
                  <FontAwesomeIcon icon="edit" />
                  <i class="fas fa-edit"/>
                </Link>
                <button on:click={() => borrarHeroe(heroe.id)} class="btn btn-outline-danger" title="Eliminar">
                  <FontAwesomeIcon icon="trash" />
                </button>
              </td>
            </tr>
          {/each}
        {:else}
          <tr>
            <td colspan="5" class="text-center">No hay registros</td>
          </tr>
        {/if}
      </tbody>
      <tfoot>
        <tr>
          <td colspan="5" class="bg-custom"><span><em>Copyright© - Carlos Mur</em></span></td>
        </tr>
      </tfoot>
    </table>
  </div>
  