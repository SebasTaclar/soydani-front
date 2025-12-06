<template>
  <main class="home-main">
    <!-- Efecto de nieve navideña -->
    <div class="snowflakes" aria-hidden="true">
      <div class="snowflake" v-for="n in 50" :key="n">❅</div>
    </div>

    <!-- Partículas tecnológicas flotantes -->
    <div class="tech-particles" aria-hidden="true">
      <div class="particle" v-for="n in 20" :key="n"></div>
    </div>

    <section class="section">
      <MainBanner @showRules="openRulesModal" />
    </section>

    <!-- Botón de Novedades antes de la tienda -->
    <section class="novedades-button-section">
      <button @click="showNovedadesModal = true" class="novedades-trigger-btn">
        <span class="trigger-icon">✨</span>
        <div class="trigger-content">
          <div class="trigger-badge">Novedades De DaniShop</div>
          <h3 class="trigger-title">Descubre nuestras novedades</h3>
          <p class="trigger-subtitle">Explora lanzamientos recientes y una selección para ti.</p>
          <div class="trigger-meta">Productos Garantizados · Entrega segura</div>
        </div>
        <span class="trigger-arrow">Descubrir ahora →</span>
      </button>
    </section>

    <!-- Modal de Novedades -->
    <transition name="modal-fade">
      <div v-if="showNovedadesModal" class="novedades-modal-overlay" @click="showNovedadesModal = false">
        <div class="novedades-modal-content" @click.stop>
          <button class="novedades-modal-close" @click="showNovedadesModal = false">
            <span>✕</span>
          </button>
          <div class="novedades-modal-header">
            <h2 class="novedades-modal-title">
              <span class="title-icon">✨</span>
              Descubre nuestras novedades
            </h2>
            <p class="novedades-modal-subtitle">Explora lanzamientos recientes y una selección curada para ti.</p>
          </div>
          <div class="novedades-modal-body">
            <ProductShowcase />
          </div>
        </div>
      </div>
    </transition>

    <section class="section">
      <ProductStore />
    </section>

    <section class="section">
      <ContactSection />
    </section>


    <section class="section offset">
      <Footer_ />
    </section>

    <!-- Enlace flotante para prueba de sincronización -->
    <!-- <router-link to="/test-sync" class="test-sync-link">
      🧪 Probar Sincronización
    </router-link> -->

    <!-- Modal de reglas -->

  </main>
</template>

<script setup lang="ts">
import { ref, watch, defineAsyncComponent } from 'vue'
import MainBanner from '@/components/MainBanner.vue'

// Carga diferida (code splitting) de secciones pesadas
const ProductShowcase = defineAsyncComponent(() => import('@/components/ProductShowcase.vue'))
const ProductStore = defineAsyncComponent(() => import('@/components/ProductStore.vue'))
const ContactSection = defineAsyncComponent(() => import('@/components/ContactSection.vue'))
const Footer_ = defineAsyncComponent(() => import('@/components/Footer_.vue'))

// Estado del modal de novedades
const showNovedadesModal = ref(false)

// Estado del modal de reglas
const showRulesModal = ref(false)

const openRulesModal = () => {
  showRulesModal.value = true
}

const closeRulesModal = () => {
  showRulesModal.value = false
}

// Watch para bloquear scroll cuando se abre el modal de novedades
watch(showNovedadesModal, (isOpen) => {
  if (isOpen) {
    const scrollY = window.scrollY
    document.body.style.position = 'fixed'
    document.body.style.top = `-${scrollY}px`
    document.body.style.width = '100%'
    document.body.style.overflow = 'hidden'
  } else {
    const scrollY = document.body.style.top
    document.body.style.position = ''
    document.body.style.top = ''
    document.body.style.width = ''
    document.body.style.overflow = ''
    window.scrollTo(0, parseInt(scrollY || '0') * -1)
  }
})

defineOptions({
  name: 'HomeView',
})

defineExpose({
  showRulesModal,
  openRulesModal,
  closeRulesModal
})

</script>

<style scoped>
/* === BOTÓN DE NOVEDADES === */
.novedades-button-section {
  max-width: 1100px;
  margin: 2rem auto;
  padding: 0 2rem;
}

