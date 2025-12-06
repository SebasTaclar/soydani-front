<template>
  <div class="ofertas-page">
    <section class="hero-banner">
      <div class="carousel-container">
        <div class="carousel-track" :style="{ transform: `translateX(-${currentSlide * 100}%)` }">
          <div v-for="(banner, index) in ofertasBanners" :key="index" class="carousel-slide">
            <div class="slide-content">
              <div class="slide-text">
                <h1 class="slide-title">{{ banner.title }}</h1>
                <p class="slide-subtitle">{{ banner.subtitle }}</p>
                <button @click="scrollToProducts" class="shop-now-btn">
                  Ver ofertas
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="m7 13 5 5 5-5"/>
                    <path d="m7 6 5 5 5-5"/>
                  </svg>
                </button>
              </div>
              <div class="slide-image">
                <img :src="banner.image" :alt="banner.title" loading="lazy" />
              </div>
            </div>
          </div>
        </div>

        <!-- Controles del carrusel -->
        <button class="carousel-btn prev" @click="prevSlide" aria-label="Anterior">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="15 18 9 12 15 6"></polyline>
          </svg>
        </button>
        <button class="carousel-btn next" @click="nextSlide" aria-label="Siguiente">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="9 18 15 12 9 6"></polyline>
          </svg>
        </button>

        <!-- Indicadores -->
        <div class="carousel-indicators">
          <button
            v-for="(banner, index) in ofertasBanners"
            :key="index"
            class="indicator"
            :class="{ active: currentSlide === index }"
            @click="goToSlide(index)"
            :aria-label="`Ir a slide ${index + 1}`"
          ></button>
        </div>
      </div>
    </section>

    <section class="products-section">
      <div class="container">
        <!-- Filtros -->
        <div class="filters-bar">
          <div class="filters-group">
            <div class="filter-item">
              <label for="sort-select">Ordenar por:</label>
              <select id="sort-select" v-model="sortBy" class="filter-select">
                <option value="price-desc">Precio: Mayor a Menor</option>
                <option value="price-asc">Precio: Menor a Mayor</option>
                <option value="name-asc">Nombre: A-Z</option>
                <option value="name-desc">Nombre: Z-A</option>
              </select>
            </div>

            <div class="filter-item">
              <label for="status-select">Disponibilidad:</label>
              <select id="status-select" v-model="filterStatus" class="filter-select">
                <option value="all">Todos</option>
                <option value="available">Disponibles</option>
                <option value="coming-soon">Próximamente</option>
              </select>
            </div>

            <div class="filter-item price-range">
              <label>Rango de precio:</label>
              <div class="price-inputs">
                <input
                  type="number"
                  v-model.number="minPrice"
                  placeholder="Mín"
                  class="price-input"
                  min="0"
                />
                <span>-</span>
                <input
                  type="number"
                  v-model.number="maxPrice"
                  placeholder="Máx"
                  class="price-input"
                  min="0"
                />
              </div>
            </div>
          </div>

          <div style="display: flex; align-items: center; gap: 1rem;">
            <div class="results-count">
              {{ filteredAndSortedProducts.length }} productos encontrados
            </div>
            <button @click="clearFilters" class="clear-filters-btn">
              Limpiar filtros
            </button>
          </div>
        </div>

        <div v-if="isLoadingProducts" class="loading-state">
          <div class="spinner"></div>
          <p>Cargando productos...</p>
        </div>

        <div v-else-if="filteredAndSortedProducts.length > 0" class="products-container">
          <div
            v-for="product in filteredAndSortedProducts"
            :key="product.id"
            class="product-card-modern"
            @click="handleAddToCart(product)"
          >
            <!-- Imagen del producto -->
            <div class="product-visual">
              <img
                :src="product.images[0]"
                :alt="product.name"
                loading="lazy"
                decoding="async"
                class="product-image"
              />
            </div>

            <!-- Información del producto -->
            <div class="product-details">
              <h2 class="product-title">{{ product.name }}</h2>
              <p class="product-tagline">{{ product.description }}</p>

              <div class="product-pricing">
                <span class="product-price">Precio ${{ product.price.toLocaleString() }}</span>
                <span v-if="product.originalPrice" class="product-price-original">
                  ${{ product.originalPrice.toLocaleString() }}
                </span>
              </div>

              <button class="product-btn" @click.stop="handleAddToCart(product)">
                Comprar
              </button>
            </div>
          </div>
        </div>

        <div v-else class="empty-state">
          <p>No hay ofertas disponibles en este momento</p>
        </div>
      </div>
    </section>

    <section class="info-section">
      <div class="container">
        <div class="info-content-centered">
          <h2 class="info-title">Ofertas - Los Mejores Precios en Todos los Productos</h2>
          <p class="info-text">
            ¡Aprovecha las mejores ofertas y descuentos especiales en todos nuestros productos!
            Desde productos tecnológicos hasta artículos para el hogar, decoración navideña y mucho más.
            Descuentos increíbles en una amplia variedad de categorías. Ofertas por tiempo limitado con
            garantía de calidad, envío seguro y la mejor atención al cliente. Encuentra lo que buscas
            al mejor precio del mercado sin sacrificar calidad. Ahorra en grande con nuestras
            promociones exclusivas. Renueva, decora y equipa tu vida con productos de primera
            a precios irresistibles. ¡No dejes pasar estas oportunidades únicas!
          </p>
        </div>
      </div>
    </section>

    <!-- Modal del producto -->
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <button class="modal-close floating" @click="closeModal">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="m18 6-12 12"/>
            <path d="m6 6 12 12"/>
          </svg>
        </button>
        <div class="modal-body">
          <div class="modal-image-container">
            <div class="modal-image">
              <img
                :src="selectedProduct?.images[selectedImageIndex]"
                :alt="`${selectedProduct?.name} - ${selectedImageIndex + 1}`"
                loading="lazy"
                decoding="async"
              />
            </div>
            <div v-if="selectedProduct?.images && selectedProduct.images.length > 1" class="image-navigation">
              <button
                class="nav-btn prev"
                @click="previousImage"
                :disabled="selectedImageIndex === 0"
                aria-label="Imagen anterior"
              >
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="m15 18-6-6 6-6"/>
                </svg>
              </button>
              <button
                class="nav-btn next"
                @click="nextImage"
                :disabled="selectedImageIndex === (selectedProduct?.images.length || 1) - 1"
                aria-label="Imagen siguiente"
              >
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="m9 18 6-6-6-6"/>
                </svg>
              </button>
            </div>
            <div v-if="selectedProduct?.images && selectedProduct.images.length > 1" class="image-indicators">
              <button
                v-for="(image, index) in selectedProduct.images"
                :key="index"
                class="indicator"
                :class="{ active: selectedImageIndex === index }"
                @click="selectedImageIndex = index"
                :aria-label="`Ver imagen ${index + 1}`"
              ></button>
            </div>
          </div>
          <div class="modal-info">
            <h2 class="modal-title">{{ selectedProduct?.name }}</h2>
            <div class="modal-category">
              AirPods
            </div>
            <p class="modal-description">
              {{ selectedProduct?.description }}
            </p>
            <div class="modal-price">
              <span class="modal-current-price">${{ selectedProduct?.price?.toLocaleString() }}</span>
              <span v-if="selectedProduct?.originalPrice" class="modal-original-price">
                ${{ selectedProduct?.originalPrice?.toLocaleString() }}
              </span>
            </div>

            <!-- Selector de colores en el modal -->
            <div v-if="selectedProduct?.colors && selectedProduct.colors.length > 0" class="modal-colors">
              <h4>Seleccionar color:</h4>
              <div class="color-options">
                <div
                  v-for="colorName in selectedProduct.colors"
                  :key="colorName"
                  class="color-option"
                  :class="{ active: modalSelectedColor === colorName }"
                  @click="modalSelectedColor = colorName"
                  :title="colorName"
                  :aria-label="'Color ' + colorName"
                >
                  <div class="color-circle" :style="{ backgroundColor: getColorHex(colorName) }">
                    <svg v-if="modalSelectedColor === colorName" class="check-icon" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="3">
                      <path d="M20 6L9 17l-5-5"/>
                    </svg>
                  </div>
                  <span class="color-name">{{ colorName }}</span>
                </div>
              </div>
            </div>

            <!-- Indicador de color de imagen -->
            <div v-else class="color-image-indicator">
              <div class="image-color-badge">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <rect x="3" y="3" width="18" height="18" rx="2" ry="2"/>
                  <circle cx="8.5" cy="8.5" r="1.5"/>
                  <path d="m21 15-5-5L5 21"/>
                </svg>
                <span>Color de la imagen</span>
              </div>
              <p class="image-color-note">Este producto se muestra con el color de la imagen</p>
            </div>

            <!-- Estado del producto -->
            <div class="modal-status">
              <span :class="['status-badge', getStatusClass(selectedProduct?.status || '')]">
                {{ getStatusText(selectedProduct?.status || '') }}
              </span>
            </div>

            <!-- Botón agregar al carrito -->
            <button
              @click="addToCartFromModal"
              :disabled="selectedProduct?.status !== 'available' || (selectedProduct?.colors && selectedProduct.colors.length > 0 && !modalSelectedColor)"
              class="modal-add-to-cart"
            >
              {{ selectedProduct?.status === 'available' ? 'Agregar al carrito' : 'No disponible' }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Botón del carrito flotante -->
    <div v-if="totalItems > 0" class="floating-cart" @click="toggleCart">
      <div class="cart-icon">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="m5 7 1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2l1-12"/>
          <path d="M22 7H2"/>
          <path d="m9 3 2-2 2 2"/>
        </svg>
        <span class="cart-badge">{{ totalItems }}</span>
      </div>
      <div class="cart-tooltip">Ver carrito de compras</div>
    </div>

    <!-- Modal del carrito -->
    <div v-if="isCartOpen" class="cart-overlay" @click="closeCart">
      <div class="cart-modal" @click.stop>
        <div class="cart-header">
          <h3>Tu Carrito</h3>
          <button @click="closeCart" class="close-btn">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="m18 6-12 12"/>
              <path d="m6 6 12 12"/>
            </svg>
          </button>
        </div>

        <div class="cart-content">
          <div v-if="cartItems.length === 0" class="empty-cart">
            <p>Tu carrito está vacío</p>
          </div>

          <div v-else class="cart-items">
            <div
              v-for="item in cartItems"
              :key="item.id"
              class="cart-item"
            >
              <img :src="item.image" :alt="item.name" />
              <div class="item-details">
                <h4>{{ item.name }}</h4>
                <span class="item-category">{{ item.category }}</span>
                <span v-if="item.selectedColor" class="item-color">Color: {{ item.selectedColor }}</span>
                <div class="item-price">${{ item.price.toLocaleString() }}</div>
              </div>
              <div class="item-controls">
                <div class="quantity-controls">
                  <button @click="updateQuantity(item.id, item.quantity - 1, item.selectedColor)" class="quantity-btn minus">-</button>
                  <span>{{ item.quantity }}</span>
                  <button @click="updateQuantity(item.id, item.quantity + 1, item.selectedColor)" class="quantity-btn plus">+</button>
                </div>
                <button @click="removeFromCart(item.id, item.selectedColor)" class="remove-btn">
                  <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M3 6h18"/>
                    <path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/>
                    <path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>

        <div v-if="cartItems.length > 0" class="cart-footer">
          <div class="cart-total-display">
            <strong>Total: ${{ totalPrice.toLocaleString() }}</strong>
          </div>
          <div class="cart-actions">
            <button @click="clearCart" class="btn-clear">
              Limpiar carrito
            </button>
            <button @click="goToCheckout" class="btn-checkout">
              Finalizar Pedido
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useProducts } from '@/composables/useProducts'
import { useCategories } from '@/composables/useCategories'
import { useCart } from '@/composables/useCart'
import type { Product } from '@/composables/useProducts'
import { useRouter } from 'vue-router'
import { useHead } from '@vueuse/head'

