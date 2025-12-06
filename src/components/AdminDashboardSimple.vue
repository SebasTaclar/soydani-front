<template>
  <div class="admin-dashboard">
    <div class="dashboard-header">
      <h1 class="dashboard-title">
        <span class="icon">⚙️</span>
        Panel de Administración - SOYDANI
      </h1>
      <p class="dashboard-subtitle">Gestiona productos, categorías y configuraciones</p>
    </div>

    <!-- Estadísticas rápidas -->
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-icon">📦</div>
        <div class="stat-content">
          <div class="stat-number">{{ products.length }}</div>
          <div class="stat-label">Productos</div>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon">🏷️</div>
        <div class="stat-content">
          <div class="stat-number">{{ categories.length }}</div>
          <div class="stat-label">Categorías</div>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon">✅</div>
        <div class="stat-content">
          <div class="stat-number">{{ availableProductsCount }}</div>
          <div class="stat-label">Disponibles</div>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon">💰</div>
        <div class="stat-content">
          <div class="stat-number">${{ totalValue.toLocaleString() }}</div>
          <div class="stat-label">Valor Total</div>
        </div>
      </div>
    </div>

    <!-- Navegación por pestañas -->
    <div class="tabs-container">
      <div class="tabs">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          type="button"
          :class="['tab', { active: activeTab === tab.id } ]"
          @click="selectTab(tab.id)"
        >
          <span class="tab-icon">{{ tab.icon }}</span>
          {{ tab.name }}
        </button>
      </div>
    </div>

    <!-- Contenido de las pestañas -->
    <div class="tab-content">
      <!-- Pestaña de Productos -->
      <div v-if="activeTab === 'products'" class="content-section">
        <div class="section-header">
          <h2>Gestión de Productos</h2>
          <button class="btn btn-primary" @click="showProductForm = true">
            <span class="btn-icon">➕</span>
            Nuevo Producto
          </button>
        </div>

        <!-- Barra de búsqueda para productos -->
        <div class="search-bar">
          <div class="search-input-wrapper">
            <svg class="search-icon" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
              <path fill="currentColor" d="M10 2a8 8 0 1 1 0 16 8 8 0 0 1 0-16zm8.707 17.293-4.387-4.387a9 9 0 1 0-1.414 1.414l4.387 4.387a1 1 0 0 0 1.414-1.414z"/>
            </svg>
            <input
              type="search"
              v-model="searchProducts"
              placeholder="Buscar productos por nombre..."
              aria-label="Buscar productos"
              class="search-input"
            />
            <button v-if="searchProducts" class="search-clear" @click.prevent="searchProducts = ''" aria-label="Limpiar búsqueda">X</button>
          </div>
        </div>

        <!-- Lista de productos -->
        <div class="products-grid">
          <div v-for="product in filteredProducts" :key="product.id" class="product-card">
            <div class="product-image">
              <img v-if="product.images && product.images.length > 0" :src="product.images[0]" :alt="product.name" />
              <div v-else class="no-image">📷</div>
            </div>
            <div class="product-info">
              <h3>{{ product.name }}</h3>
              <p class="product-description">{{ product.description }}</p>
              <div class="product-meta">
                <span class="price">${{ product.price.toLocaleString() }}</span>
                <span :class="['status', product.status]">{{ getStatusText(product.status) }}</span>
              </div>
              <div class="product-actions">
                <button class="btn btn-sm btn-secondary" @click="editProduct(product)">✏️ Editar</button>
                <button class="btn btn-sm btn-danger" @click="deleteProductConfirm(product.id)">🗑️ Eliminar</button>
              </div>
            </div>
          </div>
        </div>

        <!-- Estado vacío o sin resultados -->
        <div v-if="filteredProducts.length === 0 && !searchProducts" class="empty-state">
          <div class="empty-icon">📦</div>
          <h3>No hay productos</h3>
          <p>Comienza agregando tu primer producto</p>
          <button class="btn btn-primary" @click="showProductForm = true">
            Crear Primer Producto
          </button>
        </div>
        <div v-else-if="filteredProducts.length === 0 && searchProducts" class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>No se encontraron resultados</h3>
          <p>No hay productos que coincidan con "{{ searchProducts }}"</p>
          <button class="btn btn-secondary" @click="searchProducts = ''">
            Limpiar búsqueda
          </button>
        </div>
      </div>

      <!-- Pestaña de Categorías -->
      <div v-if="activeTab === 'categories'" class="content-section">
        <div class="section-header">
          <h2>Gestión de Categorías</h2>
          <button class="btn btn-primary" @click="showCategoryForm = true">
            <span class="btn-icon">➕</span>
            Nueva Categoría
          </button>
        </div>

        <!-- Barra de búsqueda para categorías -->
        <div class="search-bar">
          <div class="search-input-wrapper">
            <svg class="search-icon" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
              <path fill="currentColor" d="M10 2a8 8 0 1 1 0 16 8 8 0 0 1 0-16zm8.707 17.293-4.387-4.387a9 9 0 1 0-1.414 1.414l4.387 4.387a1 1 0 0 0 1.414-1.414z"/>
            </svg>
            <input
              type="search"
              v-model="searchCategories"
              placeholder="Buscar categorías por nombre..."
              aria-label="Buscar categorías"
              class="search-input"
            />
            <button v-if="searchCategories" class="search-clear" @click.prevent="searchCategories = ''" aria-label="Limpiar búsqueda">X</button>
          </div>
        </div>

        <!-- Lista de categorías -->
        <div class="categories-list">
          <div v-for="category in filteredCategories" :key="category.id" class="category-item">
            <div class="category-info">
              <h3>{{ category.name }}</h3>
              <p>{{ category.description }}</p>
              <span class="category-count">{{ getProductsInCategory(category.id) }} productos</span>
            </div>
            <div class="category-actions">
              <button class="btn btn-sm btn-secondary" @click="editCategory(category)">✏️</button>
              <button class="btn btn-sm btn-danger" @click="handleDeleteCategory(category.id)">🗑️</button>
            </div>
          </div>
        </div>

        <!-- Estado vacío o sin resultados -->
        <div v-if="filteredCategories.length === 0 && !searchCategories" class="empty-state">
          <div class="empty-icon">🏷️</div>
          <h3>No hay categorías</h3>
          <p>Crea categorías para organizar tus productos</p>
          <button class="btn btn-primary" @click="showCategoryForm = true">
            Crear Primera Categoría
          </button>
        </div>
        <div v-else-if="filteredCategories.length === 0 && searchCategories" class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>No se encontraron resultados</h3>
          <p>No hay categorías que coincidan con "{{ searchCategories }}"</p>
          <button class="btn btn-secondary" @click="searchCategories = ''">
            Limpiar búsqueda
          </button>
        </div>
      </div>

      <!-- Pestaña de Novedades (ProductShowcase) -->
      <div v-if="activeTab === 'showcase'" class="content-section">
        <div class="section-header">
          <h2>Gestión de Novedades</h2>
          <button class="btn btn-primary" @click="showShowcaseForm = true">
            <span class="btn-icon">✨</span>
            Nueva Novedad
          </button>
        </div>

        <!-- Barra de búsqueda para novedades -->
        <div class="search-bar">
          <div class="search-input-wrapper">
            <svg class="search-icon" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
              <path fill="currentColor" d="M10 2a8 8 0 1 1 0 16 8 8 0 0 1 0-16zm8.707 17.293-4.387-4.387a9 9 0 1 0-1.414 1.414l4.387 4.387a1 1 0 0 0 1.414-1.414z"/>
            </svg>
            <input
              type="search"
              v-model="searchShowcase"
              placeholder="Buscar novedades por nombre..."
              aria-label="Buscar novedades"
              class="search-input"
            />
            <button v-if="searchShowcase" class="search-clear" @click.prevent="searchShowcase = ''" aria-label="Limpiar búsqueda">X</button>
          </div>
        </div>

        <!-- Lista de productos showcase -->
        <div class="showcase-grid">
          <div v-for="product in filteredShowcase" :key="product.id" class="showcase-card">
            <div class="showcase-image">
              <img :src="product.image" :alt="product.name" />
            </div>
            <div class="showcase-info">
              <h3>{{ product.name }}</h3>
              <p class="showcase-description">{{ product.description }}</p>
              <div class="showcase-meta">
                <span class="showcase-category">{{ getCategoryById(product.category)?.name || 'Sin categoría' }}</span>
                <span class="showcase-status available">
                  Disponible
                </span>
              </div>
            </div>
            <div class="showcase-actions">
              <button class="btn btn-sm btn-secondary" @click="editShowcaseProduct(product)">✏️</button>
              <button class="btn btn-sm btn-danger" @click="deleteShowcaseConfirm(product.id)">🗑️</button>
            </div>
          </div>
        </div>

        <!-- Estado vacío o sin resultados -->
        <div v-if="filteredShowcase.length === 0 && !searchShowcase" class="empty-state">
          <div class="empty-icon">✨</div>
          <h3>No hay novedades</h3>
          <p>Agrega productos destacados para mostrar en la sección de novedades</p>
          <button class="btn btn-primary" @click="showShowcaseForm = true">
            Crear Primera Novedad
          </button>
        </div>
        <div v-else-if="filteredShowcase.length === 0 && searchShowcase" class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>No se encontraron resultados</h3>
          <p>No hay novedades que coincidan con "{{ searchShowcase }}"</p>
          <button class="btn btn-secondary" @click="searchShowcase = ''">
            Limpiar búsqueda
          </button>
        </div>
      </div>

      <!-- Pestaña de Resumen de Compras -->
      <div v-if="activeTab === 'sales'" class="content-section">
        <div class="section-header">
          <h2>Resumen de Compras</h2>
          <button @click="loadPurchases" class="btn-secondary" :disabled="isLoadingSales">
            {{ isLoadingSales ? 'Cargando...' : 'Actualizar' }}
          </button>
        </div>

        <!-- Barra de búsqueda para compras -->
        <div v-if="!isLoadingSales && !salesError && sales.length > 0" class="search-bar">
          <div class="search-input-wrapper">
            <svg class="search-icon" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
              <path fill="currentColor" d="M10 2a8 8 0 1 1 0 16 8 8 0 0 1 0-16zm8.707 17.293-4.387-4.387a9 9 0 1 0-1.414 1.414l4.387 4.387a1 1 0 0 0 1.414-1.414z"/>
            </svg>
            <input
              type="search"
              v-model="searchSales"
              placeholder="Buscar por cliente o producto..."
              aria-label="Buscar compras"
              class="search-input"
            />
            <button v-if="searchSales" class="search-clear" @click.prevent="searchSales = ''" aria-label="Limpiar búsqueda">X</button>
          </div>
        </div>

        <!-- Estado de carga -->
        <div v-if="isLoadingSales" class="loading-state">
          <div class="spinner"></div>
          <p>Cargando compras...</p>
        </div>

        <!-- Error -->
        <div v-else-if="salesError" class="error-state">
          <div class="error-icon">⚠️</div>
          <p>{{ salesError }}</p>
          <button @click="loadPurchases" class="btn-primary">Reintentar</button>
        </div>

        <!-- Contenido -->
        <div v-else>
          <!-- Estadísticas de ventas -->
          <div class="sales-stats">
            <div class="stat-card">
              <div class="stat-icon">💰</div>
              <div class="stat-content">
                <div class="stat-number">${{ totalRevenue.toLocaleString() }}</div>
                <div class="stat-label">Ingresos Totales</div>
              </div>
            </div>
            <div class="stat-card">
              <div class="stat-icon">⏳</div>
              <div class="stat-content">
                <div class="stat-number">{{ pendingSales }}</div>
                <div class="stat-label">Ventas Pendientes</div>
              </div>
            </div>
            <div class="stat-card">
              <div class="stat-icon">📈</div>
              <div class="stat-content">
                <div class="stat-number">{{ totalSalesCount }}</div>
                <div class="stat-label">Total Ventas</div>
              </div>
            </div>
          </div>

          <!-- Tabla de ventas -->
          <div class="sales-table-container" v-if="sales.length > 0">
            <table class="sales-table">
              <thead>
                <tr>
                  <th>Cliente</th>
                  <th>Productos</th>
                  <th>Cantidad</th>
                  <th>Total</th>
                  <th>Estado</th>
                  <th>Fecha</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="sale in filteredSales" :key="sale.id" class="sale-row">
                  <td>
                    <div class="customer-info">
                      <div class="customer-name">{{ sale.customerName }}</div>
                      <div class="customer-email">{{ sale.customerEmail }}</div>
                    </div>
                  </td>
                  <td>
                    <div class="product-info">
                      <!-- Un solo producto -->
                      <div v-if="sale.items && sale.items.length === 1" class="single-product">
                        <div class="product-name">{{ sale.items[0].productName }}</div>
                        <div v-if="sale.items[0].selectedColor" class="product-color">
                          <span class="color-dot" :style="{ backgroundColor: getColorHex(sale.items[0].selectedColor) }"></span>
                          {{ sale.items[0].selectedColor }}
                        </div>
                      </div>

                      <!-- Múltiples productos -->
                      <div v-else-if="sale.items && sale.items.length > 1" class="multiple-products">
                        <div class="products-summary">
                          <span class="products-badge">{{ sale.items.length }} productos</span>
                        </div>
                        <details class="products-details">
                          <summary class="products-toggle">Ver detalles</summary>
                          <ul class="products-list">
                            <li v-for="(item, idx) in sale.items" :key="idx" class="product-item">
                              <span class="item-name">{{ item.productName }}</span>
                              <span class="item-quantity">x{{ item.quantity }}</span>
                              <span v-if="item.selectedColor" class="item-color">
                                <span class="color-dot-small" :style="{ backgroundColor: getColorHex(item.selectedColor) }"></span>
                                {{ item.selectedColor }}
                              </span>
                            </li>
                          </ul>
                        </details>
                      </div>

                      <!-- Fallback -->
                      <div v-else class="product-name">{{ sale.productName }}</div>
                    </div>
                  </td>
                  <td>
                    <div class="quantity-info">
                      <span class="quantity-badge">{{ sale.quantity }}</span>
                      <span v-if="sale.items && sale.items.length > 1" class="quantity-label">unidades totales</span>
                    </div>
                  </td>
                  <td>
                    <span class="amount">${{ sale.totalAmount.toLocaleString() }}</span>
                  </td>
                  <td>
                    <span :class="['status-badge', sale.status]">
                      {{ getSaleStatusText(sale.status) }}
                    </span>
                  </td>
                  <td>
                    <span class="date">{{ formatDate(sale.date) }}</span>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Estado vacío -->
          <div v-else class="empty-state">
            <div class="empty-icon">📊</div>
            <h3>No hay ventas registradas</h3>
            <p>Las ventas aparecerán aquí cuando los clientes realicen compras</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal de Producto -->
    <div v-if="showProductForm" class="modal-overlay" @click="closeProductForm">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <h3>{{ editingProduct ? 'Editar Producto' : 'Nuevo Producto' }}</h3>
          <button class="modal-close" @click="closeProductForm">✕</button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="saveProduct">
            <div class="form-group">
              <label>Nombre del Producto *</label>
              <input v-model="productForm.name" type="text" class="form-input" required placeholder="Ej: iPhone 15 Pro" />
            </div>

            <div class="form-group">
              <label>Descripción</label>
              <textarea v-model="productForm.description" class="form-input" rows="3" placeholder="Describe las características principales del producto"></textarea>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label>Precio *</label>
                <div class="price-input">
                  <span class="currency">$</span>
                  <input v-model.number="productForm.price" type="number" class="form-input" step="1000" min="0" required placeholder="0" />
                </div>
              </div>
              <div class="form-group">
                <label>Precio Original (descuento)</label>
                <div class="price-input">
                  <span class="currency">$</span>
                  <input v-model.number="productForm.originalPrice" type="number" class="form-input" step="1000" min="0" placeholder="0" />
                </div>
              </div>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label>Categoría *</label>
                <select v-model="productForm.category" class="form-input" required>
                  <option value="">Seleccionar categoría</option>
                  <option v-for="category in categories" :key="category.id" :value="category.id">
                    {{ category.name }}
                  </option>
                </select>
              </div>
              <div class="form-group">
                <label>Estado de Disponibilidad *</label>
                <select v-model="productForm.status" class="form-input" required>
                  <option value="available">✅ Disponible</option>
                  <option value="out-of-stock">❌ Sin Stock</option>
                  <option value="coming-soon">🔜 Próximamente</option>
                </select>
              </div>
            </div>

            <!-- Selector de Colores -->
            <div class="form-group">
              <label>Configuración de Colores</label>

              <!-- Toggle para modo de color -->
              <div class="color-mode-selector">
                <button
                  type="button"
                  class="mode-btn"
                  :class="{ active: productForm.colorMode === 'image' }"
                  @click="productForm.colorMode = 'image'"
                >
                  <span class="mode-icon">🖼️</span>
                  <div class="mode-text">
                    <strong>Color de la imagen</strong>
                    <span>El producto viene en el color mostrado</span>
                  </div>
                </button>
                <button
                  type="button"
                  class="mode-btn"
                  :class="{ active: productForm.colorMode === 'selection' }"
                  @click="productForm.colorMode = 'selection'"
                >
                  <span class="mode-icon">🎨</span>
                  <div class="mode-text">
                    <strong>Múltiples colores</strong>
                    <span>El cliente puede elegir entre varios colores</span>
                  </div>
                </button>
              </div>

              <!-- Selector de colores (solo visible si el modo es 'selection') -->
              <div v-if="productForm.colorMode === 'selection'" class="colors-selector">
                <!-- Agregar color personalizado -->
                <div class="add-custom-color">
                  <div class="custom-color-inputs">
                    <input
                      v-model="customColorName"
                      type="text"
                      class="form-input custom-color-input"
                      placeholder="Nombre del color (ej: Azul Marino)"
                    />
                    <input
                      v-model="customColorHex"
                      type="color"
                      class="color-picker"
                      title="Seleccionar color"
                    />
                    <button
                      type="button"
                      class="btn-add-color"
                      @click="addCustomColor"
                      :disabled="!customColorName.trim()"
                    >
                      ➕ Agregar
                    </button>
                  </div>
                </div>

                <div class="colors-grid">
                  <div
                    v-for="color in appleColors"
                    :key="color.name"
                    class="color-option"
                    :class="{ selected: isColorSelected(color.name) }"
                    @click="toggleProductColor(color.name)"
                  >
                    <div class="color-circle" :style="{ background: color.hex }">
                      <span v-if="isColorSelected(color.name)" class="check-icon">✓</span>
                    </div>
                    <span class="color-name">{{ color.name }}</span>
                    <!-- Botón para eliminar colores personalizados -->
                    <button
                      v-if="color.isCustom"
                      type="button"
                      class="remove-custom-color"
                      @click.stop="removeCustomColor(color.name)"
                      title="Eliminar color personalizado"
                    >
                      ✕
                    </button>
                  </div>
                </div>
                <div v-if="productForm.colors.length > 0" class="selected-colors">
                  <span class="selected-label">Seleccionados ({{ productForm.colors.length }}): </span>
                  <span class="selected-list">{{ productForm.colors.join(', ') }}</span>
                </div>
                <div v-else class="color-hint">
                  ℹ️ Selecciona al menos un color para que el cliente pueda elegir
                </div>
              </div>

              <!-- Mensaje para modo imagen -->
              <div v-if="productForm.colorMode === 'image'" class="color-image-note">
                <span class="note-icon">ℹ️</span>
                <p>El producto se mostrará con el color de la imagen y no habrá opciones de selección de color para el cliente.</p>
              </div>
            </div>

            <!-- Subida de imagen -->
            <div class="form-group">
              <label>URLs de Imágenes del Producto *</label>
              <p class="field-hint">Agrega hasta 5 URLs de imágenes. La primera imagen será la principal.</p>

              <!-- Lista de URLs de imágenes -->
              <div class="image-urls-container">
                <div
                  v-for="(imageUrl, index) in productForm.images"
                  :key="index"
                  class="image-url-item"
                >
                  <div class="url-input-wrapper">
                    <span class="url-index">{{ index + 1 }}</span>
                    <input
                      v-model="productForm.images[index]"
                      type="url"
                      class="form-input url-input"
                      :required="index === 0"
                      placeholder="https://ejemplo.com/imagen.jpg"
                    />
                    <button
                      v-if="productForm.images.length > 1"
                      type="button"
                      class="btn-remove-url"
                      @click="removeSingleImage(index)"
                      title="Eliminar esta URL"
                    >
                      ✕
                    </button>
                  </div>
                  <div v-if="imageUrl" class="url-preview">
                    <img :src="imageUrl" :alt="`Imagen ${index + 1}`" @error="handleImageError(index)" />
                    <div class="url-actions">
                      <button
                        type="button"
                        class="btn-reorder"
                        :disabled="index === 0"
                        @click="moveImageLeft(index)"
                        title="Mover a la izquierda"
                      >←</button>
                      <button
                        type="button"
                        class="btn-reorder"
                        :disabled="index === productForm.images.length - 1"
                        @click="moveImageRight(index)"
                        title="Mover a la derecha"
                      >→</button>
                    </div>
                  </div>
                </div>

                <!-- Botón para agregar más URLs -->
                <button
                  v-if="productForm.images.length < 5"
                  type="button"
                  class="btn-add-url"
                  @click="addImageUrl"
                >
                  ➕ Agregar otra URL de imagen
                </button>
              </div>
            </div>

            <!-- Información adicional -->
            <div v-if="productForm.originalPrice && productForm.originalPrice > productForm.price" class="discount-info">
              <span class="discount-badge">
                💰 Descuento: {{ Math.round(((productForm.originalPrice - productForm.price) / productForm.originalPrice) * 100) }}%
              </span>
            </div>

            <div class="form-actions">
              <button type="button" class="btn btn-secondary" @click="closeProductForm">Cancelar</button>
              <button type="submit" class="btn btn-primary" :disabled="!isFormValid">
                {{ editingProduct ? 'Actualizar Producto' : 'Crear Producto' }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Modal de Categoría -->
    <div v-if="showCategoryForm" class="modal-overlay" @click="closeCategoryForm">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <h3>{{ editingCategory ? 'Editar Categoría' : 'Nueva Categoría' }}</h3>
          <button class="modal-close" @click="closeCategoryForm">✕</button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="saveCategory">
            <div class="form-group">
              <label>Nombre de la Categoría</label>
              <input v-model="categoryForm.name" type="text" class="form-input" required />
            </div>
            <div class="form-group">
              <label>Descripción</label>
              <textarea v-model="categoryForm.description" class="form-input" rows="3"></textarea>
            </div>
            <div class="form-actions">
              <button type="button" class="btn btn-secondary" @click="closeCategoryForm">Cancelar</button>
              <button type="submit" class="btn btn-primary">{{ editingCategory ? 'Actualizar' : 'Crear' }}</button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Modal de Novedad -->
    <div v-if="showShowcaseForm" class="modal-overlay" @click="closeShowcaseForm">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <h3>{{ editingShowcaseProduct ? 'Editar Novedad' : 'Nueva Novedad' }}</h3>
          <button class="modal-close" @click="closeShowcaseForm">✕</button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="saveShowcaseProduct">
            <div class="form-group">
              <label>Nombre del Producto *</label>
              <input v-model="showcaseForm.name" type="text" class="form-input" required />
            </div>
            <div class="form-group">
              <label>Descripción *</label>
              <textarea v-model="showcaseForm.description" class="form-input" rows="3" required></textarea>
            </div>
            <!-- Campo de precio oculto - siempre será 0 para novedades -->
            <div class="form-group" style="display: none;">
              <label>Precio</label>
              <input v-model.number="showcaseForm.price" type="number" class="form-input" min="0" step="1000" />
            </div>
            <div class="form-group">
              <label>URLs de Imágenes del Producto *</label>
              <p class="field-hint">Agrega hasta 5 URLs de imágenes. La primera imagen será la principal.</p>

              <!-- Lista de URLs de imágenes -->
              <div class="image-urls-container">
                <div
                  v-for="(imageUrl, index) in showcaseForm.images"
                  :key="index"
                  class="image-url-item"
                >
                  <div class="url-input-wrapper">
                    <span class="url-index">{{ index + 1 }}</span>
                    <input
                      v-model="showcaseForm.images[index]"
                      type="url"
                      class="form-input url-input"
                      :required="index === 0"
                      placeholder="https://ejemplo.com/imagen.jpg"
                    />
                    <button
                      v-if="showcaseForm.images.length > 1"
                      type="button"
                      class="btn-remove-url"
                      @click="removeShowcaseImage(index)"
                      title="Eliminar esta URL"
                    >
                      ✕
                    </button>
                  </div>
                  <div v-if="imageUrl" class="url-preview">
                    <img :src="imageUrl" :alt="`Imagen ${index + 1}`" @error="handleShowcaseImageError(index)" />
                    <div class="url-actions">
                      <button
                        type="button"
                        class="btn-reorder"
                        :disabled="index === 0"
                        @click="moveShowcaseImageLeft(index)"
                        title="Mover a la izquierda"
                      >←</button>
                      <button
                        type="button"
                        class="btn-reorder"
                        :disabled="index === showcaseForm.images.length - 1"
                        @click="moveShowcaseImageRight(index)"
                        title="Mover a la derecha"
                      >→</button>
                    </div>
                  </div>
                </div>

                <!-- Botón para agregar más URLs -->
                <button
                  v-if="showcaseForm.images.length < 5"
                  type="button"
                  class="btn-add-url"
                  @click="addShowcaseImageUrl"
                >
                  ➕ Agregar otra URL de imagen
                </button>
              </div>
            </div>
            <div class="form-group">
              <label>Categoría *</label>
              <select v-model="showcaseForm.category" class="form-input" required>
                <option value="">Seleccionar categoría</option>
                <option v-for="category in categories" :key="category.id" :value="category.id">
                  {{ category.name }}
                </option>
              </select>
            </div>
            <div class="form-actions">
              <button type="button" class="btn btn-secondary" @click="closeShowcaseForm">Cancelar</button>
              <button type="submit" class="btn btn-primary" :disabled="isSavingShowcase || !showcaseFormValid">
                <span v-if="isSavingShowcase">Guardando...</span>
                <span v-else>{{ editingShowcaseProduct ? 'Actualizar' : 'Crear' }}</span>
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'
import { useProducts, type ShowcaseProduct } from '@/composables/useProducts'
import type { Product } from '@/types/ProductType'
import type { Category, CreateCategoryRequest } from '@/types/CategoryType'
import { paymentService } from '@/services/api/paymentService'
import type { Purchase, ProductPaymentItem } from '@/services/api/paymentService'

// Tipos
interface Sale {
  id: string
  productId: string
  productName: string
  customerName: string
  customerEmail: string
  quantity: number
  unitPrice: number
  totalAmount: number
  status: 'completed' | 'pending' | 'cancelled'
  date: Date
  selectedColor?: string
  items?: ProductPaymentItem[] // Items detallados de la compra
}

// Estado reactivo (persistente)
const ACTIVE_TAB_KEY = 'admin_active_tab'
const activeTab = ref<string>(localStorage.getItem(ACTIVE_TAB_KEY) || 'products')
const showProductForm = ref(false)
const showCategoryForm = ref(false)
const showShowcaseForm = ref(false)
const editingProduct = ref<Product | null>(null)
const editingCategory = ref<Category | null>(null)
const editingShowcaseProduct = ref<ShowcaseProduct | null>(null)
const imagePreview = ref('')
const fileInput = ref<HTMLInputElement | null>(null)

// Variables para product image upload
const productImageUploadMethod = ref('url')

// Variables para showcase image upload
const imageUploadMethod = ref('url')
const showcaseImagePreview = ref('')
const showcaseFileInput = ref<HTMLInputElement | null>(null)

// Variables para búsqueda
const searchProducts = ref('')
const searchCategories = ref('')
const searchShowcase = ref('')
const searchSales = ref('')

// Variables para colores personalizados
const customColorName = ref('')
const customColorHex = ref('#000000')

// Usar el composable de productos
const {
  regularProducts, // Productos regulares (sin showcase) - para mostrar en sección Productos
  showcaseProducts,
  categories,
  availableProducts,
  addProduct,
  updateProduct,
  deleteProduct,
  loadShowcaseProducts,
  addShowcaseProduct,
  updateShowcaseProduct,
  deleteShowcaseProduct,
  getCategoryById,
  loadCategories,
  loadProducts,
  addCategory,
  updateCategory,
  deleteCategory
} = useProducts()

// Alias para compatibilidad: usar regularProducts en la vista de productos
const products = regularProducts

// Cargar categorías y productos desde el backend al montar el componente
onMounted(async () => {
  console.log('🔄 Cargando categorías y productos al montar el componente...')
  await loadCategories()
  await loadProducts()
  await loadShowcaseProducts()
  await loadPurchases()
  console.log('✅ Categorías cargadas:', categories.value)
  console.log('✅ Productos cargados:', products.value)
  console.log('✅ Productos showcase cargados:', showcaseProducts.value)
})

// Watcher para debug: observar cambios en categorías
watch(categories, (newCategories) => {
  console.log('🔔 [Watch] Categorías cambiaron:', newCategories.length, newCategories)
}, { deep: true })

// Sales / Purchases data
const sales = ref<Sale[]>([])
const isLoadingSales = ref(false)
const salesError = ref('')

// Transform Purchase to Sale format
const transformPurchaseToSale = (purchase: Purchase): Sale => {
  const firstItem = purchase.items?.[0]
  const itemCount = purchase.items?.length || 0
  const totalQuantity = purchase.items?.reduce((sum, item) => sum + item.quantity, 0) || 0

  // Generar nombre descriptivo del producto
  let productName = 'Múltiples productos'
  if (itemCount === 1) {
    productName = firstItem?.productName || 'Producto desconocido'
  } else if (itemCount > 1) {
    productName = `${itemCount} productos diferentes`
  }

  return {
    id: purchase.id.toString(),
    productId: '', // No longer available from API
    productName: productName,
    customerName: purchase.buyerName,
    customerEmail: purchase.buyerEmail,
    quantity: totalQuantity, // Suma total de cantidades
    unitPrice: firstItem?.unitPrice || 0,
    totalAmount: purchase.amount, // Use amount directly from API
    status: mapPurchaseStatus(purchase.status),
    date: new Date(purchase.createdAt),
    selectedColor: firstItem?.selectedColor,
    // Información adicional para mostrar detalles
    items: purchase.items
  }
}

// Map purchase status to sale status
const mapPurchaseStatus = (status: string): 'completed' | 'pending' | 'cancelled' => {
  const upperStatus = status.toUpperCase()
  if (upperStatus === 'COMPLETED' || upperStatus === 'APPROVED') return 'completed'
  if (upperStatus === 'CANCELLED' || upperStatus === 'REJECTED') return 'cancelled'
  return 'pending'
}

// Load purchases from API
const loadPurchases = async () => {
  isLoadingSales.value = true
  salesError.value = ''
  try {
    console.log('📦 Cargando compras desde API...')
    const response = await paymentService.getAllPurchases()
    console.log('📦 Respuesta completa de compras:', response)

    if (response.success && response.data) {
      console.log('📦 Purchases raw data:', response.data.purchases)
      sales.value = response.data.purchases.map((purchase) => {
        console.log('📦 Transformando purchase:', {
          id: purchase.id,
          amount: purchase.amount,
          items: purchase.items
        })
        return transformPurchaseToSale(purchase)
      })
      console.log('✅ Compras transformadas:', sales.value)
    } else {
      salesError.value = 'No se pudieron cargar las compras'
      console.error('❌ Error en respuesta de compras:', response)
    }
  } catch (error) {
    salesError.value = 'Error al cargar las compras'
    console.error('❌ Error cargando compras:', error)
  } finally {
    isLoadingSales.value = false
  }
}

// Formularios
const productForm = ref({
  name: '',
  description: '',
  price: 0,
  originalPrice: 0,
  images: [] as string[],
  category: '',
  status: 'available' as 'available' | 'out-of-stock' | 'coming-soon',
  colors: [] as string[],
  colorMode: 'image' as 'image' | 'selection' // 'image' = color de la imagen, 'selection' = múltiples colores
})

// Colores primarios predeterminados
const appleColors = ref([
  { name: 'Rojo', hex: '#FF0000', isCustom: false },
  { name: 'Azul', hex: '#0000FF', isCustom: false },
  { name: 'Amarillo', hex: '#FFFF00', isCustom: false },
  { name: 'Verde', hex: '#00FF00', isCustom: false },
  { name: 'Naranja', hex: '#FFA500', isCustom: false },
  { name: 'Morado', hex: '#800080', isCustom: false },
  { name: 'Rosa', hex: '#FFC0CB', isCustom: false },
  { name: 'Negro', hex: '#000000', isCustom: false },
  { name: 'Blanco', hex: '#FFFFFF', isCustom: false },
  { name: 'Gris', hex: '#808080', isCustom: false }
])

const categoryForm = ref<CreateCategoryRequest>({
  name: '',
  description: ''
})

const showcaseForm = ref({
  name: '',
  description: '',
  price: 0,
  images: [''] as string[],
  category: ''
})

// Estado de guardado de showcase (evita clicks múltiples y sensación de "bloqueo")
const isSavingShowcase = ref(false)

// Validación rápida del formulario de novedad (precio no requerido - siempre será 0)
const showcaseFormValid = computed(() => {
  const validImages = showcaseForm.value.images.filter(img => img.trim() !== '')
  return (
    showcaseForm.value.name.trim().length > 0 &&
    showcaseForm.value.description.trim().length > 0 &&
    validImages.length > 0 &&
    showcaseForm.value.category.trim().length > 0
  )
})

// Pestañas
const tabs = [
  { id: 'products', name: 'Productos', icon: '📦' },
  { id: 'categories', name: 'Categorías', icon: '🏷️' },
  { id: 'showcase', name: 'Novedades', icon: '✨' },
  { id: 'sales', name: 'Resumen de Compras', icon: '📊' }
]

// Computed
const availableProductsCount = computed(() =>
  availableProducts.value.length
)

const totalValue = computed(() =>
  sales.value
    .filter(sale => sale.status === 'completed')
    .reduce((sum, sale) => sum + sale.totalAmount, 0)
)

// Computed para estadísticas de ventas
const completedSales = computed(() =>
  sales.value.filter(s => s.status === 'completed')
)

const totalRevenue = computed(() =>
  completedSales.value.reduce((sum, s) => sum + s.totalAmount, 0)
)

const pendingSales = computed(() =>
  sales.value.filter(s => s.status === 'pending').length
)

const totalSalesCount = computed(() => sales.value.length)

// Computed properties para búsqueda y filtrado
const filteredProducts = computed(() => {
  if (!searchProducts.value.trim()) {
    return products.value
  }
  const searchLower = searchProducts.value.toLowerCase().trim()
  return products.value.filter(product =>
    product.name.toLowerCase().includes(searchLower) ||
    product.description?.toLowerCase().includes(searchLower)
  )
})

const filteredCategories = computed(() => {
  if (!searchCategories.value.trim()) {
    return categories.value
  }
  const searchLower = searchCategories.value.toLowerCase().trim()
  return categories.value.filter(category =>
    category.name.toLowerCase().includes(searchLower) ||
    category.description?.toLowerCase().includes(searchLower)
  )
})

const filteredShowcase = computed(() => {
  if (!searchShowcase.value.trim()) {
    return showcaseProducts.value
  }
  const searchLower = searchShowcase.value.toLowerCase().trim()
  return showcaseProducts.value.filter(product =>
    product.name.toLowerCase().includes(searchLower) ||
    product.description?.toLowerCase().includes(searchLower)
  )
})

const filteredSales = computed(() => {
  if (!searchSales.value.trim()) {
    return sales.value
  }
  const searchLower = searchSales.value.toLowerCase().trim()
  return sales.value.filter(sale =>
    sale.customerName.toLowerCase().includes(searchLower) ||
    sale.customerEmail.toLowerCase().includes(searchLower) ||
    sale.productName.toLowerCase().includes(searchLower) ||
    // Buscar también en los items individuales
    (sale.items && sale.items.some(item =>
      item.productName.toLowerCase().includes(searchLower)
    ))
  )
})

// Helper para convertir nombres de colores a hex
const getColorHex = (colorName: string): string => {
  // Primero buscar en la paleta actual de colores (incluye personalizados)
  const color = appleColors.value.find(
    c => normalizeString(c.name) === normalizeString(colorName)
  )

  if (color) {
    return color.hex
  }

  // Mapa de respaldo para colores comunes
  const colorMap: Record<string, string> = {
    'rojo': '#FF0000',
    'red': '#FF0000',
    'azul': '#0000FF',
    'blue': '#0000FF',
    'amarillo': '#FFFF00',
    'yellow': '#FFFF00',
    'verde': '#00FF00',
    'green': '#00FF00',
    'naranja': '#FFA500',
    'orange': '#FFA500',
    'morado': '#800080',
    'púrpura': '#800080',
    'purpura': '#800080',
    'purple': '#800080',
    'rosa': '#FFC0CB',
    'pink': '#FFC0CB',
    'negro': '#000000',
    'black': '#000000',
    'blanco': '#FFFFFF',
    'white': '#FFFFFF',
    'gris': '#808080',
    'gray': '#808080',
    'grey': '#808080'
  }

  const normalized = colorName.toLowerCase().trim()
  return colorMap[normalized] || '#9e9e9e' // Gris por defecto
}

// Cambio de pestaña con persistencia
const selectTab = (tabId: string) => {
  activeTab.value = tabId
  try {
    localStorage.setItem(ACTIVE_TAB_KEY, tabId)
  } catch (e) {
    console.warn('No se pudo persistir la pestaña activa', e)
  }
}

// Métodos
const getStatusText = (status: string) => {
  const statusMap: Record<string, string> = {
    'available': 'Disponible',
    'out-of-stock': 'Sin Stock',
    'coming-soon': 'Próximamente'
  }
  return statusMap[status] || status
}

const getSaleStatusText = (status: string) => {
  const statusMap: Record<string, string> = {
    'completed': 'Completada',
    'pending': 'Pendiente',
    'cancelled': 'Cancelada'
  }
  return statusMap[status] || status
}

const formatDate = (date: Date) => {
  return date.toLocaleDateString('es-CO', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })
}

