<template>
  <header>
    <nav class="navbar">
      <!-- Logo y marca -->
      <RouterLink class="link-navbar home" to="/" @click="closeMobileMenu">
        <div class="brand-container">
          <img src="/images/logo.png" alt="SOYDANI Logo" class="brand-logo" />
        </div>
      </RouterLink>

      <!-- Navegación principal -->
      <div class="nav-menu desktop-nav">
        <RouterLink to="/tecnologia" class="nav-link" :class="{ active: isCurrentRoute('/tecnologia') }" @click="closeMobileMenu">Tecnología</RouterLink>
        <RouterLink to="/navidad" class="nav-link" :class="{ active: isCurrentRoute('/navidad') }" @click="closeMobileMenu">Navidad</RouterLink>
        <RouterLink to="/hogar" class="nav-link" :class="{ active: isCurrentRoute('/hogar') }" @click="closeMobileMenu">Hogar</RouterLink>
        <RouterLink to="/ofertas" class="nav-link" :class="{ active: isCurrentRoute('/ofertas') }" @click="closeMobileMenu">Ofertas</RouterLink>
        <RouterLink to="/destacados" class="nav-link" :class="{ active: isCurrentRoute('/destacados') }" @click="closeMobileMenu">Destacados</RouterLink>
        <!-- <a href="#products" class="nav-link" @click="closeMobileMenu(); scrollToProductStore()">Compra Ahora</a>
        <a href="#contact" class="nav-link" @click="closeMobileMenu(); scrollToContact()">Contáctanos</a> -->
      </div>

      <!-- Controles de usuario -->
      <div class="nav-controls desktop-nav">
        <RouterLink v-if="!isLoggedIn" class="btn access-btn" to="/login">Acceder</RouterLink>
        <RouterLink v-if="isLoggedIn && isAdmin" class="btn admin-btn" to="/admin/products">⚙️ Panel Admin</RouterLink>
        <RouterLink v-if="isLoggedIn" @click="logout" class="btn logout-btn" to="/">Cerrar sesión</RouterLink>
        <div v-if="isLoggedIn" class="user-greeting">
          <span>{{ username }}</span>
        </div>
      </div>

      <!-- Menu hamburguesa para mobile -->
      <button class="hamburger-menu" @click="toggleMobileMenu" :class="{ 'active': isMobileMenuOpen }">
        <span></span>
        <span></span>
        <span></span>
      </button>

      <!-- Menu mobile desplegable -->
      <div class="mobile-menu" :class="{ 'active': isMobileMenuOpen }">
        <div class="mobile-menu-content">
          <div class="mobile-nav-links">
            <RouterLink to="/tecnologia" class="mobile-link" :class="{ active: isCurrentRoute('/tecnologia') }" @click="closeMobileMenu">Tecnología</RouterLink>
            <RouterLink to="/navidad" class="mobile-link" :class="{ active: isCurrentRoute('/navidad') }" @click="closeMobileMenu">Navidad</RouterLink>
            <RouterLink to="/hogar" class="mobile-link" :class="{ active: isCurrentRoute('/hogar') }" @click="closeMobileMenu">Hogar</RouterLink>
            <RouterLink to="/ofertas" class="mobile-link" :class="{ active: isCurrentRoute('/ofertas') }" @click="closeMobileMenu">Ofertas</RouterLink>
            <RouterLink to="/destacados" class="mobile-link" :class="{ active: isCurrentRoute('/destacados') }" @click="closeMobileMenu">Destacados</RouterLink>
            <!-- <a href="#products" class="mobile-link" @click="closeMobileMenu(); scrollToProductStore()">Compra Ahora</a>
            <a href="#contact" class="mobile-link" @click="closeMobileMenu(); scrollToContact()">Contáctanos</a> -->
          </div>

          <div class="mobile-controls">
            <RouterLink v-if="!isLoggedIn" class="mobile-btn access-btn" to="/login" @click="closeMobileMenu">
              Acceder
            </RouterLink>
            <div v-if="isLoggedIn" class="mobile-user-greeting">
              <span>Hola, {{ username }}</span>
            </div>
            <RouterLink v-if="isLoggedIn && isAdmin" class="mobile-btn admin-btn" to="/admin/products" @click="closeMobileMenu">
              ⚙️ Panel Admin
            </RouterLink>
            <button v-if="isLoggedIn" @click="handleMobileLogout" class="mobile-btn logout-btn">
              Cerrar sesión
            </button>
          </div>
        </div>
      </div>
    </nav>
  </header>

  <RouterView />

  <!-- Botones flotantes de redes sociales -->
  <SocialFloating />
