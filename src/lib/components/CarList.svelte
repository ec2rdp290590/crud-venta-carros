<script>
  import { onMount } from 'svelte';
  import { fade, slide, fly } from 'svelte/transition';
  import CarForm from './CarForm.svelte';

  // Estado para almacenar nuestros datos de carros
  let cars = [];
  let editingCar = null;
  let showForm = false;
  let searchTerm = '';
  let sortField = 'make';
  let sortDirection = 'asc';

  // Inicializar con algunos datos de ejemplo
  onMount(() => {
    // En una aplicación real, estos datos vendrían de una API o base de datos
    cars = [
      { id: 1, make: 'Toyota', model: 'Corolla', year: 2020, price: 18000, color: 'Plateado' },
      { id: 2, make: 'Honda', model: 'Civic', year: 2021, price: 22000, color: 'Azul' },
      { id: 3, make: 'Ford', model: 'Mustang', year: 2019, price: 35000, color: 'Rojo' },
      { id: 4, make: 'Chevrolet', model: 'Camaro', year: 2022, price: 42000, color: 'Negro' },
      { id: 5, make: 'Nissan', model: 'Sentra', year: 2021, price: 19500, color: 'Blanco' }
    ];
  });

  // Función para manejar la adición de un nuevo carro
  function addCar() {
    showForm = true;
    editingCar = null; // Reiniciar el carro en edición para crear uno nuevo
  }

  // Función para manejar la edición de un carro existente
  function editCar(car) {
    editingCar = { ...car }; // Crear una copia para evitar la mutación directa
    showForm = true;
  }

  // Función para manejar la eliminación de un carro
  function deleteCar(id) {
    if (confirm('¿Estás seguro de que deseas eliminar este vehículo?')) {
      // Filtrar el carro con el id correspondiente
      cars = cars.filter(car => car.id !== id);
    }
  }

  // Función para guardar un carro (nuevo o editado)
  function saveCar(event) {
    const carData = event.detail;
    
    if (carData.id) {
      // Actualizar carro existente
      cars = cars.map(car => 
        car.id === carData.id ? carData : car
      );
    } else {
      // Añadir nuevo carro con un ID generado
      const newId = Math.max(0, ...cars.map(car => car.id)) + 1;
      cars = [...cars, { ...carData, id: newId }];
    }
    
    showForm = false;
  }

  // Función para cancelar la edición del formulario
  function cancelEdit() {
    showForm = false;
  }
  
  // Función para ordenar los carros
  function sortCars(field) {
    if (sortField === field) {
      // Cambiar dirección si ya estamos ordenando por este campo
      sortDirection = sortDirection === 'asc' ? 'desc' : 'asc';
    } else {
      // Nuevo campo, ordenar ascendente por defecto
      sortField = field;
      sortDirection = 'asc';
    }
  }
  
  // Filtrar y ordenar carros
  $: filteredCars = cars
    .filter(car => {
      if (!searchTerm) return true;
      const term = searchTerm.toLowerCase();
      return car.make.toLowerCase().includes(term) || 
             car.model.toLowerCase().includes(term) ||
             car.color.toLowerCase().includes(term) ||
             car.year.toString().includes(term);
    })
    .sort((a, b) => {
      let comparison = 0;
      
      if (sortField === 'price' || sortField === 'year') {
        comparison = a[sortField] - b[sortField];
      } else {
        comparison = a[sortField].localeCompare(b[sortField]);
      }
      
      return sortDirection === 'asc' ? comparison : -comparison;
    });
    
  // Obtener el color hexadecimal para un nombre de color
  function getColorHex(colorName) {
    const colorMap = {
      'Negro': '#000000',
      'Blanco': '#FFFFFF',
      'Plateado': '#C0C0C0',
      'Gris': '#808080',
      'Rojo': '#FF0000',
      'Azul': '#0000FF',
      'Verde': '#008000',
      'Amarillo': '#FFFF00',
      'Naranja': '#FFA500'
    };
    
    return colorMap[colorName] || '#000000';
  }
</script>