const getProductsInCategory = (categoryId: string) => {
  return products.value.filter(p => p.category === categoryId).length
}

const editProduct = (product: Product) => {
  editingProduct.value = product
  const productImages = product.images && product.images.length > 0 ? [...product.images] : ['']
  productForm.value = {
    name: product.name,
    description: product.description,
    price: product.price,
    originalPrice: product.originalPrice || 0,
    images: productImages,
    category: product.category,
    status: product.status,
    colors: product.colors ? [...product.colors] : [],
    colorMode: (product.colors && product.colors.length > 0) ? 'selection' : 'image'
  }
  // Configurar preview con la primera imagen si existe
  if (productImages.length > 0 && productImages[0]) {
    imagePreview.value = productImages[0]
  } else {
    imagePreview.value = ''
  }
  showProductForm.value = true
}

const editCategory = (category: Category) => {
  editingCategory.value = category
  categoryForm.value = {
    name: category.name,
    description: category.description || '' // Manejar descripción opcional
  }
  showCategoryForm.value = true
}

const deleteProductConfirm = (id: string) => {
  if (confirm('¿Estás seguro de eliminar este producto?')) {
    deleteProduct(id)
  }
}

// Función wrapper para eliminar categoría con confirmación
const handleDeleteCategory = async (id: string) => {
  const productsCount = getProductsInCategory(id)

  let confirmMessage = '¿Estás seguro de eliminar esta categoría?'

  if (productsCount > 0) {
    confirmMessage = `⚠️ ADVERTENCIA: Esta categoría tiene ${productsCount} producto${productsCount > 1 ? 's' : ''} asociado${productsCount > 1 ? 's' : ''}.\n\n` +
      `Si eliminas esta categoría, ${productsCount > 1 ? 'estos productos' : 'este producto'} quedará${productsCount > 1 ? 'n' : ''} sin categoría asignada.\n\n` +
      `¿Estás seguro de que deseas continuar?`
  }

  if (confirm(confirmMessage)) {
    await deleteCategory(Number(id))
  }
}