</template>

<script setup lang="ts">
import { RouterLink, RouterView, useRoute } from 'vue-router';
import { authService } from '@/services/api';
import { onMounted, ref, watch, computed } from 'vue';
import router from './router';
import SocialFloating from '@/components/SocialFloating.vue';

const isLoggedIn = ref(false);
const username = ref('');
const isMobileMenuOpen = ref(false);

// Router hooks
const currentRoute = useRoute();

// Verificar si el usuario es administrador
const isAdmin = computed(() => authService.isAdmin());

// Función para verificar la ruta actual
const isCurrentRoute = (path: string): boolean => {
  return currentRoute.path === path;
};

// Funciones para el menú hamburguesa
const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value;
};

const closeMobileMenu = () => {
  isMobileMenuOpen.value = false;
};

// Función para hacer scroll a la sección de productos
/* const scrollToProductStore = () => {
  const productStoreSection = document.querySelector('.product-store');
  if (productStoreSection) {
    productStoreSection.scrollIntoView({
      behavior: 'smooth',
      block: 'start'
    });
  }
}; */

// Función para hacer scroll a la sección de contacto
/* const scrollToContact = () => {
  const contactSection = document.querySelector('.contact-section');
  if (contactSection) {
    contactSection.scrollIntoView({
      behavior: 'smooth',
      block: 'start'
    });
  }
}; */

const checkAuthStatus = () => {
  isLoggedIn.value = authService.isAuthenticated();
  if (isLoggedIn.value) {
    const currentUser = authService.getCurrentUser();
    username.value = currentUser?.name || '';
  } else {
    username.value = '';
  }
};

const logout = () => {
  authService.logout();
  isLoggedIn.value = false;
  username.value = '';
  // Usar replace para no dejar historial que permita volver a la página autenticada
  router.replace({ name: 'home' });
};

const handleMobileLogout = () => {
  closeMobileMenu();
  logout();
};

onMounted(() => {
  checkAuthStatus();
});

const route = useRoute();
watch(route, () => {
  checkAuthStatus();
});
</script>

<style scoped>
.navbar {
  background: linear-gradient(180deg, rgba(0, 0, 0, 0.95) 0%, rgba(0, 0, 0, 0.9) 100%);
  margin: 0;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  height: 75px;
  padding: 0 clamp(20px, 5vw, 60px);
  box-shadow: 0 2px 24px rgba(220, 38, 38, 0.15), 0 1px 3px rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid rgba(220, 38, 38, 0.2);
}

/* Logo y marca */
.brand-container {
  display: flex;
  align-items: center;
  gap: 12px;
}

.link-navbar {
  outline: none;
  -webkit-tap-highlight-color: transparent;
}

.link-navbar:focus {
  outline: none;
  box-shadow: none;
}

.link-navbar:focus-visible {
  outline: none;
  box-shadow: none;
}

/* Logo imagen */
.brand-logo {
  height: 60px;
  width: auto;
  object-fit: contain;
  transition: all 0.3s ease;
  filter: drop-shadow(0 4px 12px rgba(220, 38, 38, 0.4));
  border-radius: 12px;
  padding: 4px;
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(10px);
}

.brand-logo:hover {
  transform: scale(1.05);
  filter: drop-shadow(0 6px 16px rgba(220, 38, 38, 0.6));
  background: rgba(255, 255, 255, 0.08);
}



.brand-info {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 2px;
}

.brand-title {
  font-size: 22px;
  font-weight: 900;
  line-height: 1;
  margin: 0;
  letter-spacing: 1.5px;
  text-transform: uppercase;
}

