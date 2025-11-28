<template>
  <footer class="modern-footer">
    <div class="footer-content">
      <div class="footer-container">
        <!-- Grid principal -->
        <div class="footer-grid">
          <!-- Sección de marca -->
          <div class="brand-section">
            <img src="/images/logo.png" alt="SOYDANI Logo" class="footer-brand-logo" />
            <p class="footer-tagline">Tu tienda virtual de confianza</p>
            <p class="footer-description">
              Tecnología, decoración navideña y artículos para el hogar.
              Envíos a toda Colombia 🇨🇴
            </p>
          </div>

          <!-- Categorías -->
          <div class="footer-column">
            <h3 class="column-title">Categorías</h3>
            <ul class="footer-links">
              <li><RouterLink to="/iphone">📱 Tecnología</RouterLink></li>
              <li><RouterLink to="/mac">🎄 Navidad</RouterLink></li>
              <li><RouterLink to="/ipad">🏠 Hogar</RouterLink></li>
              <li><RouterLink to="/watch">🔥 Ofertas</RouterLink></li>
            </ul>
          </div>

          <!-- Contacto -->
          <div class="footer-column">
            <h3 class="column-title">Contacto</h3>
            <ul class="contact-list">
              <li>
                <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413Z"/>
                </svg>
                <span>+57 320 986 0099</span>
              </li>
              <li>
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"/>
                </svg>
                <span>Lun - Sáb: 9AM - 7PM</span>
              </li>
              <li>
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <rect x="1" y="3" width="15" height="13"/>
                  <path d="M16 8h5l3 3v5h-2m-4 0H2"/>
                  <circle cx="5.5" cy="18.5" r="2.5"/>
                  <circle cx="18.5" cy="18.5" r="2.5"/>
                </svg>
                <span>Envíos a toda Colombia</span>
              </li>
            </ul>
          </div>

          <!-- Redes sociales -->
          <div class="footer-column">
            <h3 class="column-title">Síguenos</h3>
            <div class="social-links-footer">
              <a href="https://www.facebook.com/danirodriguez.col" target="_blank" rel="noopener" class="social-link-footer facebook">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/>
                </svg>
                <div class="social-info-footer">
                  <span class="social-name-footer">Facebook</span>
                  <span class="social-handle-footer">@danirodriguez.col</span>
                </div>
              </a>

              <a href="https://www.instagram.com/soydanirodriguezz/" target="_blank" rel="noopener" class="social-link-footer instagram">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
                </svg>
                <div class="social-info-footer">
                  <span class="social-name-footer">Instagram</span>
                  <span class="social-handle-footer">@soydanirodriguezz</span>
                </div>
              </a>

              <a href="https://www.tiktok.com/@soydanirodriguezz" target="_blank" rel="noopener" class="social-link-footer tiktok">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M19.59 6.69a4.83 4.83 0 0 1-3.77-4.25V2h-3.45v13.67a2.89 2.89 0 0 1-5.2 1.74 2.89 2.89 0 0 1 2.31-4.64 2.93 2.93 0 0 1 .88.13V9.4a6.84 6.84 0 0 0-.88-.05A6.33 6.33 0 0 0 5 20.1a6.34 6.34 0 0 0 10.86-4.43V7a8.16 8.16 0 0 0 4.77 1.52v-3.4a4.85 4.85 0 0 1-1-.43z"/>
                </svg>
                <div class="social-info-footer">
                  <span class="social-name-footer">TikTok</span>
                  <span class="social-handle-footer">@soydanirodriguezz</span>
                </div>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Barra inferior -->
    <div class="footer-bottom">
      <div class="footer-container">
        <p class="copyright">&copy; 2025 SOYDANI. Todos los derechos reservados.</p>
        <p class="made-with">Hecho con ❤️ en Colombia</p>
      </div>
    </div>
  </footer>
</template>

<style scoped>
/* Footer moderno con grid de 4 columnas */
.modern-footer {
  background: linear-gradient(180deg,
      rgba(0, 0, 0, 0.98) 0%,
      rgba(10, 10, 10, 1) 100%);
  color: var(--white);
  padding: 4rem 0 0 0;
  position: relative;
  overflow: hidden;
  border-top: 3px solid var(--primary-red);
}