// Funciones para showcase products
const editShowcaseProduct = (product: ShowcaseProduct) => {
  editingShowcaseProduct.value = product
  // Convertir image a images array para compatibilidad
  const productImages = product.image ? [product.image] : ['']
  showcaseForm.value = {
    name: product.name,
    description: product.description,
    price: 5000,
    images: productImages,
    category: product.category
  }
  showShowcaseForm.value = true
}

const deleteShowcaseConfirm = async (id: string) => {
  if (confirm('¿Estás seguro de eliminar esta novedad?')) {
    try {
      await deleteShowcaseProduct(id)
      console.log('✅ Producto showcase eliminado')
    } catch (error) {
      console.error('❌ Error eliminando producto showcase:', error)
      alert('Error al eliminar la novedad')
    }
  }
}

const saveShowcaseProduct = async () => {
  if (isSavingShowcase.value) return
  if (!showcaseFormValid.value) {
    alert('Por favor completa todos los campos requeridos de la novedad.')
    return
  }
  try {
    isSavingShowcase.value = true

    // Filtrar URLs vacías
    const cleanedImages = showcaseForm.value.images.filter(img => img.trim() !== '')

    // Asegurar que el precio siempre sea 5000 para novedades
    // Solo usar la primera imagen para showcase (compatibilidad con backend)
    const showcaseData = {
      name: showcaseForm.value.name,
      description: showcaseForm.value.description,
      price: 5000,
      image: cleanedImages[0] || '',
      category: showcaseForm.value.category
    }

    if (editingShowcaseProduct.value) {
      // Actualizar novedad existente - mostrar confirmación
      const confirmMessage = `¿Estás seguro de que deseas actualizar la novedad "${editingShowcaseProduct.value.name}"?\n\nSe actualizarán todos los cambios realizados.`
      if (!confirm(confirmMessage)) {
        isSavingShowcase.value = false
        return
      }
      await updateShowcaseProduct(editingShowcaseProduct.value.id, showcaseData)
      console.log('✅ Producto showcase actualizado')
    } else {
      await addShowcaseProduct(showcaseData)
      console.log('✅ Producto showcase agregado')
    }
    closeShowcaseForm()
  } catch (e: unknown) {
    console.error('❌ Error guardando producto showcase:', e)
    const msg = typeof e === 'object' && e && 'message' in e ? (e as { message?: string }).message : undefined
    alert(msg || 'Ocurrió un problema al guardar la novedad.')
  } finally {
    isSavingShowcase.value = false
  }
}

