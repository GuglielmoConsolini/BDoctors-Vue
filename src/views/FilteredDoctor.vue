<template>
    


    <div class="container-fluid m-0 p-0 pt-2 bg-white altezza">
        <div class="position-relative container d-flex">
            <button class="btn btn-secondary">
                <router-link class="text-decoration-none text-light" to="/">
                    <i class="fa fa-arrow-left"></i>
                </router-link>
            </button>
            <h2 class="text-success container my-0">
                Risultati della Ricerca per {{ specialization }}: {{ filteredDoctors.length }}
            </h2>
        </div>

        <div v-if="loading">Caricamento...</div>
        <div v-else-if="error">Errore: {{ error }}</div>
        <div v-else>
            
            <!-- Jumbotron per le Specializzazioni -->
        <div class="container mb-4 text-dark jumbotron p-4 shadow">
            <h3 class="mb-3">Filtra per Specializzazioni, Voti e Recensioni</h3>
            <div class="search-container">
                <!-- Lista delle Specializzazioni -->
                <div class="col-md-12 mb-3">
                    <div class="specializations-list d-flex flex-wrap">
                        <span
                            v-for="specialization in specializations"
                            :key="specialization.id"
                            @click="handleSearch(specialization)"
                            :class="['specialization-button', { 'active': selectedSpecializations.includes(specialization.name) }]"
                        >
                            {{ specialization.name }}
                        </span>
                    </div>
                </div>
            </div>
        </div>


            <!-- Visualizza i dettagli del dottore -->
            <div class="container-sm">
                <div class="row text-dark">
                    <div class="col-12 col-md-6 col-lg-4 py-2" v-for="doctor in filteredDoctors" :key="doctor.id">
                        <div class="border p-3 bg-light rounded d-flex flex-column h-100 justify-content-between">
                            <div class="d-flex justify-content-center mb-3 position-relative">
                                <img v-if="doctor.pic" :src="`${base_url}/storage/images/${doctor.pic}`"
                                    alt="Immagine del dottore" class="img-fluid" @error="handleImageError" />
                                <img v-else
                                    src="https://i.pinimg.com/736x/ac/67/4d/ac674d2be5f98abf1c189c75de834155.jpg"
                                    alt="Immagine del dottore" class="img-fluid" />
                                <p v-if="doctor.sponsorships[0]?.name == 'Gold'"
                                    class="d-flex align-items-center m-0 p-0 text-center position-absolute top-25 end-0 sponsor premium p-2 rounded-pill text-dark">
                                    Sponsorizzato
                                </p>

                                <p v-if="doctor.sponsorships[0]?.name == 'Premium'"
                                    class="d-flex align-items-center m-0 p-0 text-center position-absolute top-25 end-0 sponsor premium p-2 rounded-pill text-dark">
                                    Sponsorizzato
                                </p>

                                <p v-if="doctor.sponsorships[0]?.name == 'Basic'"
                                    class="d-flex align-items-center m-0 p-0 text-center position-absolute top-25 end-0 sponsor premium p-2 rounded-pill text-light text-dark">
                                    Sponsorizzato
                                </p>
                            </div>

                            <div class="d-flex justify-content-between">
                                <h2>{{ doctor.surname || 'Nome non disponibile' }}</h2>
                            </div>
                            <p>Indirizzo: {{ doctor.address || 'Indirizzo non disponibile' }}</p>
                            <p>Telefono: {{ doctor.phone || 'Telefono non disponibile' }}</p>
                            <p>Bio: {{ doctor.bio || 'Bio non disponibile' }}</p>

                            <!-- Recensioni -->

                            <div v-if="doctor.reviews && doctor.reviews.length > 0">
                                <!-- Ciclo per visualizzare le stelle -->
                                <span v-for="n in doctor.reviews[0].stars" :key="n">
                                    <i class="fa-solid fa-star text-warning"></i>
                                </span>
                                <!-- Mostra il numero di stelle -->
                                <!-- {{ doctor.reviews[0].stars }} -->
                            </div>
                            <div v-else>
                                Nessuna recensione disponibile
                            </div>
                    
                            <!-- Specializzazioni -->
                            <h3 v-if="doctor.specializations && doctor.specializations.length > 0">
                                Specializzazioni:
                            </h3>
                            <ul v-if="doctor.specializations && doctor.specializations.length > 0">
                                <li v-for="specialization in doctor.specializations" :key="specialization.id">
                                    {{ specialization.name }}
                                </li>
                            </ul>
                            <div v-else>
                                <h3>Specializzazioni:</h3>
                                <p>Nessuna specializzazione</p>
                            </div>
                            <button class="btn btn-info mt-2" @click="goToDoctorDetail(doctor.slug)">
                                Visualizza Dettagli
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            doctors: [],
            filteredDoctors: [],
            selectedSpecializations: [],
            loading: true,
            error: null,
            base_url: 'http://127.0.0.1:8000',
            specializations: [],
        };
    },
    computed: {
        params() {
            return this.$route.query;
        },
        specialization() {
            return this.$route.query['specializations[]'] || 'Nessuna specializzazione trovata';
        }
    },
    created() {
        this.fetchDoctors();
        this.fetchSpecializations();

        // Imposta selectedSpecializations in base ai parametri della query
    const selectedParams = this.$route.query['specializations[]'];
    if (Array.isArray(selectedParams)) {
        this.selectedSpecializations = selectedParams;
    } else if (selectedParams) {
        this.selectedSpecializations = [selectedParams];
    }
    },
    methods: {
        handleImageError(event) {
            event.target.src = 'https://i.pinimg.com/736x/ac/67/4d/ac674d2be5f98abf1c189c75de834155.jpg';
        },
        getRating(rating) {
            return Math.round(rating / 2);
        },
        fetchDoctors() {
            axios.get(`${this.base_url}/api/doctors`)
                .then(response => {
                    this.doctors = response.data;
                    this.filterDoctorsBySpecialization(); // Filtra subito dopo aver recuperato i dottori
                })
                .catch(error => {
                    console.error('Errore:', error);
                    this.error = 'Errore nel recupero dei dati.';
                })
                .finally(() => {
                    this.loading = false;
                });
        },
        fetchSpecializations() {
            axios.get(`${this.base_url}/api/specializations`)
                .then(response => {
                    this.specializations = response.data;
                })
                .catch(error => {
                    console.error('Errore nel recupero delle specializzazioni:', error);
                    this.error = 'Errore nel recupero delle specializzazioni.';
                });
        },
        
        handleSearch(specialization) {
            const index = this.selectedSpecializations.indexOf(specialization.name);
            if (index === -1) {
                this.selectedSpecializations.push(specialization.name);
            } else {
                this.selectedSpecializations.splice(index, 1);
            }

            const params = new URLSearchParams();
            this.selectedSpecializations.forEach(specialization => {
                params.append('specializations[]', specialization);
            });

            this.$router.push({
                name: 'search',
                query: {
                    ...Object.fromEntries(params)
                }
            }).catch(err => {
                console.error('Errore nel reindirizzamento:', err);
            });

            this.filterDoctorsBySpecialization();
        },

        filterDoctorsBySpecialization() {
    const selectedParams = this.params['specializations[]'] || [];

    if (selectedParams.length > 0) {
        this.filteredDoctors = this.doctors.filter(doctor =>
            doctor.specializations && doctor.specializations.some(specialization =>
                selectedParams.includes(specialization.name)
            )
        );
    } else {
        this.filteredDoctors = this.doctors;
    }
},

        
        goToDoctorDetail(slug) {
            this.$router.push({ name: 'doctorDetail', params: { slug } });
        },
    },
};
</script>