.brand-title .highlight {
  background: linear-gradient(135deg, var(--white) 0%, var(--primary-red) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  text-shadow: 0 2px 8px rgba(220, 38, 38, 0.3);
  filter: drop-shadow(0 2px 8px rgba(220, 38, 38, 0.3));
}

.brand-tagline {
  font-size: 10px;
  color: rgba(255, 255, 255, 0.6);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.brand-subtitle {
  font-size: 12px;
  color: #94a3b8;
  font-weight: 500;
  line-height: 1;
  margin: 0;
}

/* Navegación principal */
.nav-menu {
  display: flex;
  align-items: center;
  gap: 30px;
  margin-left: auto;
  margin-right: 30px;
}

.nav-link {
  color: rgba(255, 255, 255, 0.85);
  text-decoration: none !important;
  font-weight: 600;
  font-size: 15px;
  padding: 10px 18px;
  border-radius: 12px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  letter-spacing: 0.3px;
  outline: none !important;
  border: none !important;
  -webkit-tap-highlight-color: transparent !important;
}

.nav-link:focus,
.nav-link:focus-visible,
.nav-link:active,
.nav-link:visited {
  outline: none !important;
  box-shadow: none !important;
  border: none !important;
  text-decoration: none !important;
}

.nav-link::after {
  content: '' !important;
  position: absolute !important;
  bottom: 5px !important;
  left: 50% !important;
  transform: translateX(-50%) !important;
  width: 0 !important;
  height: 2px !important;
  background: var(--primary-red) !important;
  transition: width 0.3s ease !important;
  border: none !important;
}

.nav-link:hover {
  color: var(--white);
  background-color: rgba(220, 38, 38, 0.1);
  transform: translateY(-2px);
  border: none !important;
  outline: none !important;
}

.nav-link:hover::after {
  width: 70% !important;
  border: none !important;
}

.nav-link.active {
  color: var(--white) !important;
  background: rgba(220, 38, 38, 0.15) !important;
  border: none !important;
  box-shadow: none !important;
  outline: none !important;
  text-decoration: none !important;
}

.nav-link.active::after {
  width: 70% !important;
  background: var(--primary-red) !important;
  border: none !important;
}

.share-btn {
  background: linear-gradient(135deg, #22d3ee 0%, #0891b2 100%);
  color: #ffffff !important;
  font-weight: 600;
  box-shadow: 0 2px 10px rgba(34, 211, 238, 0.3);
}

.share-btn:hover {
  background: linear-gradient(135deg, #0891b2 0%, #0e7490 100%);
  box-shadow: 0 4px 15px rgba(34, 211, 238, 0.5);
  transform: translateY(-2px);
}

/* Controles de usuario */
.nav-controls {
  display: flex;
  align-items: center;
  gap: 15px;
}

.btn {
  padding: 10px 20px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 600;
  font-size: 14px;
  transition: all 0.3s ease;
  border: none;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.access-btn {
  background: linear-gradient(135deg, var(--primary-red) 0%, var(--dark-red) 100%);
  color: #ffffff;
  box-shadow: 0 4px 16px rgba(220, 38, 38, 0.35);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.access-btn:hover {
  background: linear-gradient(135deg, var(--dark-red) 0%, var(--tertiary-red) 100%);
  box-shadow: 0 6px 24px rgba(220, 38, 38, 0.5);
  transform: translateY(-3px);
}

.logout-btn {
  background: rgba(248, 113, 113, 0.1);
  color: #f87171;
  border: 1px solid rgba(248, 113, 113, 0.3);
}

.logout-btn:hover {
  background: rgba(248, 113, 113, 0.2);
  border-color: rgba(248, 113, 113, 0.5);
  transform: translateY(-1px);
}

.admin-btn {
  background: linear-gradient(135deg, var(--black) 0%, #1a1a1a 100%);
  color: var(--primary-red);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(220, 38, 38, 0.3);
  font-weight: 700;
}

.admin-btn:hover {
  background: linear-gradient(135deg, #1a1a1a 0%, var(--black) 100%);
  box-shadow: 0 6px 24px rgba(220, 38, 38, 0.4);
  transform: translateY(-3px);
  border-color: var(--primary-red);
}

.purchases-btn {
  background: var(--brand-accent-gradient);
  color: #ffffff;
  box-shadow: 0 2px 10px var(--brand-accent-glow);
}

.purchases-btn:hover {
  background: var(--brand-accent-gradient);
  filter: brightness(1.1);
  box-shadow: 0 4px 15px var(--brand-accent-glow);
  transform: translateY(-2px);
}

.user-greeting {
  color: var(--white);
  font-weight: 700;
  font-size: 14px;
  padding: 10px 16px;
  background: rgba(220, 38, 38, 0.15);
  border-radius: 12px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(220, 38, 38, 0.3);
  letter-spacing: 0.3px;
}

/* Menu hamburguesa */
.hamburger-menu {
  display: none;
  flex-direction: column;
  width: 30px;
  height: 30px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  justify-content: space-around;
  align-items: center;
  z-index: 1001;
}

.hamburger-menu span {
  display: block;
  height: 3px;
  width: 100%;
  background-color: var(--brand-primary-contrast);
  border-radius: 3px;
  transition: all 0.3s ease;
}

.hamburger-menu.active span:nth-child(1) {
  transform: rotate(45deg) translate(8px, 8px);
}

.hamburger-menu.active span:nth-child(2) {
  opacity: 0;
}

.hamburger-menu.active span:nth-child(3) {
  transform: rotate(-45deg) translate(7px, -6px);
}

/* Menu mobile */
.mobile-menu {
  display: none;
  position: fixed;
  top: 70px;
  left: 0;
  width: 100%;
  height: calc(100vh - 70px);
  background: var(--brand-gradient);
  transform: translateX(-100%);
  transition: transform 0.3s ease;
  z-index: 999;
  overflow-y: auto;
}

.mobile-menu.active {
  transform: translateX(0);
}

.mobile-menu-content {
  padding: 30px 20px;
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.mobile-nav-links {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.mobile-link {
  color: #e2e8f0;
  text-decoration: none;
  padding: 15px 20px;
  font-size: 18px;
  font-weight: 500;
  border-radius: 12px;
  transition: all 0.3s ease;
  text-align: center;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  outline: none;
  -webkit-tap-highlight-color: transparent;
}

.mobile-link:focus {
  outline: none;
  box-shadow: none;
}

.mobile-link:focus-visible {
  outline: none;
  box-shadow: none;
}

.mobile-link:hover {
  background: rgba(255, 255, 255, 0.1);
  color: #ffffff;
  transform: translateY(-2px);
}

.mobile-controls {
  display: flex;
  flex-direction: column;
  gap: 15px;
  padding-top: 20px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.mobile-btn {
  padding: 15px 20px;
  border-radius: 12px;
  text-decoration: none;
  font-weight: 600;
  font-size: 16px;
  text-align: center;
  transition: all 0.3s ease;
  border: none;
  cursor: pointer;
  width: 100%;
  font-family: inherit;
}

.mobile-btn.access-btn {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  color: #ffffff;
  box-shadow: 0 4px 15px rgba(16, 185, 129, 0.3);
}

.mobile-btn.logout-btn {
  background: rgba(248, 113, 113, 0.1);
  color: #f87171;
  border: 1px solid rgba(248, 113, 113, 0.3);
}

.mobile-btn.admin-btn {
  background: linear-gradient(135deg, #06b6d4 0%, #0891b2 100%);
  color: #ffffff;
  box-shadow: 0 4px 15px rgba(6, 182, 212, 0.3);
}

.mobile-btn.purchases-btn {
  background: linear-gradient(135deg, #60a5fa 0%, #3b82f6 100%);
  color: #ffffff;
  box-shadow: 0 4px 15px rgba(96, 165, 250, 0.3);
}

.mobile-user-greeting {
  color: #e2e8f0;
  text-align: center;
  padding: 15px 20px;
  font-weight: 600;
  font-size: 16px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

/* Responsive */
@media (max-width: 768px) {
  .navbar {
    height: 70px;
    padding: 0 20px;
  }

  .desktop-nav {
    display: none;
  }

  .hamburger-menu {
    display: flex;
  }

  .mobile-menu {
    display: block;
  }

  .brand-title {
    font-size: 18px;
  }

  .brand-logo {
    height: 50px;
    width: auto;
  }

  .brand-subtitle {
    font-size: 11px;
  }

  .logo-circle {
    width: 45px;
    height: 45px;
    font-size: 20px;
  }
}

@media (max-width: 480px) {
  .navbar {
    padding: 0 15px;
  }

  .brand-container {
    gap: 10px;
  }

  .brand-title {
    font-size: 16px;
  }

  .brand-logo {
    height: 45px;
    width: auto;
  }

  .logo-circle {
    width: 40px;
    height: 40px;
    font-size: 18px;
  }
}

/* Quitar subrayado del link principal */
.link-navbar {
  text-decoration: none !important;
}

/* Estilos para enlaces activos */
.nav-link.active,
.mobile-link.active {
  color: #0071e3;
  font-weight: 600;
  position: relative;
}

.nav-link.active::after,
.mobile-link.active::after {
  content: '';
  position: absolute;
  bottom: -4px;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: #0071e3;
  border-radius: 2px;
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scaleX(0.5);
  }
  to {
    opacity: 1;
    transform: scaleX(1);
  }
}

.link-navbar:hover {
  text-decoration: none !important;
}
</style>