const closeShowcaseForm = () => {
  showShowcaseForm.value = false
  editingShowcaseProduct.value = null
  imagePreview.value = ''
  showcaseImagePreview.value = ''
  imageUploadMethod.value = 'url'
  showcaseForm.value = {
    name: '',
    description: '',
    price: 0,
    images: [''],
    category: ''
  }
  // Limpiar input de archivo
  if (showcaseFileInput.value) {
    showcaseFileInput.value.value = ''
  }
}

// Computed para validación de formulario
const isFormValid = computed(() => {
  // Filtrar URLs vacías y verificar que haya al menos una URL válida
  const validImages = productForm.value.images.filter(img => img.trim() !== '')
  return productForm.value.name.trim() !== '' &&
         productForm.value.price > 0 &&
         productForm.value.category !== '' &&
         validImages.length > 0
})

const handleMultipleFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  const files = target.files
  if (files && files.length > 0) {
    handleMultipleImageFiles(files)
  }
}

const handleMultipleImageFiles = (files: FileList) => {
  const imagePromises: Promise<string>[] = []

  for (let i = 0; i < Math.min(files.length, 5); i++) { // Máximo 5 imágenes
    const file = files[i]

    // Validar tamaño (5MB máximo)
    if (file.size > 5 * 1024 * 1024) {
      alert(`El archivo ${file.name} es demasiado grande. Máximo 5MB.`)
      continue
    }

    // Validar tipo
    if (!file.type.startsWith('image/')) {
      alert(`${file.name} no es un archivo de imagen válido.`)
      continue
    }

    // Crear promesa para leer el archivo
    const promise = new Promise<string>((resolve) => {
      const reader = new FileReader()
      reader.onload = (e) => {
        resolve(e.target?.result as string)
      }
      reader.readAsDataURL(file)
    })

    imagePromises.push(promise)
  }

  // Procesar todas las imágenes
  Promise.all(imagePromises).then((base64Images) => {
    productForm.value.images = base64Images
    imagePreview.value = base64Images[0] || ''
  })
}