useHead({
  title: 'Ofertas - Los Mejores Precios en Todos los Productos | SOYDANI',
  meta: [
    {
      name: 'description',
      content: 'Aprovecha las mejores ofertas y descuentos especiales. Desde tecnología hasta hogar, decoración navideña y más. ¡Ofertas por tiempo limitado en SOYDANI!'
    },
    { property: 'og:title', content: 'Ofertas - Los Mejores Precios | SOYDANI' },
    { property: 'og:description', content: 'Descuentos especiales en todas las categorías. Ofertas por tiempo limitado en SOYDANI.' },
    { property: 'og:image', content: 'https://www.soydani.com/images/ofertas.jpg' },
    { property: 'og:url', content: 'https://www.soydani.com/ofertas' },
    { name: 'twitter:card', content: 'summary_large_image' },
    { name: 'twitter:image', content: 'https://www.soydani.com/images/ofertas.jpg' }
  ]
})


const router = useRouter()
const { regularProducts, loadProducts, showcaseProducts, loadShowcaseProducts } = useProducts()
const { categories, loadCategories } = useCategories()
const {
  cartItems,
  isCartOpen,
  totalItems,
  totalPrice,
  addToCart,
  removeFromCart,
  updateQuantity,
  clearCart,
  toggleCart,
  closeCart
} = useCart()