.modern-footer::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 2px;
  background: linear-gradient(90deg,
      transparent 0%,
      var(--primary-red) 50%,
      transparent 100%);
  box-shadow: 0 0 20px rgba(220, 38, 38, 0.6);
  animation: glow 2s ease-in-out infinite;
}

@keyframes glow {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}

.footer-content {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 2rem;
}

/* Grid de 4 columnas */
.footer-grid {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr 1fr;
  gap: 4rem;
  padding-bottom: 3rem;
  margin-bottom: 2rem;
  border-bottom: 1px solid rgba(220, 38, 38, 0.2);
}

/* Sección de marca/branding */
.brand-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 1.5rem;
}

/* Logo imagen del footer */
.footer-brand-logo {
  height: 140px;
  width: auto;
  object-fit: contain;
  transition: all 0.3s ease;
  filter: drop-shadow(0 8px 20px rgba(220, 38, 38, 0.5));
  margin-bottom: 0.5rem;
  border-radius: 20px;
  padding: 10px;
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
}

.footer-brand-logo:hover {
  transform: scale(1.05);
  filter: drop-shadow(0 12px 28px rgba(220, 38, 38, 0.7));
  background: rgba(255, 255, 255, 0.06);
}

.footer-brand-title {
  font-size: 2.5rem;
  font-weight: 900;
  color: var(--white);
  text-transform: uppercase;
  letter-spacing: 2px;
  margin: 0;
  line-height: 1;
  text-shadow: 0 4px 16px rgba(220, 38, 38, 0.4);
  background: linear-gradient(135deg, var(--white) 0%, var(--primary-red) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.footer-tagline {
  font-size: 1.1rem;
  color: var(--primary-red);
  font-weight: 700;
  margin: 0;
  text-transform: uppercase;
  letter-spacing: 1px;
  text-align: center;
}

.footer-description {
  font-size: 1rem;
  line-height: 1.8;
  color: rgba(255, 255, 255, 0.8);
  margin: 0;
  max-width: 100%;
  text-align: center;
}

/* Columnas de enlaces */
.footer-column {
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}

.column-title {
  font-size: 1.2rem;
  font-weight: 700;
  color: var(--white);
  margin: 0 0 0.5rem 0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  position: relative;
  padding-bottom: 0.8rem;
}

.column-title::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 40px;
  height: 3px;
  background: linear-gradient(90deg, var(--primary-red) 0%, transparent 100%);
  border-radius: 2px;
}

/* Enlaces de categorías */
.footer-links {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  list-style: none;
  padding: 0;
  margin: 0;
}

.footer-links li {
  padding: 0;
}

.footer-links a {
  color: rgba(255, 255, 255, 0.75);
  text-decoration: none;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.4rem 0;
}

.footer-links a::before {
  content: '▸';
  color: var(--primary-red);
  font-size: 0.8rem;
  opacity: 0;
  transform: translateX(-5px);
  transition: all 0.3s ease;
}

.footer-links a:hover {
  color: var(--primary-red);
  transform: translateX(5px);
}

.footer-links a:hover::before {
  opacity: 1;
  transform: translateX(0);
}

/* Lista de contacto */
.contact-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  list-style: none;
}

.contact-list li {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  color: rgba(255, 255, 255, 0.85);
  font-size: 0.95rem;
  transition: all 0.3s ease;
  padding: 0.5rem 0;
}

.contact-list li:hover {
  color: rgba(255, 255, 255, 1);
  transform: translateX(5px);
}

.contact-list li svg {
  flex-shrink: 0;
  color: rgba(255, 255, 255, 0.5);
  transition: color 0.3s ease;
}

.contact-list li:hover svg {
  color: var(--primary-red);
}

/* Grid de redes sociales */
.social-links-footer {
  display: grid;
  grid-template-columns: 1fr;
  gap: 0.8rem;
}

.social-link-footer {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 1rem;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  text-decoration: none;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.social-link-footer::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(220, 38, 38, 0.1), transparent);
  transition: left 0.5s ease;
}