const updateImagePreview = () => {
  if (productForm.value.images.length > 0 && productForm.value.images[0].startsWith('http')) {
    imagePreview.value = productForm.value.images[0]
  } else {
    imagePreview.value = ''
  }
}

// Agregar nueva URL de imagen
const addImageUrl = () => {
  if (productForm.value.images.length < 5) {
    productForm.value.images.push('')
  }
}

// Manejar error de carga de imagen
const handleImageError = (index: number) => {
  console.error(`Error al cargar la imagen ${index + 1}`)
}

const removeSingleImage = (index: number) => {
  productForm.value.images.splice(index, 1)
  // Asegurar que siempre haya al menos una URL vacía
  if (productForm.value.images.length === 0) {
    productForm.value.images.push('')
  }
  if (productForm.value.images.length > 0) {
    imagePreview.value = productForm.value.images[0]
  } else {
    imagePreview.value = ''
  }
}

// Reordenar: mover a la izquierda
const moveImageLeft = (index: number) => {
  if (index <= 0) return
  const imgs = productForm.value.images
  ;[imgs[index - 1], imgs[index]] = [imgs[index], imgs[index - 1]]
  imagePreview.value = imgs[0] || ''
}

// Reordenar: mover a la derecha
const moveImageRight = (index: number) => {
  const imgs = productForm.value.images
  if (index >= imgs.length - 1) return
  ;[imgs[index + 1], imgs[index]] = [imgs[index], imgs[index + 1]]
  imagePreview.value = imgs[0] || ''
}

// setAsCover removido (no se usa actualmente)

// Funciones específicas para showcase image upload
const handleShowcaseFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (file) {
    handleShowcaseImageFile(file)
  }
}

const handleShowcaseImageFile = (file: File) => {
  // Validar tamaño (5MB máximo)
  if (file.size > 5 * 1024 * 1024) {
    alert('El archivo es demasiado grande. Máximo 5MB.')
    return
  }

  // Validar tipo
  if (!file.type.startsWith('image/')) {
    alert('Solo se permiten archivos de imagen.')
    return
  }

  // Crear URL temporal para vista previa
  const reader = new FileReader()
  reader.onload = (e) => {
    showcaseImagePreview.value = e.target?.result as string
    showcaseForm.value.images[0] = e.target?.result as string
  }
  reader.readAsDataURL(file)
}

// Funciones para manejar múltiples URLs en showcase
const addShowcaseImageUrl = () => {
  if (showcaseForm.value.images.length < 5) {
    showcaseForm.value.images.push('')
  }
}

const removeShowcaseImage = (index: number) => {
  showcaseForm.value.images.splice(index, 1)
  // Asegurar que siempre haya al menos una URL vacía
  if (showcaseForm.value.images.length === 0) {
    showcaseForm.value.images.push('')
  }
}

const moveShowcaseImageLeft = (index: number) => {
  if (index <= 0) return
  const imgs = showcaseForm.value.images
  ;[imgs[index - 1], imgs[index]] = [imgs[index], imgs[index - 1]]
}

const moveShowcaseImageRight = (index: number) => {
  const imgs = showcaseForm.value.images
  if (index >= imgs.length - 1) return
  ;[imgs[index + 1], imgs[index]] = [imgs[index], imgs[index + 1]]
}

const handleShowcaseImageError = (index: number) => {
  console.error(`Error al cargar la imagen de showcase ${index + 1}`)
}

// Función auxiliar para normalizar strings (quitar tildes, espacios extra, etc.)
const normalizeString = (str: string): string => {
  return str
    .toLowerCase()
    .normalize('NFD')
    .replace(/[\u0300-\u036f]/g, '') // Quitar tildes y diacríticos
    .trim()
    .replace(/\s+/g, ' ') // Normalizar espacios múltiples a uno solo
}

// Función para verificar si un color está seleccionado (comparación normalizada)
const isColorSelected = (colorName: string) => {
  const normalizedColorName = normalizeString(colorName)
  return productForm.value.colors.some(
    c => normalizeString(c) === normalizedColorName
  )
}

// Función para manejar la selección de colores
const toggleProductColor = (colorName: string) => {
  const normalizedColorName = normalizeString(colorName)

  // Buscar el índice comparando strings normalizados
  const index = productForm.value.colors.findIndex(
    c => normalizeString(c) === normalizedColorName
  )

  if (index > -1) {
    // Si ya está seleccionado, lo removemos
    productForm.value.colors.splice(index, 1)
  } else {
    // Si no está seleccionado, lo agregamos (usando el formato correcto del appleColors)
    productForm.value.colors.push(colorName)
  }
}

// Función para agregar un color personalizado
const addCustomColor = () => {
  const colorName = customColorName.value.trim()

  if (!colorName) {
    alert('Por favor ingresa un nombre para el color')
    return
  }

  // Verificar si el color ya existe
  const exists = appleColors.value.some(
    color => normalizeString(color.name) === normalizeString(colorName)
  )

  if (exists) {
    alert('Ya existe un color con ese nombre')
    return
  }

  // Agregar el nuevo color a la lista
  appleColors.value.push({
    name: colorName,
    hex: customColorHex.value,
    isCustom: true
  })

  // Seleccionar automáticamente el color recién agregado
  productForm.value.colors.push(colorName)

  // Limpiar los campos
  customColorName.value = ''
  customColorHex.value = '#000000'

  // Mostrar confirmación
  console.log('✅ Color personalizado agregado:', colorName, customColorHex.value)
}

// Función para eliminar un color personalizado
const removeCustomColor = (colorName: string) => {
  if (confirm(`¿Estás seguro de eliminar el color "${colorName}"?`)) {
    // Eliminar de la lista de colores disponibles
    const index = appleColors.value.findIndex(
      color => normalizeString(color.name) === normalizeString(colorName)
    )

    if (index > -1) {
      appleColors.value.splice(index, 1)
    }

    // Si estaba seleccionado, también lo eliminamos de la selección
    const selectedIndex = productForm.value.colors.findIndex(
      c => normalizeString(c) === normalizeString(colorName)
    )

    if (selectedIndex > -1) {
      productForm.value.colors.splice(selectedIndex, 1)
    }

    console.log('✅ Color personalizado eliminado:', colorName)
  }
}

const saveProduct = () => {
  // Filtrar URLs vacías antes de guardar
  const cleanedImages = productForm.value.images.filter(img => img.trim() !== '')
  const productData = {
    ...productForm.value,
    images: cleanedImages
  }

  if (editingProduct.value) {
    // Actualizar producto existente - mostrar confirmación
    const confirmMessage = `¿Estás seguro de que deseas actualizar el producto "${editingProduct.value.name}"?\n\nSe actualizarán todos los cambios realizados.`
    if (!confirm(confirmMessage)) {
      return
    }
    updateProduct(editingProduct.value.id, productData)
  } else {
    // Crear nuevo producto
    addProduct(productData)
  }
  closeProductForm()
}

const saveCategory = async () => {
  if (editingCategory.value) {
    // Actualizar categoría existente - mostrar confirmación
    const confirmMessage = `¿Estás seguro de que deseas actualizar la categoría "${editingCategory.value.name}"?\n\nSe actualizarán todos los cambios realizados.`
    if (!confirm(confirmMessage)) {
      return
    }
    await updateCategory(Number(editingCategory.value.id), categoryForm.value)
  } else {
    // Crear nueva categoría
    await addCategory(categoryForm.value)
  }
  closeCategoryForm()
}

const closeProductForm = () => {
  showProductForm.value = false
  editingProduct.value = null
  imagePreview.value = ''
  productImageUploadMethod.value = 'url'
  productForm.value = {
    name: '',
    description: '',
    price: 0,
    originalPrice: 0,
    images: [''], // Inicializar con una URL vacía
    category: '',
    status: 'available',
    colors: [],
    colorMode: 'image'
  }
  if (fileInput.value) {
    fileInput.value.value = ''
  }
}

const closeCategoryForm = () => {
  showCategoryForm.value = false
  editingCategory.value = null
  categoryForm.value = {
    name: '',
    description: ''
  }
}
</script>

<style scoped>
.admin-dashboard {
  min-height: 100vh;
  background: var(--brand-gradient);
  padding: 20px;
  color: var(--brand-primary-contrast);
}

.dashboard-header {
  text-align: center;
  margin-bottom: 40px;
  padding: 30px;
  background: var(--brand-surface);
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  border: 1px solid var(--brand-border);
}

.dashboard-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: var(--brand-primary-contrast);
  margin: 3rem 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
}

.dashboard-title .icon {
  font-size: 3rem;
}

.dashboard-title .highlight {
  color: var(--brand-success);
}

.dashboard-subtitle {
  font-size: 1.1rem;
  color: var(--brand-accent-alt);
  margin: 0;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 40px;
}

.stat-card {
  background: var(--brand-surface);
  border-radius: 16px;
  padding: 25px;
  display: flex;
  align-items: center;
  gap: 20px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  border: 1px solid var(--brand-border);
  transition: all 0.3s ease;
}