<style scoped>
.img-fluid {
    max-width: 100%;
    height: auto;
}

.jumbotron {
    background-color: #F8F8FF;
    border-radius: 0.5rem;
    padding: 2rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.btn-secondary {
    margin: 0.5rem;
}

.specializations-list {
    display: flex;
    flex-wrap: wrap;
    gap: 20px; /* Spaziatura tra i pulsanti */

}

.specialization-button {
    cursor: pointer;
    padding: 10px 15px; /* Spaziatura interna */
    border-radius: 5px; /* Angoli arrotondati */
    background-color: #98FF98; /* Colore uniforme per tutti i pulsanti */
    color: #333333; /* Colore del testo */
    transition: background-color 0.3s, transform 0.2s; /* Transizione per effetto hover */
}

.specialization-button:hover {
    transform: scale(1.05); /* Leggera ingrandimento al passaggio del mouse */
    background-color: #0056b3; /* Colore scuro al passaggio del mouse */
    color: #F8F8FF;
}

.active{
    transform: scale(1.05); /* Leggera ingrandimento al passaggio del mouse */
    background-color: #0056b3; /* Colore scuro al passaggio del mouse */
    color: #F8F8FF;
}

.stars {
    display: flex;
    gap: 0.5rem;
}

.star-circle {
    width: 1rem;
    height: 1rem;
    border-radius: 50%;
    background-color: #ddd;
    display: flex;
    align-items: center;
    justify-content: center;
}

.star-circle.filled {
    background-color: #f39c12;
    /* Colore giallo per i cerchi riempiti */
}

.star-circle.empty {
    background-color: #ddd;
    /* Colore grigio per i cerchi vuoti */
}

.star-circle.grey {
    background-color: #ccc;
    /* Colore grigio per nessuna recensione */
}

.image-container {
    position: relative;
    display: inline-block; /* Questo assicura che il contenitore si adatti all'immagine */
}

.sponsorship-label {
    position: absolute;
    top: 10px; /* Regola in base a quanto vuoi spostare il testo dall'alto */
    right: 10px; /* Regola in base a quanto vuoi spostare il testo da destra */
    background-color: rgba(255, 255, 255, 0.7); /* Sfondo semi-trasparente per la leggibilità */
    padding: 5px;
    border-radius: 5px; /* Angoli arrotondati */
}




</style>
