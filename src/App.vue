<template>
  <div id="app">
    <!-- Header, Navbar, o altri componenti comuni possono andare qui -->
    <header>
      <nav class="navbar">
        <div class="container">
          <h1 class="brand"><router-link class="brand-link" to="/">BDoctors</router-link></h1>
          <div class="navbar-links">
            <ul class="navbar-nav">
              <li class="nav-item">
                <router-link class="nav-link" to="/">Dottori</router-link>
              </li>
              <!-- Aggiungi altre voci se necessario -->
            </ul>
          </div>
        </div>
      </nav>
    </header>

    <!-- Questo è il punto in cui verranno visualizzati i componenti delle rotte -->
    <router-view></router-view>

    <!-- Footer o altri componenti comuni possono andare qui -->
  </div>
</template>

<style scoped>
/* Stili personalizzati per la navbar */

/* Impostazione globale per il layout della navbar */
.navbar {
  background-color: #87CEEB; /* Air Blue */
  padding: 0.5rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

/* Stile del brand */
.brand {
  margin: 0;
}

.brand-link {
  font-size: 2.5rem; /* Aumenta la dimensione del testo per BDoctors */
  font-weight: bold;
  text-decoration: none;
  color: #333333; /* Charcoal */
}

/* Menu a destra */
.navbar-nav {
  list-style: none;
  display: flex;
  gap: 1.5rem;
  margin: 0;
  padding: 0;
}

.nav-item {
  list-style: none;
}

.nav-link {
  font-size: 1.2rem;
  text-decoration: none;
  color: #333333; /* Charcoal */
  transition: color 0.3s;
}

/* Hover effect per i link */
.nav-link:hover {
  color: #000; /* Nero intenso */
}

</style>
