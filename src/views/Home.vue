<template>
    <div class="home">
		<Formulario />
    </div>
</template>

<script>
  // @ is an alias to /src

	import Formulario from '@/components/Formulario'

	export default {
		name: 'home',
		components: {
			Formulario
		}
	}
</script>