const isLoadingProducts = ref(true)
const currentSlide = ref(0)
let autoplayInterval: number | null = null

// Estados para el modal
const showModal = ref(false)
const selectedProduct = ref<Product | null>(null)
const modalSelectedColor = ref('')
const selectedImageIndex = ref(0)

// Banners de ofertas con imágenes llamativas
const ofertasBanners = [
  {
    title: '¡Mega Ofertas!',
    subtitle: 'Hasta 50% de descuento en productos seleccionados',
    image: 'https://images.unsplash.com/photo-1607082348824-0a96f2a4b9da?w=800&q=80'
  },
  {
    title: 'Descuentos Increíbles',
    subtitle: 'Ahorra en grande con nuestras promociones especiales',
    image: 'https://images.unsplash.com/photo-1607083206968-13611e3d76db?w=800&q=80'
  },
  {
    title: 'Ofertas Exclusivas',
    subtitle: 'Los mejores precios en tecnología y más',
    image: 'https://images.unsplash.com/photo-1607082349566-187342175e2f?w=800&q=80'
  }
]

// Estados para los filtros
const sortBy = ref('price-desc')
const filterStatus = ref('all')
const minPrice = ref<number | null>(null)
const maxPrice = ref<number | null>(null)

// Filtrar productos regulares en oferta (con descuento)
const ofertasProducts = computed(() => {
  return regularProducts.value
    .filter(p => {
      const hasDiscount = p.originalPrice && p.originalPrice > p.price
      const statusMatch = p.status === 'available' || p.status === 'coming-soon'
      return hasDiscount && statusMatch
    })
})

// Productos filtrados y ordenados
const filteredAndSortedProducts = computed(() => {
  let filtered = [...ofertasProducts.value]

  // Filtrar por estado
  if (filterStatus.value !== 'all') {
    filtered = filtered.filter(p => p.status === filterStatus.value)
  }

  // Filtrar por rango de precio
  if (minPrice.value !== null && minPrice.value > 0) {
    filtered = filtered.filter(p => p.price >= minPrice.value!)
  }
  if (maxPrice.value !== null && maxPrice.value > 0) {
    filtered = filtered.filter(p => p.price <= maxPrice.value!)
  }

  // Ordenar
  if (sortBy.value === 'price-asc') {
    filtered.sort((a, b) => a.price - b.price)
  } else if (sortBy.value === 'price-desc') {
    filtered.sort((a, b) => b.price - a.price)
  } else if (sortBy.value === 'name-asc') {
    filtered.sort((a, b) => a.name.localeCompare(b.name))
  } else if (sortBy.value === 'name-desc') {
    filtered.sort((a, b) => b.name.localeCompare(a.name))
  }

  return filtered
})

// Limpiar filtros
const clearFilters = () => {
  sortBy.value = 'price-desc'
  filterStatus.value = 'all'
  minPrice.value = null
  maxPrice.value = null
}

const prevSlide = () => { const total = ofertasBanners.length; currentSlide.value = (currentSlide.value - 1 + total) % total }
const nextSlide = () => { const total = ofertasBanners.length; currentSlide.value = (currentSlide.value + 1) % total }
const goToSlide = (i: number) => { currentSlide.value = i }

const startAutoplay = () => { autoplayInterval = window.setInterval(() => nextSlide(), 5000) }
const stopAutoplay = () => { if (autoplayInterval) { clearInterval(autoplayInterval); autoplayInterval = null } }

