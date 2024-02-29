<script>
    import { onMount } from 'svelte';
    import { Link, navigate, useLocation } from 'svelte-routing';
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import HeroeModel from '../models/Heroe.js';
    import { FontAwesomeIcon } from '@fortawesome/svelte-fontawesome';
  
    export let id;
    let location = useLocation();
    let heroeId = id || location.params.id;
    let api = 'https://crud-frameworks-default-rtdb.europe-west1.firebasedatabase.app';
    let heroe = new HeroeModel('', '', '', '');
  
    onMount(() => {
      if (heroeId !== 'nuevo') {
        getHeroeById(heroeId);
      }
    });
  
    function getHeroeById(heroeId) {
      axios.get(`${api}/heroes/${heroeId}.json`)
        .then(res => {
          if (res.data) {
            heroe = res.data;
            heroe.id = heroeId;
          }
        });
    }
  
    function guardar() {
      if (heroeId !== 'nuevo') {
        heroe.id = heroeId;
        axios.put(`${api}/heroes/${heroeId}.json`, heroe)
          .then(res => {
            if (res.data) {
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'El Heroe ha sido modificado correctamente.',
                showConfirmButton: false,
                timer: 1500
              });
              navigate('/heroes');
            }
          })
          .catch(err => {
            console.log(err);
          });
      } else {
        axios.post(`${api}/heroes.json`, heroe)
          .then(res => {
            if (res.data) {
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'El Heroe ha sido creado correctamente.',
                showConfirmButton: false,
                timer: 1500
              });
              navigate('/heroes');
            }
          })
          .catch(err => {
            console.log(err);
          });
      }
    }
  </script>
  
  <div>
    <h1>{heroe.nombre ? `Heroe: ${heroe.nombre}` : 'Heroe: Nombre del heroe'}</h1>
    <hr />
  
    <div class="row text-right">
      <div class="col">
        <Link to="/heroes" class="btn btn-outline-primary" title="Volver">
          <FontAwesomeIcon icon="arrow-left" /> Volver
        </Link>
      </div>
    </div>
  
    <div class="row">
      <div class="col">
        <form on:submit|preventDefault={guardar}>
          <div class="form-group">
            <label for="firebaseId">Id Firebase</label>
            <input type="text" class="form-control" placeholder="Id Firebase" bind:value={heroe.id} disabled id="firebaseId">
            <small class="form-text text-muted">Este campo se genera automáticamente.</small>
          </div>
          <div class="form-group">
            <label for="nombreInput">Nombre</label>
            <input type="text" class="form-control" placeholder="Nombre" bind:value={heroe.nombre} required id="nombreInput">
          </div>
          <div class="form-group">
            <label for="poderInput">Poder</label>
            <input type="text" class="form-control" placeholder="Poder" bind:value={heroe.poder} id="poderInput">
          </div>
          <div class="form-group">
            <label for="estadoInput">Estado</label>
            <br />
            {#if heroe.estado}
              <button on:click={() => (heroe.estado = false)} class="btn btn-outline-success w-25" type="button" title="Vivo">
                <FontAwesomeIcon icon="thumbs-up" title="Vivo" /> Vivo
              </button>            
            {:else}
              <button on:click={() => (heroe.estado = true)} class="btn btn-outline-danger w-25" type="button" title="Muerto">
                <FontAwesomeIcon icon="thumbs-down" title="Muerto" /> Muerto
              </button>
            {/if}
          </div>
          <hr />
          <div class="form-group text-center">
            <button type="submit" class="btn btn-outline-primary w-25" title="Guardar">
              <FontAwesomeIcon icon="save" title="Guardar" /> Guardar
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
  