.stat-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.4);
  border-color: var(--brand-accent);
}

.stat-icon {
  font-size: 2.5rem;
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  border-radius: 50%;
  width: 70px;
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.stat-number {
  font-size: 2rem;
  font-weight: 700;
  color: var(--brand-primary-contrast);
  line-height: 1;
}

.stat-label {
  font-size: 0.9rem;
  color: var(--brand-accent-alt);
  font-weight: 500;
}

.tabs-container {
  margin-bottom: 30px;
}

.tabs {
  display: flex;
  gap: 5px;
  background: var(--brand-surface);
  padding: 5px;
  border-radius: 12px;
  width: fit-content;
  border: 1px solid var(--brand-border);
}

.tab {
  background: none;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-weight: 600;
  color: var(--brand-accent-alt);
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
}

.tab.active {
  background: var(--brand-success);
  color: var(--brand-primary-contrast);
  box-shadow: 0 2px 8px rgba(16, 185, 129, 0.5);
}

.tab:hover:not(.active) {
  background: var(--brand-border);
  color: var(--brand-primary-contrast);
}

.content-section {
  background: var(--brand-surface);
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  border: 1px solid var(--brand-border);
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  padding-bottom: 20px;
  border-bottom: 2px solid var(--brand-border);
}

.section-header h2 {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--brand-primary-contrast);
  margin: 0;
}

/* Barras de búsqueda en Admin */
.search-bar {
  display: flex;
  justify-content: center;
  margin: 0 0 2rem;
  position: relative;
}

.search-input-wrapper {
  position: relative;
  width: 100%;
  max-width: 600px;
}

.search-icon {
  position: absolute;
  left: 14px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--brand-accent-alt);
  pointer-events: none;
  z-index: 1;
}

.search-input {
  width: 100%;
  padding: 0.875rem 3.5rem 0.875rem 2.75rem;
  border-radius: 999px;
  border: 1px solid var(--brand-border);
  background: var(--brand-bg-end);
  font-size: 1rem;
  color: var(--brand-primary-contrast);
  box-shadow: 0 4px 12px rgba(0,0,0,0.2);
  transition: all 0.3s ease;
  outline: none;
}

.search-input:focus {
  background: var(--brand-surface);
  border-color: var(--brand-success);
  box-shadow: 0 0 0 3px rgba(16, 185, 129, 0.2);
}

.search-input:hover {
  border-color: var(--brand-accent);
}

.search-input::placeholder {
  color: var(--brand-accent-alt);
  opacity: 0.7;
}

.search-clear {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(16, 185, 129, 0.1);
  border: none;
  cursor: pointer;
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--brand-success);
  width: 28px;
  height: 28px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

.search-clear:hover {
  background: rgba(16, 185, 129, 0.2);
  transform: translateY(-50%) scale(1.05);
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.product-card {
  background: var(--brand-bg-end);
  border-radius: 16px;
  padding: 20px;
  border: 2px solid var(--brand-border);
  transition: all 0.3s ease;
}

.product-card:hover {
  border-color: var(--brand-success);
  box-shadow: 0 8px 25px rgba(16, 185, 129, 0.3);
  transform: translateY(-2px);
}

.product-image {
  width: 100%;
  height: 150px;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 15px;
  background: var(--brand-border);
  display: flex;
  align-items: center;
  justify-content: center;
}

.product-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.no-image {
  font-size: 3rem;
  color: var(--brand-accent-alt);
}

.product-info h3 {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--brand-primary-contrast);
  margin: 0 0 8px;
}

.product-description {
  color: var(--brand-accent-alt);
  font-size: 0.9rem;
  margin: 0 0 15px;
  line-height: 1.4;
}

.product-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.price {
  font-size: 1.3rem;
  font-weight: 700;
  color: var(--brand-success);
}

.status {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
}

.status.available {
  background: #d1fae5;
  color: #065f46;
}

.status.out-of-stock {
  background: #fee2e2;
  color: #991b1b;
}

.status.coming-soon {
  background: #dbeafe;
  color: #1e40af;
}

.product-actions {
  display: flex;
  gap: 10px;
}

.categories-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.category-item {
  background: var(--brand-bg-end);
  border-radius: 12px;
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 2px solid var(--brand-border);
  transition: all 0.3s ease;
}

.category-item:hover {
  border-color: var(--brand-success);
  transform: translateY(-1px);
}

.category-info h3 {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--brand-primary-contrast);
  margin: 0 0 5px;
}

.category-info p {
  color: var(--brand-accent-alt);
  margin: 0 0 5px;
  font-size: 0.9rem;
}

.category-count {
  font-size: 0.8rem;
  color: var(--brand-success);
  font-weight: 600;
}

.category-actions {
  display: flex;
  gap: 10px;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: var(--brand-accent-alt);
}

.empty-icon {
  font-size: 4rem;
  margin-bottom: 20px;
}

.empty-state h3 {
  font-size: 1.5rem;
  margin: 0 0 10px;
  color: var(--brand-primary-contrast);
}

.empty-state p {
  margin: 0 0 30px;
  font-size: 1rem;
}

/* Loading and Error States */
.loading-state, .error-state {
  text-align: center;
  padding: 60px 20px;
}

.loading-state p, .error-state p {
  margin-top: 20px;
  font-size: 1rem;
  color: var(--brand-accent-alt);
}

.error-state {
  color: #f44336;
}

.error-icon {
  font-size: 4rem;
  margin-bottom: 20px;
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: var(--brand-primary);
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
  margin: 0 auto;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* Product info in sales table */
.product-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.product-name {
  font-weight: 500;
}

.product-color {
  font-size: 0.85rem;
  color: var(--brand-accent-alt);
}

/* === SELECTOR DE COLORES === */
.colors-selector {
  margin-top: 1rem;
  padding: 1.5rem;
  background: linear-gradient(145deg, rgba(0, 0, 0, 0.2), rgba(0, 0, 0, 0.1));
  border-radius: 16px;
  border: 1px solid var(--brand-border);
}

.colors-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  gap: 0.75rem;
  margin-bottom: 1.5rem;
}

.color-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem;
  border: 2px solid transparent;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  background: var(--brand-surface);
  position: relative;
}

.color-option:hover {
  transform: translateY(-4px);
  border-color: var(--brand-accent);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
}

.color-option.selected {
  border-color: var(--brand-success);
  background: linear-gradient(145deg, rgba(16, 185, 129, 0.15), rgba(16, 185, 129, 0.05));
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 0 0 4px rgba(16, 185, 129, 0.2), 0 8px 24px rgba(0, 0, 0, 0.4);
}

.color-option.selected .color-circle {
  border-color: var(--brand-success);
  border-width: 4px;
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.4), 0 4px 12px rgba(0, 0, 0, 0.3);
  transform: scale(1.1);
}

.color-option.selected .color-name {
  color: var(--brand-success);
  font-weight: 700;
}

.color-circle {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.3), inset 0 1px 3px rgba(255, 255, 255, 0.1);
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.check-icon {
  font-size: 18px;
  font-weight: bold;
  color: #ffffff;
  text-shadow: 0 0 6px rgba(0, 0, 0, 0.9), 0 1px 3px rgba(0, 0, 0, 0.7), 0 0 2px rgba(255, 255, 255, 0.5);
  position: absolute;
  animation: checkPop 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  filter: drop-shadow(0 0 2px rgba(0, 0, 0, 0.8));
}

@keyframes checkPop {
  0% {
    transform: scale(0) rotate(-45deg);
    opacity: 0;
  }
  50% {
    transform: scale(1.3) rotate(5deg);
  }
  100% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
}

.color-name {
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--brand-primary-contrast);
  text-align: center;
  line-height: 1.3;
  transition: all 0.3s ease;
}

.selected-colors {
  padding: 1rem 1.25rem;
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.15), rgba(16, 185, 129, 0.08));
  border: 2px solid rgba(16, 185, 129, 0.3);
  border-radius: 12px;
  margin-top: 1rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.selected-label {
  font-weight: 700;
  color: var(--brand-success);
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.selected-list {
  color: var(--brand-primary-contrast);
  font-size: 0.9rem;
  font-weight: 500;
}

/* Botones */
.btn {
  padding: 10px 20px;
  border-radius: 8px;
  border: none;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  text-decoration: none;
}

.btn-primary {
  background: var(--brand-success);
  color: var(--brand-primary-contrast);
  box-shadow: 0 2px 10px rgba(16, 185, 129, 0.5);
}

.btn-primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 15px rgba(16, 185, 129, 0.6);
  background: #059669;
}

.btn-secondary {
  background: var(--brand-border);
  color: var(--brand-primary-contrast);
}

.btn-secondary:hover {
  background: var(--brand-surface);
}