.social-link-footer:hover::before {
  left: 100%;
}

.social-link-footer:hover {
  background: rgba(255, 255, 255, 0.08);
  border-color: rgba(255, 255, 255, 0.2);
  transform: translateY(-3px);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.3);
}

.social-link-footer svg {
  width: 24px;
  height: 24px;
  flex-shrink: 0;
  color: rgba(255, 255, 255, 0.6);
  transition: all 0.3s ease;
}

.social-link-footer:hover svg {
  color: var(--white);
  transform: scale(1.1);
}

.social-info-footer {
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
  flex: 1;
}

.social-name-footer {
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.5);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.social-handle-footer {
  color: rgba(255, 255, 255, 0.9);
  font-size: 0.95rem;
  font-weight: 600;
  line-height: 1.3;
}

.social-link-footer:hover .social-name-footer {
  color: var(--primary-red);
}

/* Barra inferior */
.footer-bottom {
  background: rgba(0, 0, 0, 0.5);
  border-top: 1px solid rgba(220, 38, 38, 0.15);
  padding: 1.5rem 0;
  text-align: center;
}

.copyright {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.9rem;
  margin: 0;
}

.made-with {
  color: rgba(255, 255, 255, 0.7);
  font-size: 0.9rem;
  margin: 0.5rem 0 0 0;
}



/* Responsive Design */
@media (max-width: 1200px) {
  .footer-grid {
    grid-template-columns: 1.5fr 1fr 1fr 1fr;
    gap: 3rem;
  }
}

@media (max-width: 992px) {
  .footer-grid {
    grid-template-columns: 1fr 1fr;
    gap: 3rem 2rem;
  }

  .brand-section {
    grid-column: 1 / -1;
  }
}

@media (max-width: 768px) {
  .modern-footer {
    padding: 3rem 0 0 0;
  }

  .footer-content {
    padding: 0 1.5rem;
  }

  .footer-grid {
    grid-template-columns: 1fr;
    gap: 2.5rem;
    padding-bottom: 2rem;
  }

  .brand-section {
    align-items: center;
    text-align: center;
  }

  .footer-logo {
    width: 110px;
    height: 110px;
    font-size: 2.2rem;
  }

  .footer-brand-title {
    font-size: 2rem;
  }

  .footer-description {
    max-width: 100%;
    text-align: center;
  }

  .footer-column {
    text-align: center;
  }

  .column-title::after {
    left: 50%;
    transform: translateX(-50%);
  }

  .footer-links a {
    justify-content: center;
  }

  .contact-list {
    align-items: center;
  }

  .contact-item {
    max-width: 300px;
  }

  .social-links-footer {
    max-width: 300px;
    margin: 0 auto;
  }
}

@media (max-width: 480px) {
  .modern-footer {
    padding: 2rem 0 0 0;
  }

  .footer-content {
    padding: 0 1rem;
  }

  .footer-grid {
    gap: 2rem;
  }

  .footer-logo {
    width: 90px;
    height: 90px;
    font-size: 1.8rem;
  }

  .footer-brand-title {
    font-size: 1.6rem;
    letter-spacing: 1px;
  }

  .footer-tagline {
    font-size: 0.95rem;
  }

  .footer-description {
    font-size: 0.9rem;
  }

  .column-title {
    font-size: 1.1rem;
  }

  .footer-links a {
    font-size: 0.9rem;
  }

  .contact-icon {
    width: 32px;
    height: 32px;
  }

  .contact-icon svg {
    width: 16px;
    height: 16px;
  }

  .contact-label {
    font-size: 0.7rem;
  }

  .contact-value {
    font-size: 0.85rem;
  }

  .social-icon-footer {
    width: 38px;
    height: 38px;
  }

  .social-icon-footer svg {
    width: 18px;
    height: 18px;
  }

  .social-name-footer {
    font-size: 0.7rem;
  }

  .social-handle-footer {
    font-size: 0.9rem;
  }

  .footer-bottom {
    padding: 1rem 0;
  }

  .copyright,
  .made-with {
    font-size: 0.8rem;
  }
}
</style>

<script setup lang="ts">
import { RouterLink } from 'vue-router'
</script>