.novedades-trigger-btn {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
  padding: 2rem 2.5rem;
  background: linear-gradient(120deg, #1a1a1a 0%, #0a0a0a 30%, #1f1a2e 60%, #0a0a0a 100%);
  background-size: 200% 200%;
  border: 2px solid rgba(220, 38, 38, 0.7);
  border-radius: 18px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
  box-shadow: 0 8px 32px rgba(220, 38, 38, 0.25);
  animation: floatPulse 3.5s ease-in-out infinite, gradientShift 6s ease-in-out infinite;
}

.novedades-trigger-btn::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(220, 38, 38, 0.22) 0%, transparent 100%);
  opacity: 0;
  transition: opacity 0.4s;
}

.novedades-trigger-btn::after {
  content: '';
  position: absolute;
  top: -50%;
  left: -20%;
  width: 40%;
  height: 200%;
  background: linear-gradient(120deg, rgba(255, 255, 255, 0.06), rgba(255, 255, 255, 0));
  transform: rotate(8deg);
  opacity: 0;
  transition: opacity 0.35s;
}

.novedades-trigger-btn:hover::before {
  opacity: 1;
}

.novedades-trigger-btn:hover::after {
  opacity: 1;
  animation: shineSweep 1.4s ease-in-out;
}

.novedades-trigger-btn:hover {
  transform: translateY(-6px) scale(1.01);
  border-color: #ef4444;
  box-shadow: 0 14px 56px rgba(220, 38, 38, 0.5);
}

.trigger-icon {
  font-size: 3rem;
  animation: sparkle 2s ease-in-out infinite;
  flex-shrink: 0;
}

@keyframes sparkle {
  0%, 100% { transform: rotate(0deg) scale(1); }
  25% { transform: rotate(-15deg) scale(1.1); }
  75% { transform: rotate(15deg) scale(1.1); }
}

.trigger-content {
  flex: 1;
  text-align: left;
}

.trigger-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.4rem 0.9rem;
  background: rgba(220, 38, 38, 0.12);
  border: 1px solid rgba(220, 38, 38, 0.4);
  color: #fef2f2;
  font-size: 0.8rem;
  font-weight: 600;
  border-radius: 999px;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  margin-bottom: 0.5rem;
}

