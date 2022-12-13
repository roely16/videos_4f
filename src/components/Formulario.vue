<template>
    <div>
        <v-container>
            <v-form ref="form" v-model="valid">
                <v-row>
                    <v-col cols="12" sm="12">
                        <v-autocomplete label="Tipo de Inmueble" outlined prepend-icon="mdi-office-building" :items="tipos_inmuebles" hide-details v-model="reporte.tipo_inmueble" :rules="[v => !!v || 'Item is required']" required></v-autocomplete>
                    </v-col>
                    <v-col cols="12" sm="12">
                        <v-text-field label="Lugar" outlined prepend-icon="mdi-map" hide-details v-model="reporte.lugar" :rules="[v => !!v || 'Item is required']" required></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12">
                        <v-autocomplete label="Zona" outlined prepend-icon="mdi-map-marker" :items="zonas" hide-details v-model="reporte.zona" :rules="[v => !!v || 'Item is required']" required></v-autocomplete>
                    </v-col>
                    <v-col cols="12" sm="12">
                        <v-textarea label="Descripción" outlined prepend-icon="mdi-comment" v-model="reporte.descripcion" :rules="[v => !!v || 'Campo obligatorio', v => (v && v.length <= 50) || 'La descripción debe contener máximo 50 caracteres']" counter="50" required></v-textarea>
                    </v-col>
                    <v-col cols="12" sm="12">
                        <v-file-input chips show-size accept="video/*" prepend-icon="mdi-video" label="Videos" description="Test" outlined v-model="reporte.video" :rules="[v => !!v || 'Item is required']" required :clearable="false" hide-details></v-file-input>
                    </v-col>
                </v-row>
            </v-form>

            <v-row justify="center" align="center" class="text-center">
                <v-col cols="12" sm="12">
                    <v-btn large color="primary" :disabled="uploading" :loading="uploading" @click="subir_videos">Enviar 
                        <v-icon>mdi-upload</v-icon>
                    </v-btn>
                    <v-btn large class="ml-4" color="error" :disabled="uploading" @click="cancelar">Cancelar 
                        <v-icon>mdi-close-circle</v-icon>
                    </v-btn>
                </v-col>
               
            </v-row>
        </v-container>
    </div>    
</template>

<script>


    export default {
        data(){
            return{
                tipos_inmuebles: [],
                reporte: {
                    lugar: '',
                    tipo_inmueble: '',
                    direccion: '',
                    zona: '',
                    videos: null,
                    descripcion: ''
                },
                uploading: false,
                valid: true,
                latitude: '',
                longitude: '',
                zonas: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,21,24,25],
            }
        },
        methods: {
            subir_videos(){

                // eslint-disable-next-line no-console
                console.log(this.reporte)

                if (this.$refs.form.validate()) {

                    // eslint-disable-next-line no-console
                    console.log(this.reporte.video)

                    if (this.reporte.video) {
                        
                        this.uploading = true

                        let formData = new FormData();
                        formData.append('lugar', this.reporte.lugar)
                        formData.append('tipo_inmueble', this.reporte.tipo_inmueble)
                        formData.append('direccion', this.reporte.direccion)
                        formData.append('zona', this.reporte.zona)
                        formData.append('latitude', this.latitude)
                        formData.append('longitude', this.longitude)
                        formData.append('descripcion', this.reporte.descripcion)
                        // formData.append('archivos', this.reporte.videos.length)
                        formData.append('file', this.reporte.video)

                        // Agregar cada video
                        // let i = 1
                        // this.reporte.videos.forEach(video => {
                            
                        //     formData.append('file' + i, video)
                        //     i++

                        // });

                        this.axios.post(process.env.VUE_APP_SUBIR_URL, formData, {
                            headers: {
                                'Content-Type': 'multipart/form-data'
                            }
                        }).then((response) => {
                            // eslint-disable-next-line no-console
                            console.log(response.data)
                            this.uploading = false

                            // eslint-disable-next-line no-undef
                            Swal.fire(
                                'Excelente!',
                                'Los videos han sido subidos exitosamente!',
                                'success'
                            ).then(() => {

                                this.cancelar()

                            })
                        })

                    }else{

                         // eslint-disable-next-line no-undef
                         Swal.fire({
                            icon: 'error',
                            title: 'Oops...',
                            text: 'Debe seleccionar un video para enviar!'
                        })

                    }

                }else{

                    // eslint-disable-next-line no-undef
                    Swal.fire({
                        icon: 'error',
                        title: 'Oops...',
                        text: 'Existen campos que debe de completar!'
                    })

                }

            },
            obtener_inmuebles(){

                let data = {
                    "name": "obtener_inmuebles",
                    "param": {}
                }

                this.axios.post(process.env.VUE_APP_API_URL, data).then((response) => {
                    this.tipos_inmuebles = response.data.response.result
                })

            },
            cancelar(){

                this.valid = true

                this.reporte = {
                    lugar: '',
                    tipo_inmueble: '',
                    direccion: '',
                    zona: null,
                    video: null
                }

                this.$refs.form.resetValidation()

            },
            showPosition(position){
                // eslint-disable-next-line no-console
                this.latitude = position.coords.latitude
                this.longitude = position.coords.longitude
            }
        },
        mounted(){

            navigator.geolocation.getCurrentPosition(this.showPosition);

            this.obtener_inmuebles()

        }
    }
</script>