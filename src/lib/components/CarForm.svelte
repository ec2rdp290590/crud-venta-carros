<script>
  import { createEventDispatcher, onMount } from 'svelte';
  import { fade, fly, scale } from 'svelte/transition';
  
  // Crear un despachador para enviar eventos al componente padre
  const dispatch = createEventDispatcher();
  
  // Props: datos del carro pasados desde el padre (null para un carro nuevo)
  export let car = null;
  
  // Inicializar datos del formulario
  let formData = {
    id: null,
    make: '',
    model: '',
    year: new Date().getFullYear(),
    price: 0,
    color: 'Rojo'
  };
  
  // Colores disponibles para selección con códigos hexadecimales
  const colors = [
    { name: 'Negro', hex: '#000000' },
    { name: 'Blanco', hex: '#FFFFFF' },
    { name: 'Plateado', hex: '#C0C0C0' },
    { name: 'Gris', hex: '#808080' },
    { name: 'Rojo', hex: '#FF0000' },
    { name: 'Azul', hex: '#0000FF' },
    { name: 'Verde', hex: '#008000' },
    { name: 'Amarillo', hex: '#FFFF00' },
    { name: 'Naranja', hex: '#FFA500' }
  ];
  
  // Cuando la prop car cambia, actualizar los datos del formulario
  $: if (car) {
    formData = { ...car };
  }
  
  // Función para manejar el envío del formulario
  function handleSubmit() {
    // Validar datos del formulario
    if (!formData.make || !formData.model || !formData.year || formData.price <= 0) {
      alert('Por favor, completa todos los campos requeridos');
      return;
    }
    
    // Despachar evento de guardar con los datos del formulario
    dispatch('save', formData);
  }
  
  // Función para cancelar la edición
  function handleCancel() {
    dispatch('cancel');
  }
  
  // Obtener el código hexadecimal del color seleccionado
  $: selectedColorHex = colors.find(c => c.name === formData.color)?.hex || '#FF0000';
  
  // Marcas populares para autocompletar
  const popularMakes = ['Toyota', 'Honda', 'Ford', 'Chevrolet', 'Nissan', 'Volkswagen', 'BMW', 'Mercedes-Benz', 'Audi'];
  
  // Efecto de animación al montar el componente
  let mounted = false;
  onMount(() => {
    mounted = true;
  });
  
  // Función para generar un gradiente basado en el color seleccionado
  $: colorGradient = `linear-gradient(135deg, ${selectedColorHex} 0%, ${adjustColorBrightness(selectedColorHex, -30)} 100%)`;
  
  // Función para ajustar el brillo de un color
  function adjustColorBrightness(hex, percent) {
    // Convertir hex a RGB
    let r = parseInt(hex.substring(1, 3), 16);
    let g = parseInt(hex.substring(3, 5), 16);
    let b = parseInt(hex.substring(5, 7), 16);
    
    // Ajustar brillo
    r = Math.max(0, Math.min(255, r + percent));
    g = Math.max(0, Math.min(255, g + percent));
    b = Math.max(0, Math.min(255, b + percent));
    
    // Convertir de nuevo a hex
    return `#${r.toString(16).padStart(2, '0')}${g.toString(16).padStart(2, '0')}${b.toString(16).padStart(2, '0')}`;
  }
</script>