const scrollToProducts = () => {
  const el = document.querySelector('.products-section')
  if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

// Funciones del modal
const openProductModal = (product: Product) => {
  selectedProduct.value = product
  // Si el producto no tiene colores seleccionables, indicar que es color de imagen
  if (!product.colors || product.colors.length === 0) {
    modalSelectedColor.value = 'COLOR_DE_IMAGEN'
  } else {
    modalSelectedColor.value = ''
  }
  selectedImageIndex.value = 0
  showModal.value = true
  // Bloquear scroll del body completamente
  const scrollY = window.scrollY
  document.body.style.position = 'fixed'
  document.body.style.top = `-${scrollY}px`
  document.body.style.width = '100%'
  document.body.style.overflow = 'hidden'
}

const closeModal = () => {
  showModal.value = false
  selectedProduct.value = null
  modalSelectedColor.value = ''
  selectedImageIndex.value = 0
  // Restaurar scroll del body
  const scrollY = document.body.style.top
  document.body.style.position = ''
  document.body.style.top = ''
  document.body.style.width = ''
  document.body.style.overflow = ''
  window.scrollTo(0, parseInt(scrollY || '0') * -1)
}

// Navegación entre imágenes
const nextImage = () => {
  if (selectedProduct.value && selectedImageIndex.value < selectedProduct.value.images.length - 1) {
    selectedImageIndex.value++
  }
}

const previousImage = () => {
  if (selectedImageIndex.value > 0) {
    selectedImageIndex.value--
  }
}

// Colores de Apple predeterminados con paleta extendida
const appleColors: Record<string, string> = {
  // Colores primarios
  'Rojo': '#FF0000',
  'Azul': '#0000FF',
  'Amarillo': '#FFFF00',
  'Verde': '#00FF00',
  'Naranja': '#FFA500',
  'Morado': '#800080',
  'Rosa': '#FFC0CB',
  'Negro': '#000000',
  'Blanco': '#FFFFFF',
  'Gris': '#808080',
  // Variantes de azul
  'Azul Claro': '#ADD8E6',
  'Azul Cielo': '#87CEEB',
  'Azul Profundo': '#003d5c',
  'Azul Marino': '#000080',
  'Azul Medianoche': '#191970',
  'Azul Neblina': '#a8c7dd',
  // Variantes de verde
  'Verde Claro': '#90EE90',
  'Verde Alpino': '#0D7C66',
  'Verde Bosque': '#228B22',
  // Variantes de rojo/rosa
  'Rojo Oscuro': '#8B0000',
  'Rosa Claro': '#FFB6C1',
  'Rosa Fuerte': '#FF69B4',
  // Metálicos y neutros
  'Plata': '#C0C0C0',
  'Oro': '#FFD700',
  'Dorado Claro': '#f7e7a1',
  'Titanio': '#878681',
  'Grafito': '#383838',
  // Otros
  'Púrpura': '#9C27B0',
  'Naranja Cósmico': '#ff5e00',
  'Crema': '#FFFDD0',
  'Beige': '#F5F5DC'
}

// Normaliza nombres de colores eliminando tildes y convirtiendo a lowercase
const getColorHex = (colorName: string): string => {
  if (!colorName) return '#cccccc'

  // Función para normalizar string (remover tildes y espacios extra)
  const normalize = (str: string) => {
    return str.toLowerCase()
      .normalize('NFD')
      .replace(/[\u0300-\u036f]/g, '')
      .trim()
  }

  const normalizedInput = normalize(colorName)

  // Buscar en el objeto de colores normalizando cada clave
  for (const [colorKey, hexValue] of Object.entries(appleColors)) {
    if (normalize(colorKey) === normalizedInput) {
      return hexValue
    }
  }

  return '#cccccc' // Color por defecto si no se encuentra
}

// Función para obtener el texto del estado del producto
const getStatusText = (status: string) => {
  switch (status) {
    case 'available':
      return 'Disponible'
    case 'coming-soon':
      return 'Próximamente'
    case 'out-of-stock':
      return 'Agotado'
    default:
      return 'No disponible'
  }
}

// Función para obtener la clase CSS del estado
const getStatusClass = (status: string) => {
  switch (status) {
    case 'available':
      return 'status-available'
    case 'coming-soon':
      return 'status-coming-soon'
    case 'out-of-stock':
      return 'status-out-of-stock'
    default:
      return 'status-unavailable'
  }
}

// Función para agregar al carrito desde el modal
const addToCartFromModal = () => {
  if (selectedProduct.value) {
    if (selectedProduct.value.colors && selectedProduct.value.colors.length > 0 && !modalSelectedColor.value) {
      return
    }

    const categoryName = categories.value.find(cat => cat.id === selectedProduct.value!.category)?.name || 'AirPods'

    const mappedProduct = {
      ...selectedProduct.value,
      inStock: selectedProduct.value.status === 'available',
      image: selectedProduct.value.images[0],
      category: categoryName
    }

    // Pasar el color seleccionado como tercer parámetro
    addToCart(mappedProduct, 1, modalSelectedColor.value)
    closeModal()
  }
}

// Función ahora abre el modal
const handleAddToCart = (product: Product) => {
  openProductModal(product)
}

// Función para ir al checkout
const goToCheckout = () => {
  closeCart()
  router.push('/checkout')
}

onMounted(async () => {
  try {
    isLoadingProducts.value = true
    // Primero cargamos las categorías
    if (categories.value.length === 0) {
      await loadCategories()
    }
    // Luego cargamos los productos en paralelo
    await Promise.all([
      loadProducts(),
      loadShowcaseProducts()
    ])
    startAutoplay()
  } catch (e) {
    console.error('Error cargando datos:', e)
  } finally {
    isLoadingProducts.value = false
  }
})

onUnmounted(() => stopAutoplay())
</script>

<style scoped>
/* === TEMA OSCURO === */
.ofertas-page {
  min-height: 100vh;
  background: #000000;
  color: #f5f5f7;
}

/* === CARRUSEL === */
.hero-banner {
  position: relative;
  width: 100%;
  height: 90vh;
  min-height: 700px;
  max-height: 1000px;
  overflow: hidden;
  background: #0a0a0a;
}

.carousel-container {
  position: relative;
  width: 100%;
  height: 100%;
}

.carousel-track {
  display: flex;
  height: 100%;
  transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

.carousel-slide {
  min-width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #1a1a1a 0%, #0a0a0a 100%);
}

.slide-content {
  max-width: 1400px;
  width: 100%;
  display: grid;
  grid-template-columns: 40% 60%;
  gap: 40px;
  align-items: center;
  padding: 0 80px;
  height: 100%;
}

.slide-text {
  z-index: 2;
  padding-right: 20px;
}

.slide-label {
  display: inline-block;
  color: #ff9500;
  font-size: 1rem;
  font-weight: 600;
  letter-spacing: 2.5px;
  text-transform: uppercase;
  margin-bottom: 24px;
  animation: fadeInUp 0.6s ease-out;
}

.slide-title {
  font-size: 4.0rem;
  font-weight: 700;
  margin: 0 0 24px;
  color: #ffffff;
  letter-spacing: -3px;
  line-height: 0.95;
  animation: fadeInUp 0.6s ease-out 0.1s both;
}

.slide-subtitle {
  font-size: 1.3rem;
  font-weight: 300;
  margin: 0 0 24px;
  color: #f5f5f7;
  animation: fadeInUp 0.6s ease-out 0.2s both;
}

.shop-now-btn {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 14px 32px;
  background: #0a84ff;
  color: white;
  border: none;
  border-radius: 980px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 30px;
  animation: fadeInUp 0.6s ease-out 0.4s both;
}

.shop-now-btn:hover {
  background: #0077ed;
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(10, 132, 255, 0.4);
}

.shop-now-btn svg {
  transition: transform 0.3s ease;
}

.shop-now-btn:hover svg {
  transform: translateY(3px);
  animation: bounce 1s infinite;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(5px); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.slide-description {
  font-size: 1.0rem;
  color: #a1a1a6;
  line-height: 1.7;
  max-width: 480px;
  animation: fadeInUp 0.6s ease-out 0.3s both;
}

.slide-image {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  position: relative;
  animation: fadeInScale 0.8s ease-out 0.4s both;
}

.slide-image img {
  width: 100%;
  max-width: 1000px;
  height: auto;
  max-height: 450px;
  object-fit: contain;
  filter: drop-shadow(0 30px 80px rgba(0, 0, 0, 0.6));
  transition: transform 0.5s ease;

  /* Bordes profesionales */
  border-radius: 30px;
  border: 3px solid rgba(255, 255, 255, 0.1);
  padding: 20px;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
  backdrop-filter: blur(10px);
  box-shadow:
    0 30px 80px rgba(0, 0, 0, 0.6),
    inset 0 1px 1px rgba(255, 255, 255, 0.1),
    0 0 0 1px rgba(255, 255, 255, 0.05);
}

/* Controles del carrusel */
.carousel-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: #ffffff;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.carousel-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateY(-50%) scale(1.1);
}

.carousel-btn.prev {
  left: 30px;
}

.carousel-btn.next {
  right: 30px;
}

/* Indicadores */
.carousel-indicators {
  position: absolute;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 12px;
  z-index: 10;
}

.indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.4);
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
  padding: 0;
}