.btn-danger {
  background: rgba(239, 68, 68, 0.2);
  color: var(--brand-danger);
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.btn-danger:hover {
  background: rgba(239, 68, 68, 0.3);
}

.btn-sm {
  padding: 6px 12px;
  font-size: 0.8rem;
}

/* Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
}

.modal {
  background: var(--brand-surface);
  border-radius: 20px;
  width: 100%;
  max-width: 600px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6);
  border: 1px solid var(--brand-border);
}

.modal-header {
  padding: 30px 30px 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-header h3 {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--brand-primary-contrast);
  margin: 0;
}

.modal-close {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: var(--brand-accent-alt);
  padding: 5px;
  border-radius: 50%;
  width: 35px;
  height: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.modal-close:hover {
  background: var(--brand-border);
  color: var(--brand-primary-contrast);
}

.modal-body {
  padding: 30px;
}

/* Formularios */
.form-group {
  margin-bottom: 20px;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.form-group label {
  display: block;
  font-weight: 600;
  color: var(--brand-primary-contrast);
  margin-bottom: 8px;
}

.form-input {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid var(--brand-border);
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
  box-sizing: border-box;
  background: var(--brand-bg-end);
  color: var(--brand-primary-contrast);
}

.form-input:focus {
  outline: none;
  border-color: var(--brand-success);
  box-shadow: 0 0 0 3px rgba(16, 185, 129, 0.2);
}

.form-input::placeholder {
  color: var(--brand-accent-alt);
}

.price-input {
  position: relative;
  display: flex;
  align-items: center;
}

.currency {
  position: absolute;
  left: 16px;
  font-weight: 600;
  color: var(--brand-accent-alt);
  z-index: 1;
}

.price-input .form-input {
  padding-left: 35px;
}

/* Subida de imágenes */
.image-upload-area {
  margin-bottom: 15px;
}

.image-preview {
  position: relative;
  width: 200px;
  height: 200px;
  border-radius: 12px;
  overflow: hidden;
  border: 2px solid var(--brand-border);
  margin-bottom: 15px;
}

.image-preview img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.remove-image {
  position: absolute;
  top: 8px;
  right: 8px;
  background: rgba(239, 68, 68, 0.9);
  color: white;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
  transition: background 0.3s ease;
}

.remove-image:hover {
  background: rgba(239, 68, 68, 1);
}

/* Estilos para vista previa de múltiples imágenes */
.images-preview-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 15px;
  max-height: 300px;
  overflow-y: auto;
  padding: 10px;
}

.image-preview-item {
  position: relative;
  width: 120px;
  height: 120px;
  border-radius: 12px;
  overflow: hidden;
  border: 2px solid var(--brand-border);
  background: var(--brand-bg-end);
}

.image-preview-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.remove-single-image {
  position: absolute;
  top: 5px;
  right: 5px;
  background: rgba(239, 68, 68, 0.9);
  color: white;
  border: none;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  transition: background 0.3s ease;
}

.remove-single-image:hover {
  background: rgba(239, 68, 68, 1);
}

.image-index {
  position: absolute;
  bottom: 5px;
  left: 5px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 11px;
  font-weight: 600;
}

.image-actions {
  position: absolute;
  left: 5px;
  top: 5px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.img-action-btn {
  background: rgba(0,0,0,0.55);
  color: #fff;
  border: none;
  padding: 2px 6px;
  font-size: 10px;
  border-radius: 4px;
  cursor: pointer;
  line-height: 1.1;
  transition: background .2s ease, transform .15s ease;
}

.img-action-btn:hover:not(:disabled) {
  background: rgba(0,0,0,0.8);
  transform: translateY(-1px);
}

.img-action-btn:disabled {
  opacity: .35;
  cursor: default;
}

.img-action-btn.primary {
  background: var(--brand-success);
}

.img-action-btn.primary:hover {
  background: #059669;
}

.drop-zone {
  border: 2px dashed var(--brand-border);
  border-radius: 12px;
  padding: 40px;
  text-align: center;
  transition: all 0.3s ease;
  background: var(--brand-bg-end);
  cursor: pointer;
}

.drop-zone.dragover {
  border-color: var(--brand-success);
  background: rgba(16, 185, 129, 0.1);
}

.drop-zone:hover {
  border-color: var(--brand-success);
  background: rgba(16, 185, 129, 0.1);
}

.drop-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.upload-icon {
  font-size: 3rem;
  color: var(--brand-accent-alt);
}

.upload-btn {
  background: none;
  border: none;
  color: var(--brand-success);
  font-weight: 600;
  cursor: pointer;
  text-decoration: underline;
}

.upload-btn:hover {
  color: #059669;
}

.url-input {
  margin-top: 15px;
  padding-top: 15px;
  border-top: 1px solid var(--brand-border);
}

.url-input label {
  font-size: 0.9rem;
  color: var(--brand-accent-alt);
  margin-bottom: 8px;
}

.discount-info {
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 8px;
  padding: 12px;
  margin-bottom: 20px;
}

.discount-badge {
  color: var(--brand-success);
  font-weight: 600;
  font-size: 0.9rem;
}

.form-actions {
  display: flex;
  gap: 15px;
  justify-content: flex-end;
  margin-top: 30px;
  padding-top: 20px;
  border-top: 2px solid var(--brand-border);
}

/* Responsive */
@media (max-width: 768px) {
  .admin-dashboard {
    padding: 10px;
  }

  .dashboard-header {
    text-align: center;
    margin-bottom: 20px;
  }

  .dashboard-title {
    font-size: 1.6rem;
    line-height: 1.2;
  }

  .dashboard-subtitle {
    font-size: 0.9rem;
  }

  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
  }

  .stat-card {
    padding: 12px;
  }

  .stat-number {
    font-size: 1.3rem;
  }

  .stat-label {
    font-size: 0.75rem;
  }

  .stat-icon {
    font-size: 1.8rem;
    width: 50px;
    height: 50px;
  }

  .tabs-container {
    margin-bottom: 20px;
  }

  .tabs {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 8px;
  }

  .tab {
    padding: 8px 12px;
    font-size: 0.9rem;
    min-width: auto;
  }

  .tab-icon {
    display: none;
  }

  .section-header {
    flex-direction: column;
    gap: 15px;
    align-items: flex-start;
  }

  .section-header h2 {
    font-size: 1.4rem;
  }

  .products-grid {
    grid-template-columns: 1fr;
    gap: 15px;
  }

  .product-card {
    padding: 15px;
  }

  .product-actions {
    flex-direction: column;
    gap: 8px;
  }

  .btn {
    padding: 8px 16px;
    font-size: 0.9rem;
  }

  .form-row {
    grid-template-columns: 1fr;
    gap: 15px;
  }

  .form-actions {
    flex-direction: column;
    gap: 10px;
  }

  .modal {
    margin: 10px;
    max-width: none;
  }

  .modal-header h3 {
    font-size: 1.3rem;
  }

  .modal-body {
    padding: 15px;
  }
}

/* Estilos para la sección de ventas */
.sales-stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.sales-table-container {
  background: var(--brand-bg-end);
  border-radius: 12px;
  padding: 20px;
  border: 1px solid var(--brand-border);
  overflow-x: auto;
}

.sales-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.9rem;
}

.sales-table th {
  background: var(--brand-surface);
  color: var(--brand-primary-contrast);
  font-weight: 600;
  padding: 18px 100px;
  text-align: center;
  border-bottom: 2px solid var(--brand-border);
  white-space: nowrap;
  font-size: 0.95rem;
}

.sales-table td {
  padding: 18px 60px;
  border-bottom: 1px solid var(--brand-border);
  color: var(--brand-accent-alt);
  text-align: center;
}

.sale-row {
  transition: background-color 0.3s ease;
}

.sale-row:hover {
  background: var(--brand-surface);
}

.customer-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.customer-name {
  font-weight: 600;
  color: var(--brand-primary-contrast);
  font-size: 0.85rem;
}

.customer-email {
  font-size: 0.75rem;
  color: var(--brand-accent-alt);
}

.product-name {
  font-weight: 500;
  color: var(--brand-primary-contrast);
  font-size: 0.85rem;
}

.quantity {
  background: var(--brand-accent);
  color: white;
  padding: 4px 8px;
  border-radius: 8px;
  font-weight: 600;
  display: inline-block;
  min-width: 30px;
  text-align: center;
}

.amount {
  font-weight: 700;
  color: var(--brand-success);
  font-size: 0.9rem;
}

.status-badge {
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  display: inline-block;
}

.status-badge.completed {
  background: rgba(34, 197, 94, 0.2);
  color: #22c55e;
  border: 1px solid rgba(34, 197, 94, 0.3);
}

.status-badge.pending {
  background: rgba(251, 191, 36, 0.2);
  color: #f59e0b;
  border: 1px solid rgba(251, 191, 36, 0.3);
}

.status-badge.cancelled {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.date {
  color: var(--brand-accent-alt);
  font-size: 0.75rem;
}

/* Estilos para productos múltiples */
.product-info {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.single-product {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.product-color {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 0.75rem;
  color: var(--brand-accent-alt);
}

.color-dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
}

.multiple-products {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.products-summary {
  display: flex;
  align-items: center;
  gap: 8px;
}

.products-badge {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 0.75rem;
  font-weight: 600;
  display: inline-block;
}

.products-details {
  margin-top: 4px;
}

.products-toggle {
  cursor: pointer;
  color: var(--brand-accent);
  font-size: 0.75rem;
  font-weight: 500;
  padding: 4px 0;
  user-select: none;
  transition: color 0.2s;
}

.products-toggle:hover {
  color: var(--brand-primary-contrast);
}

.products-toggle::marker {
  color: var(--brand-accent);
}

.products-list {
  list-style: none;
  padding: 8px 0 0 0;
  margin: 4px 0 0 0;
  display: flex;
  flex-direction: column;
  gap: 6px;
  border-top: 1px solid var(--brand-border);
}

.product-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 8px;
  background: var(--brand-surface);
  border-radius: 6px;
  font-size: 0.8rem;
}

.item-name {
  flex: 1;
  font-weight: 500;
  color: var(--brand-primary-contrast);
}

.item-quantity {
  background: var(--brand-accent);
  color: white;
  padding: 2px 8px;
  border-radius: 10px;
  font-size: 0.7rem;
  font-weight: 600;
}

.item-color {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 0.7rem;
  color: var(--brand-accent-alt);
}

.color-dot-small {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border: 1.5px solid #fff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

/* Estilos para cantidad mejorada */
.quantity-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2px;
}

.quantity-header {
  font-size: 0.7rem;
  font-weight: 600;
  color: var(--brand-accent);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  text-align: center;
  width: 100%;
}

.quantity-badge {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  color: white;
  padding: 6px 12px;
  border-radius: 12px;
  font-weight: 700;
  font-size: 0.9rem;
  display: inline-block;
  min-width: 40px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(245, 87, 108, 0.3);
}

.quantity-sublabel {
  font-size: 0.65rem;
  color: var(--brand-accent-alt);
  text-align: left;
  line-height: 1.2;
  width: 100%;
}

.quantity-label {
  font-size: 0.65rem;
  color: var(--brand-accent-alt);
  text-align: center;
  line-height: 1.2;
}

@media (max-width: 768px) {
  .sales-stats {
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
    margin-bottom: 20px;
  }

  .stat-card {
    padding: 12px;
  }

  .sales-table-container {
    overflow-x: scroll;
    padding: 12px;
    border-radius: 8px;
  }

  .sales-table {
    min-width: 400px;
    font-size: 0.75rem;
  }

  .sales-table th:last-child,
  .sales-table td:last-child {
    min-width: 80px;
    width: 80px;
  }

  .sales-table th {
    padding: 8px 6px;
    font-size: 0.7rem;
  }

  .sales-table td {
    padding: 8px 6px;
  }

  .customer-info {
    gap: 1px;
  }

  .customer-name {
    font-size: 0.8rem;
  }

  .customer-email {
    font-size: 0.7rem;
  }

  .product-name {
    font-size: 0.8rem;
  }

  .quantity {
    padding: 2px 6px;
    font-size: 0.7rem;
    min-width: 24px;
  }

  .amount {
    font-size: 0.8rem;
  }

  .status-badge {
    padding: 4px 8px;
    font-size: 0.65rem;
  }

  .date {
    font-size: 0.7rem;
  }
}

/* Estilos para la sección de showcase/novedades */
.showcase-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 20px;
}

.showcase-card {
  background: var(--brand-bg-end);
  border-radius: 16px;
  padding: 20px;
  border: 2px solid var(--brand-border);
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
}

.showcase-card:hover {
  border-color: var(--brand-accent);
  box-shadow: 0 8px 25px rgba(59, 130, 246, 0.3);
  transform: translateY(-2px);
}

.showcase-image {
  width: 100%;
  height: 180px;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 15px;
  background: var(--brand-border);
}

.showcase-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.showcase-info {
  flex: 1;
  margin-bottom: 15px;
}

.showcase-info h3 {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--brand-primary-contrast);
  margin: 0 0 8px;
}