.trigger-title {
  font-size: 1.85rem;
  font-weight: 700;
  color: white;
  margin: 0 0 0.4rem 0;
  background: linear-gradient(135deg, #ffffff 0%, #DC2626 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.trigger-subtitle {
  font-size: 1rem;
  color: rgba(255, 255, 255, 0.7);
  margin: 0;
}

.trigger-meta {
  margin-top: 0.25rem;
  font-size: 0.95rem;
  color: rgba(255, 255, 255, 0.6);
}

.trigger-arrow {
  font-size: 1.05rem;
  padding: 0.85rem 1.5rem;
  color: #111827;
  background: #fca5a5;
  border-radius: 999px;
  font-weight: 700;
  letter-spacing: 0.01em;
  transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  flex-shrink: 0;
}

.novedades-trigger-btn:hover .trigger-arrow {
  transform: translateX(12px) scale(1.02);
}

/* Microinteracciones llamativas */
@keyframes floatPulse {
  0%, 100% { transform: translateY(0); box-shadow: 0 8px 32px rgba(220, 38, 38, 0.2); }
  50% { transform: translateY(-3px); box-shadow: 0 10px 40px rgba(220, 38, 38, 0.35); }
}

@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

@keyframes shineSweep {
  0% { transform: translateX(-20%) rotate(8deg); opacity: 0; }
  30% { opacity: 1; }
  100% { transform: translateX(260%) rotate(8deg); opacity: 0; }
}

.novedades-title {
  font-size: 3rem;
  font-weight: 700;
  color: white;
  margin: 0 0 1rem 0;
  background: linear-gradient(135deg, #ffffff 0%, #DC2626 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.novedades-description {
  font-size: 1.125rem;
  color: rgba(255, 255, 255, 0.7);
  max-width: 600px;
  margin: 0 auto;
}

/* === MODAL DE NOVEDADES === */
.novedades-modal-overlay {
  position: fixed;
  inset: 0;
  background: rgb(0, 0, 0);
  backdrop-filter: blur(10px);
  z-index: 999999;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  overflow: hidden;
}

.novedades-modal-content {
  background: linear-gradient(135deg, #1a1a1a 0%, #0a0a0a 100%);
  border-radius: 24px;
  width: 100%;
  max-width: 1400px;
  max-height: 90vh;
  overflow: hidden;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(220, 38, 38, 0.3);
  display: flex;
  flex-direction: column;
  position: relative;
}

.novedades-modal-close {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 48px;
  height: 48px;
  background: rgba(220, 38, 38, 0.1);
  border: 2px solid #DC2626;
  border-radius: 50%;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
  z-index: 10;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.novedades-modal-close:hover {
  background: #DC2626;
  transform: rotate(90deg) scale(1.1);
}

.novedades-modal-header {
  padding: 2rem 2.5rem;
  border-bottom: 1px solid rgba(220, 38, 38, 0.2);
  background: linear-gradient(135deg, rgba(220, 38, 38, 0.1) 0%, transparent 100%);
}

.novedades-modal-title {
  display: flex;
  align-items: center;
  gap: 1rem;
  font-size: 2rem;
  font-weight: 700;
  color: white;
  margin: 0 0 0.5rem 0;
}

.title-icon {
  font-size: 2.5rem;
  animation: sparkle 2s ease-in-out infinite;
}

.novedades-modal-subtitle {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1rem;
  margin: 0;
}

.novedades-modal-body {
  flex: 1;
  overflow-y: auto;
  padding: 1.5rem;
}

/* Animaciones del modal */
.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s ease;
}

.modal-fade-enter-active .novedades-modal-content,
.modal-fade-leave-active .novedades-modal-content {
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-from .novedades-modal-content {
  transform: scale(0.9) translateY(30px);
  opacity: 0;
}

.modal-fade-leave-to .novedades-modal-content {
  transform: scale(0.95) translateY(20px);
  opacity: 0;
}

/* Responsive */
@media (max-width: 768px) {
  .novedades-button-section {
    padding: 0 0.75rem;
    margin: 0.3rem auto;
  }

  .novedades-trigger-btn {
    padding: 1rem 1rem;
    gap: 0.75rem;
    flex-direction: column;
    text-align: center;
    border-radius: 12px;
  }

  .trigger-icon {
    font-size: 1.5rem;
  }

  .trigger-content {
    text-align: center;
  }

  .trigger-badge {
    font-size: 0.6rem;
    padding: 0.2rem 0.5rem;
  }

  .trigger-title {
    font-size: 1.1rem;
  }

  .trigger-subtitle {
    font-size: 0.75rem;
  }

  .trigger-meta {
    font-size: 0.7rem;
  }

  .trigger-arrow {
    font-size: 0.75rem;
    padding: 0.5rem 0.75rem;
    width: 100%;
  }

  .novedades-trigger-btn:hover .trigger-arrow {
    transform: translateY(3px);
  }

  .novedades-modal-content {
    border-radius: 16px;
    max-height: 95vh;
  }

  .novedades-modal-header {
    padding: 1.5rem 1rem;
  }

  .novedades-modal-title {
    font-size: 1.5rem;
  }

  .title-icon {
    font-size: 1.75rem;
  }

  .novedades-modal-close {
    width: 40px;
    height: 40px;
    font-size: 1.25rem;
  }

  .novedades-modal-body {
    padding: 1rem;
  }
}

/* === EFECTOS VISUALES === */

/* Animación de nieve cayendo */
@keyframes snowfall {
  0% {
    top: -10%;
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    top: 100%;
    opacity: 0;
  }
}

/* Movimiento horizontal de nieve */
@keyframes sway {
  0%, 100% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(100px);
  }
}

/* Partículas tecnológicas flotantes */
@keyframes float-particle {
  0% {
    transform: translateY(0) translateX(0) rotate(0deg);
    opacity: 0.2;
  }
  25% {
    opacity: 0.5;
  }
  50% {
    transform: translateY(-200px) translateX(50px) rotate(180deg);
    opacity: 0.8;
  }
  75% {
    opacity: 0.5;
  }
  100% {
    transform: translateY(-400px) translateX(-50px) rotate(360deg);
    opacity: 0;
  }
}

/* Resplandor rojo pulsante */
@keyframes glow {
  0%, 100% {
    box-shadow: 0 0 20px rgba(220, 38, 38, 0.3);
  }
  50% {
    box-shadow: 0 0 40px rgba(220, 38, 38, 0.6);
  }
}

.home-main {
  padding-top: 60px;
  background: linear-gradient(180deg, #000000 0%, #0a0a0a 50%, #000000 100%);
  color: var(--white);
  width: 100%;
  max-width: 100vw;
  overflow-x: hidden;
  box-sizing: border-box;
  position: relative;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

/* === EFECTO DE NIEVE === */
.snowflakes {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 100;
  overflow: hidden;
}

.snowflake {
  position: absolute;
  top: -10%;
  color: #fff;
  font-size: 1em;
  user-select: none;
  animation: snowfall linear infinite, sway ease-in-out infinite;
  text-shadow: 0 0 5px rgba(255, 255, 255, 0.8);
}

.snowflake:nth-child(1n) { left: 1%; animation-duration: 12s, 3s; animation-delay: 0s, 0s; font-size: 0.8em; }
.snowflake:nth-child(2n) { left: 10%; animation-duration: 15s, 4s; animation-delay: 1s, 0.5s; font-size: 1.2em; }
.snowflake:nth-child(3n) { left: 20%; animation-duration: 18s, 5s; animation-delay: 2s, 1s; font-size: 1em; }
.snowflake:nth-child(4n) { left: 30%; animation-duration: 14s, 3.5s; animation-delay: 3s, 1.5s; font-size: 1.5em; }
.snowflake:nth-child(5n) { left: 40%; animation-duration: 16s, 4.5s; animation-delay: 1.5s, 0s; font-size: 0.9em; }
.snowflake:nth-child(6n) { left: 50%; animation-duration: 13s, 3s; animation-delay: 2.5s, 0.5s; font-size: 1.3em; }
.snowflake:nth-child(7n) { left: 60%; animation-duration: 17s, 5s; animation-delay: 0.5s, 1s; font-size: 1.1em; }
.snowflake:nth-child(8n) { left: 70%; animation-duration: 19s, 4s; animation-delay: 3.5s, 1.5s; font-size: 0.8em; }
.snowflake:nth-child(9n) { left: 80%; animation-duration: 14s, 3.5s; animation-delay: 1s, 0s; font-size: 1.4em; }
.snowflake:nth-child(10n) { left: 90%; animation-duration: 16s, 4.5s; animation-delay: 2s, 0.5s; font-size: 1em; }
.snowflake:nth-child(11n) { left: 5%; animation-duration: 15s, 3s; animation-delay: 0.5s, 1s; font-size: 1.2em; }
.snowflake:nth-child(12n) { left: 15%; animation-duration: 17s, 4s; animation-delay: 2.5s, 1.5s; font-size: 0.9em; }
.snowflake:nth-child(13n) { left: 25%; animation-duration: 13s, 5s; animation-delay: 1.5s, 0s; font-size: 1.1em; }

/* === PARTÍCULAS TECNOLÓGICAS === */
.tech-particles {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 50;
  overflow: hidden;
}

.particle {
  position: absolute;
  bottom: -20px;
  width: 4px;
  height: 4px;
  background: linear-gradient(135deg, #DC2626, #EF4444);
  border-radius: 50%;
  animation: float-particle linear infinite;
  box-shadow:
    0 0 10px rgba(220, 38, 38, 0.5),
    0 0 20px rgba(220, 38, 38, 0.3),
    0 0 30px rgba(220, 38, 38, 0.2);
}

.particle:nth-child(1) { left: 5%; animation-duration: 8s; animation-delay: 0s; width: 6px; height: 6px; }
.particle:nth-child(2) { left: 15%; animation-duration: 10s; animation-delay: 1s; width: 4px; height: 4px; }
.particle:nth-child(3) { left: 25%; animation-duration: 12s; animation-delay: 2s; width: 5px; height: 5px; }
.particle:nth-child(4) { left: 35%; animation-duration: 9s; animation-delay: 0.5s; width: 7px; height: 7px; }
.particle:nth-child(5) { left: 45%; animation-duration: 11s; animation-delay: 1.5s; width: 4px; height: 4px; }
.particle:nth-child(6) { left: 55%; animation-duration: 13s; animation-delay: 2.5s; width: 6px; height: 6px; }
.particle:nth-child(7) { left: 65%; animation-duration: 10s; animation-delay: 0.8s; width: 5px; height: 5px; }
.particle:nth-child(8) { left: 75%; animation-duration: 12s; animation-delay: 1.8s; width: 4px; height: 4px; }
.particle:nth-child(9) { left: 85%; animation-duration: 9s; animation-delay: 2.8s; width: 6px; height: 6px; }
.particle:nth-child(10) { left: 95%; animation-duration: 11s; animation-delay: 0.3s; width: 5px; height: 5px; }
.particle:nth-child(11) { left: 10%; animation-duration: 14s; animation-delay: 1.2s; width: 4px; height: 4px; }
.particle:nth-child(12) { left: 20%; animation-duration: 8s; animation-delay: 2.2s; width: 7px; height: 7px; }
.particle:nth-child(13) { left: 30%; animation-duration: 10s; animation-delay: 0.7s; width: 5px; height: 5px; }
.particle:nth-child(14) { left: 40%; animation-duration: 13s; animation-delay: 1.7s; width: 6px; height: 6px; }
.particle:nth-child(15) { left: 50%; animation-duration: 9s; animation-delay: 2.7s; width: 4px; height: 4px; }
.particle:nth-child(16) { left: 60%; animation-duration: 11s; animation-delay: 0.4s; width: 5px; height: 5px; }
.particle:nth-child(17) { left: 70%; animation-duration: 12s; animation-delay: 1.4s; width: 6px; height: 6px; }
.particle:nth-child(18) { left: 80%; animation-duration: 10s; animation-delay: 2.4s; width: 4px; height: 4px; }
.particle:nth-child(19) { left: 90%; animation-duration: 14s; animation-delay: 0.6s; width: 7px; height: 7px; }
.particle:nth-child(20) { left: 12%; animation-duration: 9s; animation-delay: 1.6s; width: 5px; height: 5px; }

.section {
  padding: 1.5rem 0 0 0;
  border-bottom: 1px solid rgba(220, 38, 38, 0.1);
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
}

.section:last-child {
  border-bottom: none;
}

/* Resplandor en hover */
.section:hover {
  animation: glow 2s ease-in-out infinite;
}

/* Responsive: reducir efectos en móviles */
@media (max-width: 768px) {
  .home-main {
    padding-top: 50px;
    gap: 1rem;
  }

  .section {
    padding: 1rem 0 0 0;
  }

  .snowflake:nth-child(n+20) {
    display: none;
  }

  .particle:nth-child(n+10) {
    display: none;
  }
}

/* Extra small screens */
@media (max-width: 480px) {
  .home-main {
    padding-top: 45px;
    gap: 0.75rem;
  }

  .section {
    padding: 0.5rem 0 0 0;
  }

  .novedades-button-section {
    padding: 0 0.75rem;
    margin: 1rem auto;
  }

  .novedades-trigger-btn {
    padding: 1.25rem 1rem;
    gap: 0.75rem;
    border-radius: 14px;
  }

  .trigger-icon {
    font-size: 2rem;
  }

  .trigger-badge {
    font-size: 0.7rem;
    padding: 0.25rem 0.6rem;
  }

  .trigger-title {
    font-size: 1.25rem;
  }

  .trigger-subtitle {
    font-size: 0.8rem;
  }

  .trigger-meta {
    font-size: 0.75rem;
  }

  .trigger-arrow {
    font-size: 0.9rem;
    padding: 0.7rem 1rem;
  }

  .novedades-modal-content {
    border-radius: 12px;
    max-height: 98vh;
  }

  .novedades-modal-header {
    padding: 1rem 0.75rem;
  }

  .novedades-modal-title {
    font-size: 1.25rem;
    gap: 0.5rem;
  }

  .title-icon {
    font-size: 1.5rem;
  }

  .novedades-modal-subtitle {
    font-size: 0.85rem;
  }

  .novedades-modal-close {
    width: 36px;
    height: 36px;
    font-size: 1rem;
    top: 0.75rem;
    right: 0.75rem;
  }

  .novedades-modal-body {
    padding: 0.75rem;
  }
}


/* Enlace flotante para prueba de sincronización */
.test-sync-link {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: linear-gradient(45deg, #667eea, #764ba2);
  color: white;
  padding: 12px 20px;
  border-radius: 25px;
  text-decoration: none;
  font-weight: 600;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
  z-index: 1000;
  transition: all 0.3s ease;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.test-sync-link:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.3);
  background: linear-gradient(45deg, #764ba2, #667eea);
}
</style>