.indicator.active {
  width: 24px;
  border-radius: 4px;
  background: #ffffff;
}

.indicator:hover {
  background: rgba(255, 255, 255, 0.7);
}

/* Efecto hover en imagen */
.carousel-slide:hover .slide-image img {
  transform: scale(1.03);
}

/* Animaciones */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInRight {
  from {
    opacity: 0;
    transform: translateX(-30px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes fadeInScale {
  from {
    opacity: 0;
    transform: scale(0.9) translateX(30px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateX(0);
  }
}

/* === CONTAINER === */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* === PRODUCTOS SECTION === */
.products-section {
  padding: 100px 0;
  background: #0a0a0a;
}

@media (max-width: 480px) {
  .products-section {
    padding: 10px 0;
  }
}

.filters-bar {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.08), rgba(255, 255, 255, 0.03));
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 1.75rem 2rem;
  margin-bottom: 3rem;
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

.filters-group {
  display: flex;
  flex-wrap: wrap;
  gap: 1.25rem;
  align-items: flex-end;
  margin-bottom: 1.5rem;
}

.filter-item {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  flex: 0 1 auto;
  min-width: 180px;
}

.filter-item.price-range {
  flex: 1 1 280px;
}

.filter-item label {
  font-size: 0.875rem;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.9);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0;
}

.filter-select,
.price-input {
  padding: 0.75rem;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  background: rgba(0, 0, 0, 0.3);
  color: #fff;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  outline: none;
}

.filter-select:focus,
.price-input:focus {
  border-color: #0071e3;
  box-shadow: 0 0 0 3px rgba(0, 113, 227, 0.1);
}

.filter-select option {
  background: #1a1a1a;
  color: #fff;
}

.price-inputs {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.price-inputs span {
  color: rgba(255, 255, 255, 0.6);
}

.price-input[type="number"]::-webkit-inner-spin-button,
.price-input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.results-count {
  font-size: 0.875rem;
  color: rgba(255, 255, 255, 0.7);
  font-weight: 500;
  flex: 1;
  position: fixed;
  top: 1.4rem;
}

.clear-filters-btn {
  padding: 0.75rem 1rem;
  background: linear-gradient(135deg, rgba(0, 113, 227, 0.15), rgba(0, 113, 227, 0.08));
  border: 1px solid rgba(0, 113, 227, 0.4);
  color: #0071e3;
  border-radius: 10px;
  font-size: 0.875rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  white-space: nowrap;
}

.clear-filters-btn:hover {
  background: linear-gradient(135deg, #0071e3, #005bb5);
  color: #fff;
  border-color: #0071e3;
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 113, 227, 0.4);
}

@media (max-width: 768px) {
  .filters-bar {
    padding: 1rem 0.75rem;
    border-radius: 12px;
    margin-bottom: 1.5rem;
    width: 95%;
    max-width: 95%;
    margin-left: auto;
    margin-right: auto;
  }

  .filters-group {
    flex-direction: column;
    gap: 0.875rem;
    margin-bottom: 0.875rem;
    width: 100%;
  }

  .filter-item {
    width: 100%;
    min-width: unset;
    max-width: 100%;
  }

  .filter-item.price-range {
    flex: 1 1 100%;
  }

  .price-inputs {
    flex-direction: column;
    gap: 0.5rem;
  }

  .price-inputs span {
    display: none;
  }

  .results-count {
    position: static;
    font-size: 0.8rem;
  }

  .clear-filters-btn {
    padding: 0.625rem 1rem;
    font-size: 0.8rem;
    width: auto;
  }
}

.products-container {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.product-card-modern {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 60px;
  align-items: center;
  padding: 80px 40px;
  border-bottom: 1px solid #2a2a2a;
  cursor: pointer;
  transition: all 0.3s ease;
  background: transparent;
}

.product-card-modern:hover {
  background: rgba(0, 0, 0, 0.3);
  border-bottom-color: rgba(0, 113, 227, 0.4);
  transform: translateX(8px);
}

.product-card-modern:nth-child(even) .product-visual {
  order: 2;
}

.product-card-modern:nth-child(even) .product-details {
  order: 1;
}

.product-visual {
  display: flex;
  align-items: center;
  justify-content: center;
}

.product-image {
  width: 100%;
  max-width: 400px;
  height: auto;
  object-fit: contain;
  transition: all 0.4s ease;

  /* Bordes visuales y profesionales */
  border-radius: 24px;
  border: 4px solid rgba(255, 255, 255, 0.2);
  padding: 20px;
  background: linear-gradient(145deg,
    rgba(255, 255, 255, 0.1),
    rgba(255, 255, 255, 0.05));
  backdrop-filter: blur(15px);
  box-shadow:
    /* Sombra principal */
    0 20px 50px rgba(0, 0, 0, 0.6),
    /* Anillo exterior */
    0 0 0 1px rgba(255, 255, 255, 0.25),
    /* Anillo azul Apple */
    0 0 0 2px rgba(10, 132, 255, 0.15),
    /* Brillo interno superior */
    inset 0 1px 2px rgba(255, 255, 255, 0.15),
    /* Sombra interna inferior */
    inset 0 -1px 2px rgba(0, 0, 0, 0.1);
}

.product-card-modern:hover .product-image {
  transform: scale(1.05);
  border-color: rgba(255, 255, 255, 0.35);
  box-shadow:
    /* Sombra más dramática */
    0 25px 60px rgba(0, 0, 0, 0.7),
    /* Anillo exterior brillante */
    0 0 0 1px rgba(255, 255, 255, 0.4),
    /* Anillo azul más visible */
    0 0 0 3px rgba(10, 132, 255, 0.3),
    /* Glow azul */
    0 0 30px rgba(10, 132, 255, 0.2),
    /* Brillos internos intensificados */
    inset 0 2px 4px rgba(255, 255, 255, 0.2),
    inset 0 -2px 4px rgba(0, 0, 0, 0.15);
}

.product-details {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.product-label {
  display: inline-block;
  color: #ff9500;
  font-size: 0.9rem;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.product-title {
  font-size: 3rem;
  font-weight: 700;
  margin: 0;
  color: #ffffff;
  line-height: 1.1;
}

.product-tagline {
  font-size: 1.3rem;
  color: #a1a1a6;
  margin: 0;
  line-height: 1.4;
}

.product-pricing {
  display: flex;
  align-items: baseline;
  gap: 10px;
  margin-top: 10px;
}

.product-price {
  font-size: 1.1rem;
  color: #f5f5f7;
  font-weight: 400;
}

.product-price-original {
  font-size: 0.95rem;
  color: #6e6e73;
  text-decoration: line-through;
}

.product-meta {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 5px;
}

.product-purchases {
  font-size: 0.9rem;
  color: #0a84ff;
  font-weight: 500;
}

.product-btn {
  width: fit-content;
  padding: 12px 28px;
  background: #0a84ff;
  color: white;
  border: none;
  border-radius: 980px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 10px;
}

.product-btn:hover {
  background: #0077ed;
  transform: scale(1.02);
}

/* === SECCIÓN INFORMATIVA SIMPLE === */
.info-section {
  padding: 80px 0;
  background: #0a0a0a;
}

.info-content-centered {
  max-width: 900px;
  margin: 0 auto;
  text-align: center;
  padding: 0 20px;
}

.info-title {
  font-size: 3rem;
  font-weight: 700;
  color: #ffffff;
  margin: 0 0 30px;
  letter-spacing: -1.5px;
}

.info-text {
  font-size: 1.3rem;
  line-height: 1.8;
  color: #a1a1a6;
  margin: 0;
  font-weight: 400;
}

/* === LOADING & EMPTY STATES === */
.loading-state,
.empty-state {
  text-align: center;
  padding: 80px 20px;
  color: #6e6e73;
}

.spinner {
  width: 50px;
  height: 50px;
  border: 3px solid rgba(10, 132, 255, 0.2);
  border-top-color: #0a84ff;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin: 0 auto 20px;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* === MODAL DEL PRODUCTO === */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgb(0, 0, 0);
  backdrop-filter: blur(10px);
  z-index: 999999;
  display: flex;
  align-items: center;
  justify-content: center;
  animation: fadeIn 0.3s ease-out;
  padding: 20px;
  overflow: hidden;
}

.modal-content {
  background: #1d1d1f;
  border-radius: 20px;
  max-width: 1000px;
  width: 100%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  animation: slideUp 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid rgba(255, 255, 255, 0.1);
  margin: auto;
}

.modal-close.floating {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(0, 0, 0, 0.6);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: #f5f5f7;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;
  z-index: 10;
  backdrop-filter: blur(10px);
}

.modal-close.floating:hover {
  background: rgba(0, 0, 0, 0.8);
  transform: rotate(90deg);
}

.modal-body {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  padding: 40px;
}

.modal-image-container {
  position: relative;
  width: 100%;
  height: 100%;
}

.modal-image {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-image img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.03);
}

.image-navigation {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 0 16px;
  pointer-events: none;
}

.nav-btn {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  border: none;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(12px);
  color: white;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: auto;
}

.nav-btn:hover:not(:disabled) {
  background: rgba(0, 0, 0, 0.8);
  transform: scale(1.1);
}

.nav-btn:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.image-indicators {
  position: absolute;
  bottom: 16px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 8px;
  z-index: 10;
}

.indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  border: none;
  background: rgba(255, 255, 255, 0.4);
  cursor: pointer;
  transition: all 0.3s;
  padding: 0;
}

.indicator:hover {
  background: rgba(255, 255, 255, 0.6);
}

.indicator.active {
  background: #0071e3;
  width: 24px;
  border-radius: 4px;
}

.modal-info {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.modal-title {
  font-size: 32px;
  font-weight: 800;
  margin: 0;
  color: #f5f5f7;
  letter-spacing: -0.5px;
}

.modal-category {
  color: #86868b;
  font-size: 14px;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.modal-description {
  color: #a1a1a6;
  font-size: 16px;
  line-height: 1.6;
  margin: 0;
}

.modal-price {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 10px;
}

.modal-current-price {
  font-size: 32px;
  font-weight: 700;
  color: #0071e3;
}

.modal-original-price {
  font-size: 20px;
  color: #86868b;
  text-decoration: line-through;
}

.modal-colors {
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  padding: 20px 0;
}

.modal-colors h4 {
  margin: 0 0 16px 0;
  font-size: 16px;
  font-weight: 600;
  color: #f5f5f7;
}

.color-options {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
  gap: 16px;
}

.color-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  padding: 12px;
  border-radius: 12px;
  transition: all 0.3s;
  background: rgba(255, 255, 255, 0.03);
  border: 2px solid transparent;
}

.color-option:hover {
  background: rgba(255, 255, 255, 0.08);
  transform: translateY(-2px);
}

.color-option.active {
  border-color: #0071e3;
  background: rgba(0, 113, 227, 0.1);
}

.color-option.active .color-circle {
  transform: scale(1.15);
  box-shadow: 0 4px 16px rgba(0, 113, 227, 0.5);
}

.color-circle {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  transition: all 0.3s;
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.3), inset 0 1px 3px rgba(255, 255, 255, 0.2);
  border: 3px solid rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.check-icon {
  position: absolute;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.5));
}

.color-name {
  font-size: 13px;
  color: #f5f5f7;
  text-align: center;
  font-weight: 500;
  max-width: 100%;
  word-break: break-word;
}

.color-image-indicator {
  padding: 16px;
  background: rgba(255, 255, 255, 0.03);
  border-radius: 12px;
  border: 2px solid rgba(255, 255, 255, 0.1);
}

.image-color-badge {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  background: linear-gradient(135deg, rgba(0, 113, 227, 0.15), rgba(0, 199, 190, 0.15));
  border-radius: 8px;
  border: 1px solid rgba(0, 113, 227, 0.3);
  margin-bottom: 12px;
}

.image-color-badge svg {
  color: #0071e3;
  flex-shrink: 0;
}

.image-color-badge span {
  font-size: 15px;
  font-weight: 600;
  color: #f5f5f7;
  letter-spacing: 0.3px;
}

.image-color-note {
  margin: 0;
  font-size: 13px;
  color: #a1a1a6;
  line-height: 1.5;
  padding-left: 4px;
}

.modal-status {
  display: flex;
  gap: 8px;
}

.status-badge {
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.status-available {
  background: rgba(52, 199, 89, 0.15);
  color: #34c759;
  border: 1px solid rgba(52, 199, 89, 0.3);
}

.status-coming-soon {
  background: rgba(255, 204, 0, 0.15);
  color: #ffcc00;
  border: 1px solid rgba(255, 204, 0, 0.3);
}

.status-out-of-stock {
  background: rgba(255, 59, 48, 0.15);
  color: #ff3b30;
  border: 1px solid rgba(255, 59, 48, 0.3);
}

.status-unavailable {
  background: rgba(142, 142, 147, 0.15);
  color: #8e8e93;
  border: 1px solid rgba(142, 142, 147, 0.3);
}

.modal-add-to-cart {
  background: linear-gradient(135deg, #0071e3 0%, #0051a8 100%);
  color: white;
  border: none;
  padding: 16px 32px;
  border-radius: 12px;
  font-size: 18px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  box-shadow: 0 4px 15px rgba(0, 113, 227, 0.3);
  margin-top: auto;
}

.modal-add-to-cart:hover:not(:disabled) {
  box-shadow: 0 6px 20px rgba(0, 113, 227, 0.4);
  transform: translateY(-2px);
}

.modal-add-to-cart:disabled {
  background: rgba(142, 142, 147, 0.2);
  color: #8e8e93;
  cursor: not-allowed;
  box-shadow: none;
}

/* === CARRITO FLOTANTE === */
.floating-cart {
  position: fixed;
  bottom: 500px;
  right: 20px;
  background: var(--brand-success);
  color: white;
  padding: 1rem;
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3);
  transition: all 0.3s ease;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
}

.floating-cart:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 40px rgba(16, 185, 129, 0.4);
}

.floating-cart:hover .cart-tooltip {
  opacity: 1;
  visibility: visible;
}

.cart-icon {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cart-badge {
  position: absolute;
  top: -8px;
  right: -8px;
  background: var(--brand-danger);
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  font-size: 0.8rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
}

.cart-tooltip {
  position: absolute;
  right: 70px;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(0, 0, 0, 0.8);
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  font-size: 0.85rem;
  white-space: nowrap;
  opacity: 0;
  visibility: hidden;
  transition: all 0.3s ease;
  pointer-events: none;
  z-index: 1001;
}

.cart-tooltip::after {
  content: '';
  position: absolute;
  left: 100%;
  top: 50%;
  transform: translateY(-50%);
  border: 5px solid transparent;
  border-left-color: rgba(0, 0, 0, 0.8);
}

/* === MODAL DEL CARRITO === */
.cart-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  z-index: 2000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
  backdrop-filter: blur(8px);
}

.cart-modal {
  background: var(--brand-surface);
  border-radius: 20px;
  max-width: 900px;
  width: 100%;
  max-height: 85vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  border: 1px solid var(--brand-border);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6);
}

.cart-header {
  padding: 2rem 2rem 1rem;
  border-bottom: 1px solid var(--brand-border);
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: var(--brand-surface);
}

.cart-header h3 {
  margin: 0;
  font-size: 1.5rem;
  color: var(--brand-primary-contrast);
}

.close-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 50%;
  transition: background 0.3s ease;
  color: var(--brand-primary-contrast);
}

.close-btn:hover {
  background: var(--brand-border);
}

.cart-content {
  flex: 1;
  overflow-y: auto;
  padding: 1rem 2rem;
  background: var(--brand-surface);
}

.empty-cart {
  text-align: center;
  padding: 3rem 0;
  color: rgba(255, 255, 255, 0.6);
}

.empty-cart p {
  font-size: 18px;
  margin: 0;
}

.cart-items {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.cart-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  background: var(--brand-bg-end);
  border-radius: 15px;
  border: 1px solid var(--brand-border);
}

.cart-item img {
  width: 80px;
  height: 80px;
  object-fit: cover;
  border-radius: 10px;
}

.item-details {
  flex: 1;
}

.item-details h4 {
  margin: 0 0 0.25rem;
  color: var(--brand-primary-contrast);
}

.item-category {
  font-size: 0.9rem;
  color: var(--brand-success);
  font-weight: 600;
}

.item-color {
  font-size: 0.8rem;
  color: rgba(255, 255, 255, 0.8);
  background: var(--brand-border);
  padding: 0.2rem 0.6rem;
  border-radius: 12px;
  display: inline-block;
  margin-top: 0.3rem;
  font-weight: 500;
}

.item-price {
  font-weight: 700;
  color: var(--brand-success);
  margin-top: 0.5rem;
}

.item-controls {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.quantity-controls {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.quantity-btn {
  background: var(--brand-success);
  color: white;
  border: none;
  border-radius: 8px;
  width: 32px;
  height: 32px;
  cursor: pointer;
  font-weight: 700;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quantity-btn:hover {
  background: #0d9466;
  transform: scale(1.1);
}

.quantity-controls span {
  min-width: 30px;
  text-align: center;
  font-weight: 600;
  color: var(--brand-primary-contrast);
}

.remove-btn {
  background: var(--brand-danger);
  color: white;
  border: none;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.remove-btn:hover {
  background: #dc2626;
  transform: scale(1.1);
}

.cart-footer {
  padding: 1rem 2rem 2rem;
  border-top: 1px solid var(--brand-border);
  background: var(--brand-surface);
}

.cart-total-display {
  text-align: center;
  margin-bottom: 1.5rem;
  font-size: 1.3rem;
  color: var(--brand-primary-contrast);
  font-weight: 700;
}

.cart-actions {
  display: flex;
  gap: 1rem;
}

.btn-clear,
.btn-checkout {
  flex: 1;
  padding: 0.875rem;
  border: none;
  border-radius: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-clear {
  background: var(--brand-border);
  color: rgba(255, 255, 255, 0.8);
}

.btn-clear:hover {
  background: var(--brand-bg-end);
  color: var(--brand-primary-contrast);
}

.btn-checkout {
  background: var(--brand-success);
  color: white;
}

.btn-checkout:hover {
  background: #0d9466;
  transform: translateY(-2px);
}

@media (max-width: 768px) {
  .floating-cart {
    bottom: 350px;
    right: 8px;
    padding: 0.75rem;
    width: 55px;
    height: 55px;
  }

  .cart-tooltip {
    display: none;
  }

  .floating-cart:hover .cart-tooltip,
  .floating-cart:active .cart-tooltip {
    display: block;
  }

  .products-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .cart-overlay {
    padding: 0.5rem;
  }

  .cart-modal {
    width: 95%;
    max-height: 95vh;
    max-width: none;
    margin: 0;
    border-radius: 15px;
  }

  .cart-header {
    padding: 1.5rem 1rem 1rem;
  }

  .cart-header h3 {
    font-size: 1.3rem;
  }

  .cart-content {
    padding: 1rem;
  }

  .cart-item {
    padding: 0.75rem;
    gap: 0.75rem;
  }

  .cart-item img {
    width: 60px;
    height: 60px;
  }

  .item-details h4 {
    font-size: 0.9rem;
    margin-bottom: 0.2rem;
  }

  .item-category {
    font-size: 0.8rem;
  }

  .item-color {
    font-size: 0.7rem;
    padding: 0.15rem 0.4rem;
  }

  .item-price {
    font-size: 0.9rem;
    margin-top: 0.3rem;
  }

  .cart-footer {
    padding: 1rem;
  }

  .cart-total-display {
    font-size: 1.1rem;
    margin-bottom: 1rem;
  }

  .cart-actions {
    flex-direction: column;
  }

  .quantity-controls {
    gap: 0.5rem;
    padding: 0.5rem;
  }

  .quantity-btn {
    width: 32px;
    height: 32px;
    border-radius: 8px;
    font-size: 0.9rem;
  }

  .quantity-controls span {
    font-size: 0.9rem;
    padding: 0.3rem;
    min-width: 24px;
  }
}

/* Responsive para modal de producto */
@media (max-width: 768px) {
  .modal-overlay {
    padding: 0;
  }

  .modal-content {
    width: 100%;
    max-width: 100%;
    height: 100vh;
    max-height: 100vh;
    border-radius: 0;
    overflow-y: auto;
    margin: 0;
  }

  .modal-body {
    grid-template-columns: 1fr;
    padding: 16px;
    gap: 16px;
    overflow: visible;
  }

  .modal-image {
    border-radius: 12px;
  }

  .modal-image img {
    border-radius: 12px;
  }

  .modal-title {
    font-size: 24px;
  }

  .modal-current-price {
    font-size: 24px;
  }

  .cart-modal {
    max-width: 100%;
  }

  .floating-cart {
    bottom: 350px;
    right: 8px;
  }

  .cart-icon {
    width: 50px;
    height: 50px;
  }
}

/* === RESPONSIVE === */
@media (max-width: 1200px) {
  .slide-content {
    max-width: 100%;
    padding: 0 60px;
  }

  .slide-image img {
    max-width: 550px;
  }
}

@media (max-width: 1024px) {
  .hero-banner {
    height: auto;
    min-height: 600px;
  }

  .slide-content {
    grid-template-columns: 1fr;
    gap: 50px;
    padding: 60px 40px;
  }

  .slide-text {
    padding-right: 0;
    text-align: center;
  }

  .slide-title {
    font-size: 4rem;
  }

  .slide-subtitle {
    font-size: 1.8rem;
  }

  .slide-description {
    max-width: 100%;
    margin: 0 auto;
  }

  .slide-image {
    order: -1;
    height: auto;
    padding: 40px 0;
  }

  .slide-image img {
    max-width: 500px;
    max-height: 500px;
    border-radius: 26px;
    border-width: 2.5px;
    padding: 18px;
  }

  .product-card-modern {
    grid-template-columns: 1fr;
    gap: 40px;
    padding: 60px 30px;
  }

  .product-card-modern:nth-child(even) .product-visual,
  .product-card-modern:nth-child(even) .product-details {
    order: initial;
  }
}

@media (max-width: 768px) {
  .hero-banner {
    min-height: 550px;
  }

  .slide-content {
    padding: 50px 30px;
    gap: 40px;
  }

  .slide-label {
    font-size: 0.85rem;
    margin-bottom: 16px;
  }

  .slide-title {
    font-size: 3rem;
    letter-spacing: -2px;
    margin-bottom: 16px;
  }

  .slide-subtitle {
    font-size: 1.5rem;
    margin-bottom: 16px;
  }

  .slide-description {
    font-size: 1rem;
  }

  .slide-image {
    padding: 30px 0;
  }

  .slide-image img {
    max-width: 380px;
    max-height: 400px;
    border-radius: 24px;
    border-width: 2px;
    padding: 15px;
  }

  .carousel-btn {
    width: 40px;
    height: 40px;
  }

  .carousel-btn.prev {
    left: 15px;
  }

  .carousel-btn.next {
    right: 15px;
  }

  .product-title {
    font-size: 2.2rem;
  }

  .product-tagline {
    font-size: 1.0rem;
  }

  .info-section {
    padding: 50px 0;
  }

  .info-title {
    font-size: 2.2rem;
    margin-bottom: 25px;
  }

  .info-text {
    font-size: 1.1rem;
    line-height: 1.7;
  }
}

@media (max-width: 480px) {
  .hero-banner {
    min-height: 500px;
  }

  .slide-content {
    padding: 80px 20px;
    padding-bottom: 50px;
    gap: 30px;
  }

  .slide-label {
    font-size: 0.75rem;
    margin-bottom: 12px;
  }

  .slide-title {
    font-size: 2.4rem;
    letter-spacing: -1.5px;
    margin-bottom: 12px;
  }

  .slide-subtitle {
    font-size: 1.0rem;
    margin-bottom: 12px;
  }

  .slide-description {
    font-size: 0.95rem;
  }

  .slide-image {
    padding: 20px 0;
  }

  .slide-image img {
    max-width: 280px;
    max-height: 300px;
    border-radius: 20px;
    border-width: 2px;
    padding: 12px;
  }

  .carousel-btn {
    width: 35px;
    height: 35px;
  }

  .carousel-btn.prev {
    left: 10px;
  }

  .carousel-btn.next {
    right: 10px;
  }

  .product-card-modern {
    padding: 40px 20px;
  }

  .product-title {
    font-size: 1.8rem;
  }

  .info-section {
    padding: 40px 0;
  }

  .info-content-centered {
    padding: 0 15px;
  }

  .info-title {
    font-size: 1.8rem;
    margin-bottom: 20px;
    letter-spacing: -1px;
  }

  .info-text {
    font-size: 1rem;
    line-height: 1.6;
  }
}
</style>