.showcase-description {
  color: var(--brand-accent-alt);
  font-size: 0.9rem;
  margin: 0 0 15px;
  line-height: 1.4;
}

.showcase-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
}

.showcase-category {
  background: var(--brand-accent);
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
}

.showcase-status {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
}

.showcase-status.available {
  background: rgba(34, 197, 94, 0.2);
  color: #22c55e;
  border: 1px solid rgba(34, 197, 94, 0.3);
}

.showcase-status.unavailable {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.showcase-actions {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  color: var(--brand-primary-contrast);
  font-weight: 500;
}

.checkbox-label input[type="checkbox"] {
  width: 18px;
  height: 18px;
  accent-color: var(--brand-success);
}

/* Estilos para upload de imágenes en showcase */
.image-tabs {
  display: flex;
  margin-bottom: 1rem;
  border-radius: 8px;
  overflow: hidden;
  border: 1px solid #333;
}

.tab-btn {
  flex: 1;
  padding: 0.75rem 1rem;
  background: #2a2a2a;
  color: rgba(255, 255, 255, 0.7);
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 500;
}

.tab-btn.active {
  background: var(--brand-success);
  color: white;
}

.tab-btn:hover:not(.active) {
  background: #333;
  color: rgba(255, 255, 255, 0.9);
}

.image-input-section {
  margin-top: 0.5rem;
}

/* Estilos para múltiples URLs de imágenes */
.field-hint {
  font-size: 0.85rem;
  color: rgba(255, 255, 255, 0.6);
  margin-top: 0.25rem;
  margin-bottom: 1rem;
}

.image-urls-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.image-url-item {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  padding: 1rem;
  background: rgba(0, 0, 0, 0.2);
  border-radius: 12px;
  border: 1px solid var(--brand-border);
}

.url-input-wrapper {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.url-index {
  flex-shrink: 0;
  width: 28px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--brand-success);
  color: white;
  border-radius: 50%;
  font-weight: 600;
  font-size: 0.85rem;
}

.url-input {
  flex: 1;
}

.btn-remove-url {
  flex-shrink: 0;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(239, 68, 68, 0.9);
  color: white;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.btn-remove-url:hover {
  background: #dc2626;
  transform: scale(1.1);
}

.url-preview {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding-top: 0.5rem;
  border-top: 1px solid var(--brand-border);
}

.url-preview img {
  width: 80px;
  height: 80px;
  object-fit: cover;
  border-radius: 8px;
  border: 2px solid var(--brand-border);
}

.url-actions {
  display: flex;
  gap: 0.5rem;
}

.btn-reorder {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--brand-surface);
  color: var(--brand-primary-contrast);
  border: 1px solid var(--brand-border);
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.btn-reorder:hover:not(:disabled) {
  background: var(--brand-accent);
  transform: translateY(-2px);
}

.btn-reorder:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.btn-add-url {
  width: 100%;
  padding: 0.75rem;
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.2), rgba(16, 185, 129, 0.1));
  color: var(--brand-success);
  border: 2px dashed var(--brand-success);
  border-radius: 12px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.btn-add-url:hover {
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.3), rgba(16, 185, 129, 0.15));
  transform: translateY(-2px);
}

.file-input {
  display: none;
}

.file-upload-area {
  border: 2px dashed #333;
  border-radius: 12px;
  padding: 2rem;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  background: #1a1a1a;
}

.file-upload-area:hover {
  border-color: var(--brand-success);
  background: rgba(16, 185, 129, 0.05);
}

.upload-placeholder {
  color: rgba(255, 255, 255, 0.7);
}

.upload-placeholder svg {
  color: #555;
  margin-bottom: 1rem;
}

.upload-placeholder p {
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: rgba(255, 255, 255, 0.8);
}

.upload-placeholder span {
  font-size: 0.9rem;
  color: rgba(255, 255, 255, 0.5);
}

.image-preview {
  position: relative;
  display: inline-block;
}

.image-preview img {
  max-width: 200px;
  max-height: 150px;
  border-radius: 8px;
  object-fit: cover;
}

.remove-image {
  position: absolute;
  top: -8px;
  right: -8px;
  background: #ef4444;
  color: white;
  border: none;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  font-size: 0.7rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.remove-image:hover {
  background: #dc2626;
  transform: scale(1.1);
}

@media (max-width: 1400px) {
  .showcase-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

@media (max-width: 1000px) {
  .showcase-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (max-width: 768px) {
  .showcase-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 480px) {
  .admin-dashboard {
    padding: 8px;
  }

  .dashboard-title {
    font-size: 1.4rem;
  }

  .dashboard-subtitle {
    font-size: 0.85rem;
  }

  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
  }

  .stat-card {
    padding: 10px;
  }

  .stat-number {
    font-size: 1.1rem;
  }

  .stat-label {
    font-size: 0.7rem;
  }

  .stat-icon {
    font-size: 1.5rem;
    width: 40px;
    height: 40px;
  }

  .tabs {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 8px;
  }

  .tab {
    padding: 10px;
    text-align: center;
    border-radius: 8px;
  }

  .section-header h2 {
    font-size: 1.2rem;
  }

  .product-card {
    padding: 12px;
  }

  .product-info h3 {
    font-size: 1rem;
  }

  .product-description {
    font-size: 0.8rem;
    line-height: 1.3;
  }

  .price {
    font-size: 0.9rem;
  }

  .btn {
    padding: 6px 12px;
    font-size: 0.8rem;
  }

  .modal-header h3 {
    font-size: 1.1rem;
  }

  .form-group label {
    font-size: 0.9rem;
  }

  .form-input,
  .form-textarea,
  .form-select {
    padding: 8px;
    font-size: 0.9rem;
  }

  .showcase-grid {
    grid-template-columns: 1fr;
    gap: 15px;
  }

  .showcase-card {
    padding: 15px;
  }

  .showcase-image {
    height: 160px;
  }

  .showcase-info h3 {
    font-size: 1rem;
  }

  .showcase-description {
    font-size: 0.8rem;
  }

  .showcase-meta {
    flex-direction: column;
    align-items: flex-start;
    gap: 8px;
  }

  .file-upload-area {
    padding: 1.2rem;
  }

  .upload-placeholder svg {
    width: 28px;
    height: 28px;
  }

  .upload-text {
    font-size: 0.8rem;
  }

  /* Sales section optimizations for very small screens */
  .sales-stats {
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    margin-bottom: 15px;
  }

  .sales-table-container {
    padding: 8px;
    margin: 0 -8px;
  }

  .sales-table {
    min-width: 350px;
    font-size: 0.7rem;
  }

  .sales-table th:last-child,
  .sales-table td:last-child {
    min-width: 90px;
    width: 90px;
  }

  .sales-table th {
    padding: 6px 4px;
    font-size: 0.65rem;
  }

  .sales-table td {
    padding: 6px 4px;
  }

  .customer-name {
    font-size: 0.75rem;
  }

  .customer-email {
    font-size: 0.65rem;
  }

  .product-name {
    font-size: 0.75rem;
  }

  .quantity {
    padding: 2px 4px;
    font-size: 0.65rem;
    min-width: 20px;
  }

  .amount {
    font-size: 0.75rem;
  }

  .status-badge {
    padding: 3px 6px;
    font-size: 0.6rem;
  }

  .date {
    font-size: 0.65rem;
  }
}

/* Color Mode Selector Styles */
.color-mode-selector {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.mode-btn {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1.25rem;
  border: 2px solid var(--brand-border);
  border-radius: 12px;
  background: var(--brand-bg-end);
  cursor: pointer;
  transition: all 0.3s ease;
  text-align: left;
}

.mode-btn:hover {
  border-color: var(--brand-accent);
  background: var(--brand-surface);
  transform: translateY(-2px);
}

.mode-btn.active {
  border-color: var(--brand-success);
  background: rgba(16, 185, 129, 0.1);
  box-shadow: 0 0 0 3px rgba(16, 185, 129, 0.2);
}

.mode-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.mode-text {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.mode-text strong {
  color: var(--brand-primary-contrast);
  font-size: 1rem;
  font-weight: 600;
}

.mode-text span {
  color: var(--brand-accent-alt);
  font-size: 0.85rem;
}

.color-hint {
  padding: 0.75rem 1rem;
  background: rgba(59, 130, 246, 0.1);
  border-radius: 8px;
  color: #60a5fa;
  font-size: 0.9rem;
  text-align: center;
  margin-top: 1rem;
}

.color-image-note {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem;
  background: rgba(59, 130, 246, 0.1);
  border-radius: 8px;
  border-left: 4px solid #3b82f6;
  margin-top: 1rem;
}

.note-icon {
  font-size: 1.25rem;
  flex-shrink: 0;
}

.color-image-note p {
  margin: 0;
  color: var(--brand-primary-contrast);
  font-size: 0.9rem;
  line-height: 1.5;
}

/* Agregar color personalizado */
.add-custom-color {
  margin-bottom: 1.5rem;
  padding: 1rem;
  background: rgba(59, 130, 246, 0.05);
  border-radius: 10px;
  border: 1px dashed rgba(59, 130, 246, 0.3);
}

.custom-color-inputs {
  display: flex;
  gap: 0.75rem;
  align-items: center;
}

.custom-color-input {
  flex: 1;
  min-width: 0;
}

.color-picker {
  width: 60px;
  height: 44px;
  border: 2px solid var(--brand-border);
  border-radius: 8px;
  cursor: pointer;
  background: var(--brand-bg-end);
  transition: all 0.2s ease;
}

.color-picker:hover {
  border-color: var(--brand-accent);
  transform: scale(1.05);
}

.btn-add-color {
  padding: 0.75rem 1.25rem;
  background: var(--brand-success);
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
  box-shadow: 0 2px 8px rgba(16, 185, 129, 0.3);
}

.btn-add-color:hover:not(:disabled) {
  background: #059669;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.4);
}

.btn-add-color:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.remove-custom-color {
  position: absolute;
  top: 1px;
  right: 1px;
  background: rgba(239, 68, 68, 0.9);
  color: white;
  border: none;
  border-radius: 50%;
  width: 18px;
  height: 18px;
  font-size: 11px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: all 0.2s ease;
  z-index: 10;
}

.color-option {
  position: relative;
}

.color-option:hover .remove-custom-color {
  opacity: 1;
}

.remove-custom-color:hover {
  background: rgba(239, 68, 68, 1);
  transform: scale(1.1);
}

@media (max-width: 768px) {
  .color-mode-selector {
    grid-template-columns: 1fr;
  }

  .mode-text strong {
    font-size: 0.9rem;
  }

  .mode-text span {
    font-size: 0.75rem;
  }

  .custom-color-inputs {
    flex-direction: column;
  }

  .custom-color-input {
    width: 100%;
  }

  .btn-add-color {
    width: 100%;
  }
}
</style>