<div class="car-bg min-h-screen py-8 px-4">
  <div class="main-container p-6">
    <header class="mb-8 text-center">
      <h1 class="text-4xl font-bold mb-2 gradient-text">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 inline-block mr-2 text-blue-600" viewBox="0 0 20 20" fill="currentColor">
          <path d="M8 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0zM15 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0z" />
          <path d="M3 4a1 1 0 00-1 1v10a1 1 0 001 1h1.05a2.5 2.5 0 014.9 0H10a1 1 0 001-1v-1h3a1 1 0 00.8-.4l3-4a1 1 0 00.2-.6V8a1 1 0 00-1-1h-3.05A2.5 2.5 0 0011 5H8.05A2.5 2.5 0 005 7H3a1 1 0 00-1 1v1a1 1 0 001 1h1.05a2.5 2.5 0 014.9 0H10a1 1 0 001-1v-1h3a1 1 0 00.8-.4l3-4a1 1 0 00.2-.6V5a1 1 0 00-1-1h-3.05A2.5 2.5 0 0011 2H5a1 1 0 00-1 1v1z" />
        </svg>
        Gestión de Inventario de Carros
      </h1>
      <p class="text-gray-600 text-lg">Sistema de administración de vehículos para su concesionario</p>
    </header>
    
    <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-6 gap-4">
      <!-- Botón para añadir un nuevo carro -->
      <button 
        class="btn btn-primary flex items-center justify-center pulse-effect"
        on:click={addCar}
        in:fly={{ y: 20, duration: 300 }}
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
        </svg>
        Añadir Nuevo Vehículo
      </button>
      
      <!-- Buscador -->
      <div class="relative" in:fly={{ y: 20, duration: 300, delay: 100 }}>
        <input 
          type="text" 
          bind:value={searchTerm} 
          placeholder="Buscar vehículos..." 
          class="form-input pl-10 w-full md:w-64 dynamic-shadow"
        />
        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </div>
      </div>
    </div>
    
    {#if showForm}
      <!-- Componente de formulario para añadir/editar carros -->
      <div class="mb-8" in:slide={{ duration: 300 }} out:slide={{ duration: 300 }}>
        <CarForm 
          car={editingCar} 
          on:save={saveCar} 
          on:cancel={cancelEdit} 
        />
      </div>
    {/if}
    
    <!-- Tabla de listado de carros -->
    <div class="overflow-x-auto" in:fade={{ duration: 300 }}>
      {#if filteredCars.length > 0}
        <table class="car-table">
          <thead>
            <tr>
              <th class="cursor-pointer shine-effect" on:click={() => sortCars('make')}>
                Marca
                {#if sortField === 'make'}
                  <span class="ml-1">{sortDirection === 'asc' ? '↑' : '↓'}</span>
                {/if}
              </th>
              <th class="cursor-pointer shine-effect" on:click={() => sortCars('model')}>
                Modelo
                {#if sortField === 'model'}
                  <span class="ml-1">{sortDirection === 'asc' ? '↑' : '↓'}</span>
                {/if}
              </th>
              <th class="cursor-pointer shine-effect" on:click={() => sortCars('year')}>
                Año
                {#if sortField === 'year'}
                  <span class="ml-1">{sortDirection === 'asc' ? '↑' : '↓'}</span>
                {/if}
              </th>
              <th class="cursor-pointer shine-effect" on:click={() => sortCars('price')}>
                Precio
                {#if sortField === 'price'}
                  <span class="ml-1">{sortDirection === 'asc' ? '↑' : '↓'}</span>
                {/if}
              </th>
              <th class="cursor-pointer shine-effect" on:click={() => sortCars('color')}>
                Color
                {#if sortField === 'color'}
                  <span class="ml-1">{sortDirection === 'asc' ? '↑' : '↓'}</span>
                {/if}
              </th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            {#each filteredCars as car, i (car.id)}
              <tr class="car-card" style="animation-delay: {i * 50}ms">
                <td class="font-medium">{car.make}</td>
                <td>{car.model}</td>
                <td>{car.year}</td>
                <td class="font-medium">${car.price.toLocaleString()}</td>
                <td>
                  <div class="flex items-center">
                    <div 
                      class="color-preview"
                      style="background-color: {getColorHex(car.color)}; box-shadow: 0 0 5px {getColorHex(car.color)}80;"
                    ></div>
                    {car.color}
                  </div>
                </td>
                <td>
                  <div class="flex space-x-2">
                    <!-- Botón de editar -->
                    <button 
                      class="btn btn-primary py-1 px-3 text-sm"
                      on:click={() => editCar(car)}
                    >
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                      </svg>
                    </button>
                    <!-- Botón de eliminar -->
                    <button 
                      class="btn btn-danger py-1 px-3 text-sm"
                      on:click={() => deleteCar(car.id)}
                    >
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                      </svg>
                    </button>
                  </div>
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      {:else}
        <!-- Mostrar cuando no hay carros disponibles o no hay resultados de búsqueda -->
        <div class="text-center py-10 bg-white rounded-lg shadow glass-card">
          {#if searchTerm}
            <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mx-auto text-gray-400 mb-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
            </svg>
            <h3 class="text-lg font-medium text-gray-900">No se encontraron resultados</h3>
            <p class="mt-2 text-gray-500">No hay vehículos que coincidan con "{searchTerm}"</p>
            <button class="mt-4 btn btn-secondary" on:click={() => searchTerm = ''}>Limpiar búsqueda</button>
          {:else}
            <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mx-auto text-gray-400 mb-4 float-effect" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4" />
            </svg>
            <h3 class="text-lg font-medium text-gray-900">No hay vehículos disponibles</h3>
            <p class="mt-2 text-gray-500">Añade un nuevo vehículo para comenzar</p>
            <button class="mt-4 btn btn-primary pulse-effect" on:click={addCar}>Añadir Vehículo</button>
          {/if}
        </div>
      {/if}
    </div>
    
    <!-- Estadísticas -->
    {#if cars.length > 0}
      <div class="mt-8 grid grid-cols-1 md:grid-cols-3 gap-4" in:fly={{ y: 20, duration: 300 }}>
        <div class="bg-white p-4 rounded-lg shadow-md glass-card">
          <h3 class="text-lg font-medium text-gray-900">Total de Vehículos</h3>
          <p class="text-3xl font-bold text-blue-600 gradient-text">{cars.length}</p>
        </div>
        <div class="bg-white p-4 rounded-lg shadow-md glass-card">
          <h3 class="text-lg font-medium text-gray-900">Valor del Inventario</h3>
          <p class="text-3xl font-bold text-green-600 gradient-text">
            ${cars.reduce((sum, car) => sum + car.price, 0).toLocaleString()}
          </p>
        </div>
        <div class="bg-white p-4 rounded-lg shadow-md glass-card">
          <h3 class="text-lg font-medium text-gray-900">Precio Promedio</h3>
          <p class="text-3xl font-bold text-purple-600 gradient-text">
            ${Math.round(cars.reduce((sum, car) => sum + car.price, 0) / cars.length).toLocaleString()}
          </p>
        </div>
      </div>
    {/if}
    
    <footer class="mt-12 text-center text-gray-500 text-sm">
      <p>© {new Date().getFullYear()} Concesionario de Vehículos. Todos los derechos reservados.</p>
    </footer>
  </div>
</div>