<div class="form-appear">
  <form on:submit|preventDefault={handleSubmit} class="space-y-6 bg-white p-6 rounded-xl shadow-lg glow-border">
    <div class="text-center mb-6">
      <div class="inline-block p-3 rounded-full bg-blue-100 mb-2 pulse-effect">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
        </svg>
      </div>
      <h3 class="text-2xl font-bold text-gray-800 gradient-text">{car ? 'Actualizar Información' : 'Registrar Nuevo Vehículo'}</h3>
      <p class="text-gray-500">Completa todos los detalles del vehículo</p>
    </div>
    
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- Campo de entrada para la marca -->
      <div class="space-y-2" in:fly={{ y: 20, duration: 300, delay: 100 }}>
        <label for="make" class="block text-sm font-medium text-gray-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-1 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
          </svg>
          Marca
        </label>
        <div class="relative">
          <input 
            type="text" 
            id="make" 
            bind:value={formData.make} 
            class="form-input pl-10 dynamic-shadow"
            placeholder="ej. Toyota"
            list="makes-list"
            required
          />
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
        </div>
        <datalist id="makes-list">
          {#each popularMakes as make}
            <option value={make} />
          {/each}
        </datalist>
      </div>
      
      <!-- Campo de entrada para el modelo -->
      <div class="space-y-2" in:fly={{ y: 20, duration: 300, delay: 200 }}>
        <label for="model" class="block text-sm font-medium text-gray-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-1 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
          </svg>
          Modelo
        </label>
        <div class="relative">
          <input 
            type="text" 
            id="model" 
            bind:value={formData.model} 
            class="form-input pl-10 dynamic-shadow"
            placeholder="ej. Corolla"
            required
          />
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 20l4-16m2 16l4-16M6 9h14M4 15h14" />
            </svg>
          </div>
        </div>
      </div>
      
      <!-- Campo de entrada para el año -->
      <div class="space-y-2" in:fly={{ y: 20, duration: 300, delay: 300 }}>
        <label for="year" class="block text-sm font-medium text-gray-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-1 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
          </svg>
          Año
        </label>
        <div class="relative">
          <input 
            type="number" 
            id="year" 
            bind:value={formData.year} 
            min="1900" 
            max={new Date().getFullYear() + 1}
            class="form-input pl-10 dynamic-shadow"
            required
          />
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
            </svg>
          </div>
        </div>
      </div>
      
      <!-- Campo de entrada para el precio -->
      <div class="space-y-2" in:fly={{ y: 20, duration: 300, delay: 400 }}>
        <label for="price" class="block text-sm font-medium text-gray-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-1 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          Precio ($)
        </label>
        <div class="relative">
          <input 
            type="number" 
            id="price" 
            bind:value={formData.price} 
            min="0" 
            step="100"
            class="form-input pl-10 dynamic-shadow"
            required
          />
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Selección de color con vista previa -->
    <div class="space-y-2" in:fly={{ y: 20, duration: 300, delay: 500 }}>
      <label for="color" class="block text-sm font-medium text-gray-700">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-1 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm  stroke-width="2" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm0 0h12a2 2 0 002-2v-4a2 2 0 00-2-2h-2.343M11 7.343l1.657-1.657a2 2 0 012.828 0l2.829 2.829a2 2 0 010 2.828l-8.486 8.485M7 17h.01" />
        </svg>
        Color
      </label>
      
      <div class="bg-gray-50 p-4 rounded-lg glass-card">
        <div class="flex items-center mb-4">
          <div 
            class="color-preview shine-effect" 
            style="background-color: {selectedColorHex}; box-shadow: 0 0 15px {selectedColorHex}80;"
            transition:scale={{duration: 200}}
          ></div>
          <span class="font-medium">{formData.color}</span>
        </div>
        
        <div class="grid grid-cols-3 gap-2">
          {#each colors as color}
            <button 
              type="button"
              class="flex items-center p-2 rounded-md transition-all duration-200 hover:bg-gray-100 {formData.color === color.name ? 'bg-blue-50 ring-2 ring-blue-500' : ''}"
              on:click={() => formData.color = color.name}
            >
              <div 
                class="color-preview" 
                style="background-color: {color.hex}; {formData.color === color.name ? `box-shadow: 0 0 10px ${color.hex}80;` : ''}"
              ></div>
              <span class="text-sm">{color.name}</span>
            </button>
          {/each}
        </div>
      </div>
    </div>
    
    <!-- Vista previa del carro -->
    <div 
      class="p-6 rounded-lg text-center float-effect" 
      in:fly={{ y: 20, duration: 300, delay: 600 }}
      style="background: {colorGradient}; color: {selectedColorHex === '#FFFFFF' || selectedColorHex === '#FFFF00' ? '#000000' : '#FFFFFF'};"
    >
      <div class="inline-block p-4 rounded-full mb-2 bg-white bg-opacity-20">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12" viewBox="0 0 20 20" fill="currentColor">
          <path d="M8 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0zM15 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0z" />
          <path d="M3 4a1 1 0 00-1 1v10a1 1 0 001 1h1.05a2.5 2.5 0 014.9 0H10a1 1 0 001-1v-1h3a1 1 0 00.8-.4l3-4a1 1 0 00.2-.6V8a1 1 0 00-1-1h-3.05A2.5 2.5 0 0011 5H8.05A2.5 2.5 0 005 7H3a1 1 0 00-1 1v1a1 1 0 001 1h1.05a2.5 2.5 0 014.9 0H10a1 1 0 001-1v-1h3a1 1 0 00.8-.4l3-4a1 1 0 00.2-.6V5a1 1 0 00-1-1h-3.05A2.5 2.5 0 0011 2H5a1 1 0 00-1 1v1z" />
        </svg>
      </div>
      <h4 class="font-bold text-lg">{formData.make || 'Marca'} {formData.model || 'Modelo'}</h4>
      <p class="text-sm">{formData.year || 'Año'} • ${formData.price?.toLocaleString() || '0'}</p>
    </div>
    
    <!-- Botones del formulario -->
    <div class="flex justify-end space-x-3 pt-4" in:fly={{ y: 20, duration: 300, delay: 700 }}>
      <button 
        type="button" 
        on:click={handleCancel}
        class="btn btn-secondary"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-1 inline" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
        Cancelar
      </button>
      <button 
        type="submit" 
        class="btn btn-success shine-effect"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-1 inline" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
        </svg>
        {car ? 'Actualizar Carro' : 'Añadir Carro'}
      </button>
    </div>
  </form>
</div>
