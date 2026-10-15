<script setup>
import { computed, nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import {
  ArrowLeft, ArrowRight, Check, ChevronDown, ChevronRight, CircleUserRound, CreditCard,
  Clock3, Heart, Leaf, MapPin, Menu, Minus, PackageCheck, Pencil, Phone, Plus,
  Scale, Search, ShieldCheck, ShoppingBag, SlidersHorizontal, Trash2, Truck, X, Zap
} from 'lucide-vue-next'
import StatusBadge from './components/StatusBadge.vue'
import ConfirmModal from './components/ConfirmModal.vue'
import OrderTable from './components/OrderTable.vue'
import PolicyModals from './components/policies/PolicyModals.vue'

const activeCategory = ref('ყველა')
const visibleCount = ref(12)
const search = ref('')
const searchFocused = ref(false)
const cartCount = ref(2)
const wishlist = ref(new Set([2]))
const selectedProduct = ref(null)
const detailQty = ref(1)
const selectedProductImageIndex = ref(0)
const detailTab = ref('description')
const productFaqOpen = ref(0)
const zoomOpen = ref(false)
const cartPage = ref(false)
const checkoutStep = ref(0)
const adminPage = ref(false)
const adminTab = ref('overview')
const orderPlaced = ref(false)
const placedOrder = ref(null)
const paymentResult = ref(null)
const paymentResultOrder = ref('')
const orderSaving = ref(false)
const orderError = ref('')
const apiOrders = ref([])
const ordersLoading = ref(false)
const apiUsers = ref([])
const usersLoading = ref(false)
const userSaving = ref(false)
const userRoleSaving = ref(null)
const userActionSaving = ref(null)
const showUserForm = ref(false)
const userForm = ref({
  firstName: '',
  lastName: '',
  phone: '',
  email: '',
  password: '',
  role: 'user'
})
const userError = ref('')
const activityLogs = ref([])
const activityLogsLoading = ref(false)
const activityLogsError = ref('')
const selectedAdminOrder = ref(null)
const orderStatusSaving = ref(false)
const refundSaving = ref(false)
const refundConfirmOpen = ref(false)
const refundForm = ref({ amount: '', reason: '' })
const orderReportPeriod = ref('month')
const orderReportFrom = ref('')
const orderReportTo = ref('')
const paymentMethod = ref('bog-installment')
const deliveryMethod = ref('courier')
const checkoutForm = ref({
  firstName: '',
  lastName: '',
  phone: '',
  email: '',
  city: 'თბილისი',
  address: '',
  note: '',
  pickupBranch: 'tbilisi'
})
const mobileMenu = ref(false)
const catalogOpen = ref(false)
const activeCatalogCategoryName = ref('')
const addedToast = ref(false)
const compareIds = ref([])
const comparePage = ref(false)
const productsPage = ref(false)
const accountPage = ref(false)
const userOrders = ref([])
const userOrdersLoading = ref(false)
const selectedUserOrder = ref(null)
const profileEditing = ref(false)
const profileForm = ref({
  firstName: '',
  lastName: '',
  phone: '',
  email: '',
  city: '',
  region: '',
  street: '',
  building: '',
  addressNote: ''
})
const filterOpen = ref(false)
const expandedFilterCategories = ref(new Set())
const minPrice = ref('')
const maxPrice = ref('')
const selectedBrand = ref('ყველა')
const onlyInStock = ref(false)
const onlyDiscounted = ref(false)
const sortBy = ref('popular')
const stockSearch = ref('')
const stockFilter = ref('all')
const stockVisibleCount = ref(24)
const adminPageSize = 10
const ordersPage = ref(1)
const usersPage = ref(1)
const logsPage = ref(1)
const applyingHistoryState = ref(false)
const apiBaseUrl = import.meta.env.VITE_API_URL
const appBaseUrl = import.meta.env.BASE_URL || '/'
const bogClientId = import.meta.env.VITE_BOG_CLIENT_ID || '10002550'
let bogSdkPromise = null
const publicAssetUrl = (path) => `${appBaseUrl}${path.replace(/^\/+/, '')}`
const categoryImageRules = [
  { keys: ['მოტობლოკ', 'მოტოკულტივატორ'], file: 'motoblokebi.jpg' },
  { keys: ['კულტივატორ'], file: 'kultivatori.jpg' },
  { keys: ['გენერატორ'], file: 'generatorebi.jpg' },
  { keys: ['ბენზოხერხ', 'ხერხ'], file: 'benzoxerxebi.jpg' },
  { keys: ['ტუმბ', 'ტურბ'], file: 'tumblobi.jpg' },
  { keys: ['სათიბ', 'გაზონ', 'ბაღის', 'ბალახ'], file: 'bagis-teqnika.jpg' },
  { keys: ['საბურავ'], file: 'saburavebi.jpg' },
  { keys: ['საწველ', 'მწველ', 'რძის'], file: 'sawveli-aparatebi.png' },
  { keys: ['შესასხურ', 'შემასხურ', 'აპარატ'], file: 'shesasxureblebi.jpg' },
  { keys: ['ტრაქტორ'], file: 'traqtorebi.jpg' },
  { keys: ['კვადროციკლ', 'atv'], file: 'kvadrociklebi.png' },
  { keys: ['მიწის ბურღ', 'ბურღ'], file: 'miwis-burgi.png' },
  { keys: ['საფქვავ', 'ფქვ', 'წისქვილ'], file: 'safqvavebi.png' },
  { keys: ['შემდუღ', 'სვარკ', 'welder'], file: 'shemdugebeli.png' },
  { keys: ['კუთხსახეხ', 'სახეხ'], file: 'kutxsaxexebi.jpg' },
  { keys: ['ნაწილ', 'სათადარიგ', 'სხვადასხვა'], file: 'nawilebi.jpg' }
]
function categoryImage(name = '') {
  const normalized = String(name).trim().toLowerCase()
  const match = categoryImageRules.find(rule => rule.keys.some(key => normalized.includes(key)))
  return publicAssetUrl(`category-images/${match?.file || 'default.jpg'}`)
}
const authHeaders = () => currentUser.value?.token
  ? { Authorization: `Bearer ${currentUser.value.token}` }
  : {}
const apiProducts = ref([])
const apiCategories = ref([])
const apiBrands = ref([])
const productsLoading = ref(false)
const productsError = ref('')
const productSaving = ref(false)
const productDeleting = ref(false)
const categoriesError = ref('')
const brandsError = ref('')
const showAddProduct = ref(false)
const showDiscountForm = ref(false)
const discountSaving = ref(false)
const discountForm = ref({ productId: '', regularPrice: '', salePrice: '', percent: '' })
const discountProductSearch = ref('')
const discountProductPickerOpen = ref(false)
const categorySaving = ref(false)
const categoryNameInput = ref(null)
const categoryForm = ref({ id: null, name: '', parentId: '', sortOrder: 0, imageUrl: '', imageFile: null })
const brandSaving = ref(false)
const brandForm = ref({ id: null, name: '', sortOrder: 0 })
const brandDeleteTarget = ref(null)
const brandDeleting = ref(false)
const categoryDeleteTarget = ref(null)
const categoryDeleting = ref(false)
const privacyConsentOpen = ref(false)
const privacyConsentExpanded = ref(false)
const returnPolicyOpen = ref(false)
const warrantyPolicyOpen = ref(false)
const installmentPolicyOpen = ref(false)
const paymentPolicyOpen = ref(false)
const deliveryPolicyOpen = ref(false)
const termsPolicyOpen = ref(false)
const termsPolicyText = ref('')
const termsPolicyLoading = ref(false)
const adminNotice = ref('')
let adminNoticeTimer = null
const authOpen = ref(false)
const authMode = ref('login')
const authSaving = ref(false)
const authError = ref('')
const authStorageKey = 'agrotradeUser'
const cartStorageKey = 'agrotradeCart'
const pageStateStorageKey = 'agrotradePageState'
const privacyConsentCookie = 'agrotradePrivacyConsent'
const authSessionMs = 1000 * 60 * 60 * 8
const companyStartYear = 2019
const companyYearsOnMarket = computed(() => Math.max(0, new Date().getFullYear() - companyStartYear))
const heroOfferIndex = ref(0)
let authLogoutTimer = null
let heroOfferTimer = null
const currentUser = ref(readStoredUser())
syncProfileForm()
const authForm = ref({
  firstName: '',
  lastName: '',
  phone: '',
  email: '',
  password: '',
  confirmPassword: ''
})
const productForm = ref({
  id: null,
  name: '',
  sku: '',
  price: '',
  regularPrice: '',
  salePrice: '',
  stockQuantity: '',
  brandId: '',
  categoryId: '',
  categoryNames: '',
  imageUrl: '',
  imageUrls: [],
  imageFiles: [],
  imagePreviews: [],
  shortDescription: '',
  specifications: [
    { key: '', value: '' }
  ]
})
const productImageFallback = 'https://agro-trade.ge/wp-content/uploads/2022/01/lawnmower.png'

const specificationPresets = {
  'მოტობლოკები': ['ბრენდი', 'ძრავის მოდელი', 'სიმძლავრე', 'საწვავის ტიპი', 'საწვავის ტევადობა', 'გადაცემათა კოლოფი', 'დამუშავების სიგანე', 'დამუშავების სიღრმე', 'საბურავების ზომა', 'წონა', 'გარანტია'],
  'ტრაქტორები': ['ბრენდი', 'სიმძლავრე', 'ძრავის ტიპი', 'წამყვანი სისტემა', 'გადაცემათა კოლოფი', 'საბურავების ზომა', 'წონა', 'გარანტია'],
  'კუთხსახეხები': ['ვატობა', 'დისკის დიამეტრი', 'ბრუნი წუთში', 'წონა', 'გარანტია'],
  'გენერატორები': ['სიმძლავრე', 'ძაბვა', 'საწვავის ტიპი', 'ავზის მოცულობა', 'სტარტი', 'წონა'],
  'ბენზოხერხები': ['სიმძლავრე', 'ძრავის მოცულობა', 'შინის სიგრძე', 'საწვავის ტიპი', 'წონა']
}

const categories = computed(() => {
  return apiCategories.value.map(category => ({
    name: category.name,
    image: category.imageUrl || categoryImage(category.name),
    count: category.productCount || countCategoryProducts(category.name)
  }))
})
const flatCategories = computed(() => flattenCategories(apiCategories.value))
const catalogCategories = computed(() => {
  return apiCategories.value
})
const categoryFilterItems = computed(() => [
  { name: 'ყველა', level: 0, count: allProducts.value.length },
  ...flatCategories.value.map(category => ({
    name: category.name,
    level: category.level,
    count: countCategoryTreeProducts(category)
  }))
])
const topCategoryTabs = computed(() => categoryFilterItems.value.slice(0, 6).map(category => category.name))
const selectedCatalogCategory = computed(() => {
  return catalogCategories.value.find(category => category.name === activeCatalogCategoryName.value)
    || catalogCategories.value.find(category => category.children?.length)
    || catalogCategories.value[0]
})

const cartItems = ref(readStoredCart())

const products = computed(() => apiProducts.value)
const visibleProducts = computed(() => products.value.filter(product => !product.isHidden))
const allProducts = computed(() => visibleProducts.value)
const discountProducts = computed(() => visibleProducts.value.filter(product => product.oldPrice))
const heroOfferProducts = computed(() => {
  const topProducts = [...discountProducts.value]
    .filter(product => Number(product.price || 0) > 0 && Number(product.oldPrice || 0) > Number(product.price || 0))
    .sort((a, b) => discountPercent(b) - discountPercent(a))
    .slice(0, 3)
  return topProducts
})
const heroOfferProduct = computed(() => heroOfferProducts.value[heroOfferIndex.value] || heroOfferProducts.value[0] || null)
const hasHeroOffer = computed(() => heroOfferProducts.value.length > 0)
const heroOfferLabel = computed(() => heroOfferProduct.value?.oldPrice ? 'ყველაზე დიდი ფასდაკლება' : '')
const discountSelectedProduct = computed(() => products.value.find(product => String(product.backendId) === String(discountForm.value.productId)))
const discountProductOptions = computed(() => {
  const query = discountProductSearch.value.trim().toLowerCase()
  return products.value
    .filter(product => product.backendId && !product.isHidden)
    .filter(product => {
      if (!query) return true
      return [product.name, product.sku, product.category, product.brand]
        .filter(Boolean)
        .some(value => String(value).toLowerCase().includes(query))
    })
})
const stockProducts = computed(() => products.value
  .filter(product => {
    const query = stockSearch.value.trim().toLowerCase()
    const matchesSearch = !query || [product.name, product.sku, product.category, product.brand]
      .filter(Boolean)
      .some(value => String(value).toLowerCase().includes(query))
    const stock = Number(product.stock || 0)
    const matchesFilter = stockFilter.value === 'low'
      ? stock > 0 && stock <= 2
      : stockFilter.value === 'out'
        ? stock <= 0
        : true
    return matchesSearch && matchesFilter
  })
  .sort(sortByStockPriority))
const displayedStockProducts = computed(() => stockProducts.value.slice(0, stockVisibleCount.value))
const lowStockPreview = computed(() => products.value
  .filter(product => Number(product.stock || 0) <= 2)
  .sort(sortByStockPriority)
  .slice(0, 5))

function stockPriority(product) {
  const stock = Number(product?.stock || 0)
  if (stock <= 0) return 0
  if (stock <= 2) return 1
  return 2
}

function sortByStockPriority(a, b) {
  return stockPriority(a) - stockPriority(b)
    || Number(a.stock || 0) - Number(b.stock || 0)
    || a.name.localeCompare(b.name, 'ka')
}

const brands = computed(() => apiBrands.value.map(brand => brand.name))
const brandFilterOptions = computed(() => ['ყველა', ...new Set([...brands.value, 'სხვა'])])
const openFaq = ref(0)

function readStoredUser() {
  try {
    const storedUser = JSON.parse(localStorage.getItem(authStorageKey) || 'null')
    if (!storedUser) return null

    if (storedUser.expiresAt && storedUser.expiresAt <= Date.now()) {
      localStorage.removeItem(authStorageKey)
      return null
    }

    if (!storedUser.expiresAt) {
      const userWithExpiry = { ...storedUser, expiresAt: Date.now() + authSessionMs }
      localStorage.setItem(authStorageKey, JSON.stringify(userWithExpiry))
      return userWithExpiry
    }

    return storedUser
  } catch {
    localStorage.removeItem(authStorageKey)
    return null
  }
}

function persistCurrentUser() {
  if (!currentUser.value) {
    localStorage.removeItem(authStorageKey)
    scheduleAutoLogout()
    return
  }

  currentUser.value = {
    ...currentUser.value,
    expiresAt: Date.now() + authSessionMs
  }
  localStorage.setItem(authStorageKey, JSON.stringify(currentUser.value))
  scheduleAutoLogout()
}

function scheduleAutoLogout() {
  if (authLogoutTimer) {
    window.clearTimeout(authLogoutTimer)
    authLogoutTimer = null
  }

  if (!currentUser.value?.expiresAt) return

  const delay = currentUser.value.expiresAt - Date.now()
  if (delay <= 0) {
    logout()
    return
  }

  authLogoutTimer = window.setTimeout(() => logout(), delay)
}

function readStoredCart() {
  try {
    const storedCart = JSON.parse(localStorage.getItem(cartStorageKey) || '[]')
    return Array.isArray(storedCart) ? storedCart : []
  } catch {
    localStorage.removeItem(cartStorageKey)
    return []
  }
}

function clearCart() {
  cartItems.value = []
  localStorage.removeItem(cartStorageKey)
}

function hasCookie(name) {
  return document.cookie
    .split(';')
    .map(cookie => cookie.trim())
    .some(cookie => cookie.startsWith(`${name}=`))
}

function setCookie(name, value, maxAgeSeconds) {
  document.cookie = `${name}=${encodeURIComponent(value)}; Max-Age=${maxAgeSeconds}; Path=/; SameSite=Lax`
}

function acceptPrivacyConsent() {
  setCookie(privacyConsentCookie, 'accepted', 60 * 60 * 24 * 365)
  privacyConsentOpen.value = false
  privacyConsentExpanded.value = false
}

function closePolicyModal(policy) {
  const policyState = {
    terms: termsPolicyOpen,
    installment: installmentPolicyOpen,
    payment: paymentPolicyOpen,
    delivery: deliveryPolicyOpen,
    warranty: warrantyPolicyOpen,
    return: returnPolicyOpen,
    privacy: privacyConsentOpen
  }[policy]

  if (policyState) policyState.value = false
}

async function openTermsPolicy() {
  termsPolicyOpen.value = true
  if (termsPolicyText.value || termsPolicyLoading.value) return

  termsPolicyLoading.value = true
  try {
    const response = await fetch(publicAssetUrl('terms-and-conditions.txt'))
    termsPolicyText.value = await response.text()
  } catch {
    termsPolicyText.value = 'წესები და პირობების ტექსტის ჩატვირთვა ვერ მოხერხდა.'
  } finally {
    termsPolicyLoading.value = false
  }
}

function getProductBrand(product) {
  if (product.brand) return product.brand
  return brands.value.find(brand => product.name.toUpperCase().includes(brand.toUpperCase())) || 'სხვა'
}

const filteredProducts = computed(() => allProducts.value.filter(p => {
  const categoryMatch = activeCategory.value === 'ყველა' || productMatchesCategories(p, activeCategoryNames.value)
  const searchMatch = p.name.toLowerCase().includes(search.value.toLowerCase())
  const minMatch = !minPrice.value || p.price >= Number(minPrice.value)
  const maxMatch = !maxPrice.value || p.price <= Number(maxPrice.value)
  const brandMatch = selectedBrand.value === 'ყველა' || getProductBrand(p) === selectedBrand.value
  const stockMatch = !onlyInStock.value || p.stock > 0
  const discountMatch = !onlyDiscounted.value || Boolean(p.oldPrice)
  return categoryMatch && searchMatch && minMatch && maxMatch && brandMatch && stockMatch && discountMatch
}).sort((a, b) => {
  if (sortBy.value === 'price-low') return a.price - b.price
  if (sortBy.value === 'price-high') return b.price - a.price
  if (sortBy.value === 'discount') return (b.oldPrice ? b.oldPrice - b.price : 0) - (a.oldPrice ? a.oldPrice - a.price : 0)
  if (sortBy.value === 'new') return (b.sortKey ?? 0) - (a.sortKey ?? 0)
  return (b.sortKey ?? 0) - (a.sortKey ?? 0)
}))
const displayedProducts = computed(() => filteredProducts.value.slice(0, visibleCount.value))
const searchResults = computed(() => search.value.trim().length < 2 ? [] : allProducts.value.filter(p => p.name.toLowerCase().includes(search.value.trim().toLowerCase()) || p.category.toLowerCase().includes(search.value.trim().toLowerCase())).slice(0, 6))
const cartSubtotal = computed(() => cartItems.value.reduce((sum, item) => sum + item.price * item.qty, 0))
const deliveryPrice = computed(() => {
  if (!cartItems.value.length || deliveryMethod.value === 'pickup' || cartSubtotal.value > 300) return 0
  const destination = `${checkoutForm.value.city} ${checkoutForm.value.address}`.trim().toLowerCase()
  if (!destination) return 6
  if (destination.includes('თბილის')) return 6
  if (destination.includes('სოფ')) return 15
  return 10
})
const orderTotal = computed(() => cartSubtotal.value + deliveryPrice.value)
const deliveryPriceText = computed(() => deliveryPrice.value ? `${deliveryPrice.value.toLocaleString()} ₾` : 'უფასო')
const deliveryNoteText = computed(() => {
  if (deliveryMethod.value === 'pickup') return 'ფილიალიდან გატანა უფასოა.'
  if (cartSubtotal.value > 300) return '300₾-ზე მეტი შეკვეთისთვის მიწოდება უფასოა.'
  if (!`${checkoutForm.value.city} ${checkoutForm.value.address}`.trim()) return 'ქალაქის შევსების შემდეგ მიწოდების საფასური დაზუსტდება.'
  return `მიწოდების საფასური: ${deliveryPriceText.value}`
})
const compareProducts = computed(() => compareIds.value.map(id => allProducts.value.find(product => product.id === id)).filter(Boolean))
const bestSellerProducts = computed(() => allProducts.value.slice(0, 4))
const relatedProducts = computed(() => {
  if (!selectedProduct.value) return []
  const sameCategory = allProducts.value.filter(product => product.category === selectedProduct.value.category && product.id !== selectedProduct.value.id)
  const fallback = [...allProducts.value]
    .filter(product => product.id !== selectedProduct.value.id && !sameCategory.some(item => item.id === product.id))
    .sort((a, b) => (b.sortKey ?? 0) - (a.sortKey ?? 0))
  return [...sameCategory, ...fallback].slice(0, 4)
})
const cartRecommendations = computed(() => allProducts.value.filter(product => !cartItems.value.some(item => item.id === product.id)).slice(0, 3))
watch(cartItems, () => {
  if (cartItems.value.length) {
    localStorage.setItem(cartStorageKey, JSON.stringify(cartItems.value))
  } else {
    localStorage.removeItem(cartStorageKey)
  }
}, { deep: true })
const monthlyPayment = computed(() => selectedProduct.value ? Math.ceil(selectedProduct.value.price / 24) : 0)
const todayKey = computed(() => new Date().toISOString().slice(0, 10))
const todaysOrders = computed(() => adminOrders.value.filter(order => new Date(order.createdAt).toISOString().slice(0, 10) === todayKey.value))
const adminStats = computed(() => [
  {
    label: 'დღის გაყიდვები',
    value: `${todaysOrders.value.reduce((sum, order) => sum + Number(order.total || 0), 0).toLocaleString()} ₾`,
    note: `${todaysOrders.value.length} შეკვეთა დღეს`
  },
  {
    label: 'ახალი შეკვეთები',
    value: adminOrders.value.filter(order => order.statusValue === 'new').length.toLocaleString(),
    note: `${adminOrders.value.filter(order => order.statusValue === 'processing').length} პროცესშია`
  },
  {
    label: 'პროდუქტები',
    value: products.value.length.toLocaleString(),
    note: `${products.value.filter(product => Number(product.stock || 0) <= 2).length} იწურება`
  },
  {
    label: 'მომხმარებლები',
    value: apiUsers.value.length.toLocaleString(),
    note: `${apiUsers.value.filter(user => user.role === 'user').length} user ანგარიში`
  }
])
const selectedProductImages = computed(() => {
  if (!selectedProduct.value) return []
  const images = selectedProduct.value.imageUrls?.length
    ? selectedProduct.value.imageUrls
    : [selectedProduct.value.image]
  return images.filter(Boolean).slice(0, 3)
})
const selectedProductImage = computed(() => {
  return selectedProductImages.value[selectedProductImageIndex.value] || selectedProduct.value?.image || productImageFallback
})
const selectedProductDescription = computed(() => {
  return selectedProduct.value?.description?.trim()
    || 'ძლიერი და საიმედო ტექნიკა ყოველდღიური სამუშაოებისთვის. ეკონომიური ძრავი, გამძლე კონსტრუქცია და მარტივი მართვა.'
})

function formatMoney(value) {
  return Number(value || 0).toLocaleString('ka-GE', {
    maximumFractionDigits: 2
  })
}

function discountAmount(product) {
  return Math.max(0, Number(product?.oldPrice || 0) - Number(product?.price || 0))
}

function discountPercent(product) {
  const oldPrice = Number(product?.oldPrice || 0)
  const price = Number(product?.price || 0)
  return oldPrice > price && oldPrice > 0 ? Math.round((oldPrice - price) / oldPrice * 100) : 0
}
const adminOrders = computed(() => apiOrders.value)
const reportPeriodOptions = [
  { value: 'month', label: 'თვე' },
  { value: 'quarter', label: 'კვარტალი' },
  { value: 'year', label: 'წელი' },
  { value: 'custom', label: 'პერიოდი' }
]
const orderStatusOptions = [
  { value: 'new', label: 'ახალი' },
  { value: 'processing', label: 'პროცესშია' },
  { value: 'completed', label: 'დასრულებულია' },
  { value: 'cancelled', label: 'გაუქმებულია' }
]
const adminNavItems = computed(() => [
  { id: 'overview', label: '▦ მიმოხილვა' },
  { id: 'products', label: '▣ პროდუქტები', count: products.value.length },
  { id: 'orders', label: '▤ შეკვეთები', count: apiOrders.value.length },
  { id: 'users', label: '◉ მომხმარებლები', count: apiUsers.value.length },
  { id: 'logs', label: '☷ ლოგები', count: activityLogs.value.length },
  { id: 'categories', label: '◇ კატეგორიები', count: flatCategories.value.length },
  { id: 'brands', label: '◈ ბრენდები', count: apiBrands.value.length },
  { id: 'discounts', label: '％ ფასდაკლებები' },
  { id: 'stock', label: '▧ მარაგები' },
  { id: 'settings', label: '⚙ პარამეტრები' }
])
const adminTabTitle = computed(() => ({
  overview: 'მიმოხილვა',
  products: 'პროდუქტები',
  orders: 'შეკვეთები',
  users: 'მომხმარებლები',
  logs: 'ლოგები',
  categories: 'კატეგორიები',
  brands: 'ბრენდები',
  discounts: 'ფასდაკლებები',
  stock: 'მარაგები',
  settings: 'პარამეტრები'
})[adminTab.value])
const orderTableLabels = {
  order: 'შეკვეთა',
  customer: 'მომხმარებელი',
  date: 'თარიღი',
  amount: 'თანხა',
  payment: 'გადახდა',
  bankStatus: 'ბანკის სტატუსი',
  status: 'სტატუსი'
}

function startOfDay(date) {
  return new Date(date.getFullYear(), date.getMonth(), date.getDate(), 0, 0, 0, 0)
}

function endOfDay(date) {
  return new Date(date.getFullYear(), date.getMonth(), date.getDate(), 23, 59, 59, 999)
}

function formatDateTimeOffset(date) {
  const pad = value => String(value).padStart(2, '0')
  const offsetMinutes = -date.getTimezoneOffset()
  const offsetSign = offsetMinutes >= 0 ? '+' : '-'
  const offsetHours = pad(Math.floor(Math.abs(offsetMinutes) / 60))
  const offsetRemainder = pad(Math.abs(offsetMinutes) % 60)

  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())}T${pad(date.getHours())}:${pad(date.getMinutes())}:${pad(date.getSeconds())}${offsetSign}${offsetHours}:${offsetRemainder}`
}

function formatDateInput(date) {
  const pad = value => String(value).padStart(2, '0')
  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())}`
}

function formatDisplayDate(date) {
  const pad = value => String(value).padStart(2, '0')
  return `${pad(date.getDate())}/${pad(date.getMonth() + 1)}/${date.getFullYear()}`
}

function parseDisplayDate(value) {
  const trimmedValue = value.trim()
  const match = trimmedValue.match(/^(\d{1,2})[./-](\d{1,2})[./-](\d{4})$/)
  if (!match) return ''

  const [, day, month, year] = match
  const date = new Date(Number(year), Number(month) - 1, Number(day))
  if (
    date.getFullYear() !== Number(year)
    || date.getMonth() !== Number(month) - 1
    || date.getDate() !== Number(day)
  ) {
    return ''
  }

  return formatDateInput(date)
}

const orderReportRange = computed(() => {
  const now = new Date()
  let start = new Date(now)
  start.setMonth(start.getMonth() - 1)
  start = startOfDay(start)
  let end = endOfDay(now)

  if (orderReportPeriod.value === 'quarter') {
    start = new Date(now)
    start.setMonth(start.getMonth() - 3)
    start = startOfDay(start)
    end = endOfDay(now)
  }

  if (orderReportPeriod.value === 'year') {
    start = new Date(now)
    start.setFullYear(start.getFullYear() - 1)
    start = startOfDay(start)
    end = endOfDay(now)
  }

  if (orderReportPeriod.value === 'custom') {
    start = orderReportFrom.value ? startOfDay(new Date(`${orderReportFrom.value}T00:00:00`)) : start
    end = orderReportTo.value ? endOfDay(new Date(`${orderReportTo.value}T00:00:00`)) : end
  }

  return { start, end }
})
const orderReportDisplayFrom = computed({
  get: () => orderReportPeriod.value === 'custom' && orderReportFrom.value
    ? formatDisplayDate(new Date(`${orderReportFrom.value}T00:00:00`))
    : formatDisplayDate(orderReportRange.value.start),
  set: value => {
    const parsedValue = parseDisplayDate(value)
    if (parsedValue) {
      orderReportPeriod.value = 'custom'
      orderReportFrom.value = parsedValue
    }
  }
})
const orderReportDisplayTo = computed({
  get: () => orderReportPeriod.value === 'custom' && orderReportTo.value
    ? formatDisplayDate(new Date(`${orderReportTo.value}T00:00:00`))
    : formatDisplayDate(orderReportRange.value.end),
  set: value => {
    const parsedValue = parseDisplayDate(value)
    if (parsedValue) {
      orderReportPeriod.value = 'custom'
      orderReportTo.value = parsedValue
    }
  }
})
const orderReportPickerFrom = computed({
  get: () => orderReportPeriod.value === 'custom' && orderReportFrom.value
    ? orderReportFrom.value
    : formatDateInput(orderReportRange.value.start),
  set: value => {
    if (value) {
      orderReportPeriod.value = 'custom'
      orderReportFrom.value = value
    }
  }
})
const orderReportPickerTo = computed({
  get: () => orderReportPeriod.value === 'custom' && orderReportTo.value
    ? orderReportTo.value
    : formatDateInput(orderReportRange.value.end),
  set: value => {
    if (value) {
      orderReportPeriod.value = 'custom'
      orderReportTo.value = value
    }
  }
})
const reportOrders = computed(() => {
  const { start, end } = orderReportRange.value
  return adminOrders.value.filter(order => {
    const createdAt = new Date(order.createdAt)
    return !Number.isNaN(createdAt.getTime()) && createdAt >= start && createdAt <= end
  })
})
const ordersPageCount = computed(() => Math.max(1, Math.ceil(reportOrders.value.length / adminPageSize)))
const paginatedReportOrders = computed(() => paginateItems(reportOrders.value, ordersPage.value))
const usersPageCount = computed(() => Math.max(1, Math.ceil(apiUsers.value.length / adminPageSize)))
const paginatedUsers = computed(() => paginateItems(apiUsers.value, usersPage.value))
const logsPageCount = computed(() => Math.max(1, Math.ceil(activityLogs.value.length / adminPageSize)))
const paginatedLogs = computed(() => paginateItems(activityLogs.value, logsPage.value))
const reportPaidOrders = computed(() => reportOrders.value.filter(order => ['paid', 'refunded', 'partially_refunded'].includes(order.paymentStatus)))
const paymentResultContent = computed(() => ({
  success: {
    eyebrow: 'გადახდა წარმატებულია',
    title: 'შეკვეთა გადახდილია',
    text: 'Liberty გადახდა წარმატებით დასრულდა. შეკვეთა უკვე პროცესშია.'
  },
  error: {
    eyebrow: 'გადახდა ვერ დასრულდა',
    title: 'ტრანზაქცია უარყოფილია',
    text: 'გადახდა ვერ შესრულდა ან ბანკმა დააბრუნა შეცდომა. შეგიძლია სცადო თავიდან.'
  },
  cancel: {
    eyebrow: 'გადახდა გაუქმდა',
    title: 'ტრანზაქცია გაუქმებულია',
    text: 'გადახდა გაუქმდა. შეკვეთა გადაუხდელი დარჩა.'
  }
})[paymentResult.value] || null)
const orderReportStats = computed(() => [
  {
    label: 'სულ შეკვეთები',
    value: reportOrders.value.length.toLocaleString(),
    note: `${reportPaidOrders.value.length} გადახდილი`
  },
  {
    label: 'საბოლოო თანხა',
    value: `${reportPaidOrders.value.reduce((sum, order) => sum + Number(order.total || 0), 0).toLocaleString()} ₾`,
    note: 'მხოლოდ გადახდილი შეკვეთები'
  },
  {
    label: 'ახალი / პროცესში',
    value: `${reportOrders.value.filter(order => order.statusValue === 'new').length} / ${reportOrders.value.filter(order => order.statusValue === 'processing').length}`,
    note: 'მიმდინარე სამუშაო'
  },
  {
    label: 'გაუქმებული / დაბრუნებული',
    value: `${reportOrders.value.filter(order => order.statusValue === 'cancelled' || order.paymentStatus === 'cancelled').length} / ${reportOrders.value.filter(order => ['refunded', 'partially_refunded'].includes(order.paymentStatus)).length}`,
    note: 'ბანკის სტატუსითაც ითვლება'
  }
])
const activeCategoryNames = computed(() => {
  if (activeCategory.value === 'ყველა') return []
  const category = flatCategories.value.find(item => item.name === activeCategory.value)
  return category ? categoryDescendantNames(category) : [activeCategory.value]
})

function paginateItems(items, page) {
  const start = (Math.max(1, page) - 1) * adminPageSize
  return items.slice(start, start + adminPageSize)
}

function setAdminPage(type, page) {
  const targets = {
    orders: { page: ordersPage, count: ordersPageCount },
    users: { page: usersPage, count: usersPageCount },
    logs: { page: logsPage, count: logsPageCount }
  }
  const target = targets[type]
  if (!target) return
  target.page.value = Math.min(target.count.value, Math.max(1, page))
}

watch(reportOrders, () => { ordersPage.value = 1 })
watch(apiUsers, () => { usersPage.value = Math.min(usersPage.value, usersPageCount.value) })
watch(activityLogs, () => { logsPage.value = Math.min(logsPage.value, logsPageCount.value) })
watch(heroOfferProducts, () => {
  heroOfferIndex.value = Math.min(heroOfferIndex.value, Math.max(0, heroOfferProducts.value.length - 1))
})

function mapApiProduct(product) {
  const categoryNames = parseCategoryNames(product)
  const category = categoryNames[0] || 'სხვა'
  const price = Number(product.price ?? product.regularPrice ?? product.salePrice ?? 0)
  const oldPrice = product.salePrice && product.regularPrice && product.regularPrice > product.salePrice
    ? Number(product.regularPrice)
    : null
  const discountPercent = oldPrice && price
    ? Math.round((oldPrice - price) / oldPrice * 100)
    : 0
  const stock = Number(product.stockQuantity ?? 0)
  const parsedSpecs = parseSpecifications(product.specificationsJson)
  const imageUrls = parseProductImageUrls(product.imageUrl)

  return {
    id: `db-${product.id}`,
    backendId: product.id,
    sortKey: product.id,
    name: product.name || product.slug || `პროდუქტი #${product.id}`,
    category,
    categoryNames,
    categoryId: product.categoryId,
    price,
    oldPrice,
    rating: 4.8,
    reviews: product.source === 'local' ? 0 : 8,
    badge: discountPercent > 0 ? `-${discountPercent}%` : 'ახალი',
    stock,
    imageUrl: imageUrls[0] || '',
    imageUrls,
    image: imageUrls[0] || productImageFallback,
    brandId: product.brandId || product.brand?.id || '',
    brand: product.brand?.name || '',
    sku: product.sku || '',
    shortDescription: product.shortDescription || '',
    specificationsJson: product.specificationsJson || '',
    isHidden: Boolean(product.isHidden),
    specs: parsedSpecs.length ? parsedSpecs.map(spec => `${spec.key}: ${spec.value}`) : [
      product.sku ? `კოდი: ${product.sku}` : 'კოდი არ არის',
      stock > 0 && product.stockStatus === 'instock' ? 'მარაგშია' : 'არ არის მარაგში',
      category
    ],
    source: product.source,
    description: product.description || product.shortDescription || ''
  }
}

function isInStock(product) {
  return Number(product?.stock ?? 0) > 0
}

function productBadge(product) {
  return isInStock(product) ? product.badge : 'არ არის მარაგში'
}

function stockLabel(product) {
  const stock = Number(product?.stock ?? 0)
  return stock > 0 ? `მარაგშია — ${stock} ცალი` : 'არ არის მარაგში'
}

function flattenCategories(items, level = 0) {
  return items.flatMap(category => [
    { ...category, level },
    ...flattenCategories(category.children || [], level + 1)
  ])
}

function countCategoryProducts(name) {
  return allProducts.value.filter(product => productMatchesCategories(product, [name])).length
}

function categoryDescendantNames(category) {
  return [
    category.name,
    ...(category.children || []).flatMap(child => categoryDescendantNames(child))
  ]
}

function countCategoryTreeProducts(category) {
  const names = categoryDescendantNames(category)
  return allProducts.value.filter(product => productMatchesCategories(product, names)).length
}

function parseCategoryNames(product) {
  const names = [
    product.category?.name,
    ...(typeof product.categoryNames === 'string' ? product.categoryNames.split(',') : [])
  ]
    .map(name => String(name || '').trim())
    .filter(Boolean)

  return [...new Set(names)]
}

function productMatchesCategories(product, names) {
  const productCategories = product.categoryNames?.length ? product.categoryNames : [product.category]
  return productCategories.some(categoryName => names.includes(categoryName))
}

function catalogIcon(index) {
  return ['✦', '◈', '◉', '⚡', '⌁', '◌', '♧', '✣'][index % 8]
}

function setCatalogPreview(category) {
  activeCatalogCategoryName.value = category.name
}

function selectFilterCategory(name) {
  activeCategory.value = name
  visibleCount.value = 12
}

function toggleFilterCategory(category) {
  const next = new Set(expandedFilterCategories.value)
  next.has(category.name) ? next.delete(category.name) : next.add(category.name)
  expandedFilterCategories.value = next
}

function isFilterCategoryExpanded(category) {
  return expandedFilterCategories.value.has(category.name)
    || categoryDescendantNames(category).includes(activeCategory.value)
}

function parseSpecifications(value) {
  if (!value) return []

  try {
    const specs = JSON.parse(value)
    return Array.isArray(specs)
      ? specs.filter(spec => spec?.key && spec?.value)
      : []
  } catch {
    return []
  }
}

async function loadProducts() {
  productsLoading.value = true
  productsError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/products?includeHidden=true`)
    if (!response.ok) throw new Error('products request failed')
    const data = await response.json()
    apiProducts.value = data.map(mapApiProduct)
  } catch {
    productsError.value = 'ლოკალურ API-სთან კავშირი ვერ დამყარდა'
  } finally {
    productsLoading.value = false
  }
}

async function loadCategories() {
  categoriesError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/categories`)
    if (!response.ok) throw new Error('categories request failed')
    apiCategories.value = await response.json()
  } catch {
    categoriesError.value = 'კატეგორიების წამოღება ვერ მოხერხდა'
  }
}

async function loadBrands() {
  brandsError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/brands`)
    if (!response.ok) throw new Error('brands request failed')
    apiBrands.value = await response.json()
  } catch {
    brandsError.value = 'ბრენდების წამოღება ვერ მოხერხდა'
  }
}

function paymentMethodLabel(value) {
  return ({
    'bog-installment': 'BOG განვადება',
    'bog-parts': 'BOG ნაწილ-ნაწილ',
    bog_piece_by_piece: 'BOG ნაწილ-ნაწილ',
    bog_installment_plan: 'BOG განვადება',
    tbc: 'TBC განვადება',
    tbc_installment: 'TBC განვადება',
    liberty: 'Liberty გადახდა',
    cash: 'cash'
  })[value] || value
}

function paymentStatusLabel(value) {
  return ({
    unpaid: 'გადაუხდელი',
    pending: 'მოლოდინში',
    pending_callback: 'დასადასტურებელია',
    paid: 'გადახდილია',
    failed: 'ვერ გადაიხადა',
    cancelled: 'გაუქმებულია',
    refunded: 'დაბრუნებულია',
    partially_refunded: 'ნაწილობრივ დაბრუნებულია'
  })[value] || value || 'მოლოდინში'
}

function orderStatusLabel(value) {
  return orderStatusOptions.find(option => option.value === value)?.label || value
}

function mapApiOrder(order) {
  return {
    ...order,
    customerName: `${order.customer?.firstName || ''} ${order.customer?.lastName || ''}`.trim(),
    paymentLabel: paymentMethodLabel(order.paymentMethod),
    paymentStatusLabel: paymentStatusLabel(order.paymentStatus),
    statusValue: order.status,
    status: orderStatusLabel(order.status)
  }
}

function getLibertyBrowserInfo() {
  const screenInfo = window.screen || {}
  const timezoneOffset = String(new Date().getTimezoneOffset())
  const storedDeviceId = localStorage.getItem('agrotradeDeviceId')
  const deviceId = storedDeviceId
    || crypto.randomUUID?.()
    || `device-${Date.now()}-${Math.random().toString(16).slice(2)}`

  if (!storedDeviceId) {
    localStorage.setItem('agrotradeDeviceId', deviceId)
  }

  return {
    deviceId,
    acceptHeader: 'application/json,application/jose;charset=utf-8',
    ip: null,
    timeZoneOffset: timezoneOffset,
    platform: navigator.platform || null,
    jsEnabled: true,
    colorDepth: screenInfo.colorDepth || null,
    screenWidth: screenInfo.width || null,
    screenHeight: screenInfo.height || null,
    userAgent: navigator.userAgent || null,
    language: navigator.language || null,
    javaEnabled: typeof navigator.javaEnabled === 'function' ? navigator.javaEnabled() : false
  }
}

async function loadOrders() {
  ordersLoading.value = true
  orderError.value = ''

  try {
    const { start, end } = orderReportRange.value
    const params = new URLSearchParams({
      from: formatDateTimeOffset(start),
      to: formatDateTimeOffset(end)
    })
    const response = await fetch(`${apiBaseUrl}/api/orders?${params}`, { headers: authHeaders() })
    if (!response.ok) throw new Error('orders request failed')
    const data = await response.json()
    apiOrders.value = data.map(mapApiOrder)
    if (selectedAdminOrder.value) {
      selectedAdminOrder.value = apiOrders.value.find(order => order.id === selectedAdminOrder.value.id) || null
    }
  } catch {
    orderError.value = 'შეკვეთების წამოღება ვერ მოხერხდა'
  } finally {
    ordersLoading.value = false
  }
}

watch([orderReportPeriod, orderReportFrom, orderReportTo], () => {
  if (adminPage.value && adminTab.value === 'orders' && !selectedAdminOrder.value) {
    loadOrders()
  }
})

async function loadUsers() {
  usersLoading.value = true
  userError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth`, { headers: authHeaders() })
    if (!response.ok) throw new Error('users request failed')
    apiUsers.value = await response.json()
  } catch {
    apiUsers.value = []
    userError.value = 'მომხმარებლების წამოღება ვერ მოხერხდა'
  } finally {
    usersLoading.value = false
  }
}

function resetUserForm() {
  userForm.value = {
    firstName: '',
    lastName: '',
    phone: '',
    email: '',
    password: '',
    role: 'user'
  }
}

function openUserForm() {
  userError.value = ''
  resetUserForm()
  showUserForm.value = true
}

function closeUserForm() {
  if (userSaving.value) return
  showUserForm.value = false
  resetUserForm()
}

async function createAdminUser() {
  if (userSaving.value) return

  userSaving.value = true
  userError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/admin/users`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify(userForm.value)
    })
    if (!response.ok) {
      const error = await response.json().catch(() => null)
      throw new Error(error?.message || 'create user failed')
    }

    const user = await response.json()
    apiUsers.value = [user, ...apiUsers.value]
    closeUserForm()
    showAdminNotice('მომხმარებელი დაემატა')
  } catch (error) {
    userError.value = error.message || 'მომხმარებლის დამატება ვერ მოხერხდა'
  } finally {
    userSaving.value = false
  }
}

async function updateUserRole(user, role) {
  if (!user || userRoleSaving.value) return
  if (user.role === role) return

  userRoleSaving.value = user.id
  userError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/${user.id}/role`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify({ role })
    })
    if (!response.ok) {
      const error = await response.json().catch(() => null)
      throw new Error(error?.message || 'role update failed')
    }

    const updatedUser = await response.json()
    apiUsers.value = apiUsers.value.map(item => item.id === updatedUser.id ? updatedUser : item)
    if (currentUser.value?.id === updatedUser.id) {
      currentUser.value = { ...currentUser.value, role: updatedUser.role }
      persistCurrentUser()
    }
    showAdminNotice('როლი განახლდა')
  } catch (error) {
    userError.value = error.message || 'როლის შეცვლა ვერ მოხერხდა'
  } finally {
    userRoleSaving.value = null
  }
}

async function updateUserBlock(user) {
  if (!user || userActionSaving.value || currentUser.value?.id === user.id) return

  userActionSaving.value = user.id
  userError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/${user.id}/block`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify({ isBlocked: !user.isBlocked })
    })
    if (!response.ok) {
      const error = await response.json().catch(() => null)
      throw new Error(error?.message || 'block user failed')
    }

    const updatedUser = await response.json()
    apiUsers.value = apiUsers.value.map(item => item.id === updatedUser.id ? updatedUser : item)
    showAdminNotice(updatedUser.isBlocked ? 'მომხმარებელი დაიბლოკა' : 'მომხმარებელი გაიხსნა')
  } catch (error) {
    userError.value = error.message || 'მომხმარებლის სტატუსის შეცვლა ვერ მოხერხდა'
  } finally {
    userActionSaving.value = null
  }
}

async function deleteUser(user) {
  if (!user || userActionSaving.value || currentUser.value?.id === user.id) return
  if (!window.confirm(`${user.firstName} ${user.lastName} წავშალო?`)) return

  userActionSaving.value = user.id
  userError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/${user.id}`, {
      method: 'DELETE',
      headers: authHeaders()
    })
    if (!response.ok) {
      const error = await response.json().catch(() => null)
      throw new Error(error?.message || 'delete user failed')
    }

    apiUsers.value = apiUsers.value.filter(item => item.id !== user.id)
    showAdminNotice('მომხმარებელი წაიშალა')
  } catch (error) {
    userError.value = error.message || 'მომხმარებლის წაშლა ვერ მოხერხდა'
  } finally {
    userActionSaving.value = null
  }
}

async function loadActivityLogs() {
  activityLogsLoading.value = true
  activityLogsError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/activity-logs?take=80`, { headers: authHeaders() })
    if (!response.ok) throw new Error('activity logs request failed')
    activityLogs.value = await response.json()
  } catch {
    activityLogs.value = []
    activityLogsError.value = 'ლოგების წამოღება ვერ მოხერხდა'
  } finally {
    activityLogsLoading.value = false
  }
}

function selectAdminTab(tab) {
  adminTab.value = tab
  if (tab === 'orders') loadOrders()
  if (tab === 'users') loadUsers()
  if (tab === 'logs') loadActivityLogs()
}

function openAuth(mode = 'login') {
  authMode.value = mode
  authError.value = ''
  authOpen.value = true
}

function closeAuth() {
  authOpen.value = false
  authError.value = ''
}

function syncProfileForm() {
  profileForm.value = {
    firstName: currentUser.value?.firstName || '',
    lastName: currentUser.value?.lastName || '',
    phone: currentUser.value?.phone || '',
    email: currentUser.value?.email || '',
    city: currentUser.value?.city || '',
    region: currentUser.value?.region || '',
    street: currentUser.value?.street || '',
    building: currentUser.value?.building || '',
    addressNote: currentUser.value?.addressNote || ''
  }
}

function fillCheckoutFromCurrentUser() {
  if (!currentUser.value) return

  checkoutForm.value.firstName = currentUser.value.firstName || checkoutForm.value.firstName
  checkoutForm.value.lastName = currentUser.value.lastName || checkoutForm.value.lastName
  checkoutForm.value.phone = currentUser.value.phone || checkoutForm.value.phone
  checkoutForm.value.email = currentUser.value.email || checkoutForm.value.email
  checkoutForm.value.city = currentUser.value.city || checkoutForm.value.city
  checkoutForm.value.address = buildProfileAddress(currentUser.value) || checkoutForm.value.address
}

function buildProfileAddress(profile) {
  return [profile?.region, profile?.street, profile?.building]
    .filter(Boolean)
    .join(', ')
}

async function loadUserOrders() {
  if (!currentUser.value?.id) return

  userOrdersLoading.value = true
  try {
    const response = await fetch(`${apiBaseUrl}/api/orders/user/${currentUser.value.id}`, { headers: authHeaders() })
    if (!response.ok) throw new Error('user orders failed')
    const data = await response.json()
    userOrders.value = data.map(mapApiOrder)
  } catch {
    userOrders.value = []
  } finally {
    userOrdersLoading.value = false
  }
}

function openAccount() {
  if (!currentUser.value) {
    openAuth('login')
    return
  }

  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = true
  comparePage.value = false
  selectedUserOrder.value = null
  syncProfileForm()
  loadUserOrders()
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openUserOrder(order) {
  selectedUserOrder.value = order
}

function closeUserOrder() {
  selectedUserOrder.value = null
}

async function saveProfile() {
  const email = currentUser.value?.isEmailVerified
    ? currentUser.value.email
    : profileForm.value.email.trim()

  const payload = {
    firstName: profileForm.value.firstName.trim(),
    lastName: profileForm.value.lastName.trim(),
    phone: profileForm.value.phone.trim(),
    email,
    city: profileForm.value.city.trim(),
    region: profileForm.value.region.trim(),
    street: profileForm.value.street.trim(),
    building: profileForm.value.building.trim(),
    addressNote: profileForm.value.addressNote.trim()
  }

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/me`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify(payload)
    })
    if (!response.ok) throw new Error('profile update failed')
    const updatedUser = await response.json()
    currentUser.value = {
      ...currentUser.value,
      ...updatedUser
    }
  } catch {
    showAdminNotice('პროფილის შენახვა ვერ მოხერხდა')
    return
  }

  persistCurrentUser()
  fillCheckoutFromCurrentUser()
  profileEditing.value = false
  showAdminNotice('პროფილი განახლდა')
}

function resetAuthForm() {
  authForm.value = {
    firstName: '',
    lastName: '',
    phone: '',
    email: '',
    password: '',
    confirmPassword: ''
  }
}

async function submitAuth() {
  if (authSaving.value) return

  authSaving.value = true
  authError.value = ''

  if (authMode.value === 'register' && authForm.value.password !== authForm.value.confirmPassword) {
    authError.value = 'პაროლები ერთმანეთს არ ემთხვევა.'
    authSaving.value = false
    return
  }

  const payload = authMode.value === 'register'
    ? {
        firstName: authForm.value.firstName,
        lastName: authForm.value.lastName,
        phone: authForm.value.phone,
        email: authForm.value.email,
        password: authForm.value.password
      }
    : { email: authForm.value.email, password: authForm.value.password }

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/${authMode.value === 'register' ? 'register' : 'login'}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
    if (!response.ok) {
      let message = ''
      try {
        message = (await response.json())?.message || ''
      } catch {
        message = ''
      }
      throw new Error(message || 'auth failed')
    }
    const authResult = await response.json()
    currentUser.value = {
      ...authResult.user,
      token: authResult.token
    }
    persistCurrentUser()
    syncProfileForm()
    fillCheckoutFromCurrentUser()
    resetAuthForm()
    closeAuth()
    await loadUsers()
    showAdminNotice(authMode.value === 'register' ? 'რეგისტრაცია დასრულდა. დადასტურების ლინკი გამოგიგზავნეთ ელფოსტაზე.' : 'წარმატებით შეხვედით')
  } catch (error) {
    const serverMessage = error instanceof Error ? error.message : ''
    authError.value = serverMessage.includes('already exists')
      ? 'ეს ელფოსტა უკვე დარეგისტრირებულია. სცადე შესვლა.'
      : authMode.value === 'register'
        ? 'რეგისტრაცია ვერ მოხერხდა. შეამოწმე მონაცემები.'
        : 'შესვლა ვერ მოხერხდა. შეამოწმე ელფოსტა და პაროლი.'
  } finally {
    authSaving.value = false
  }
}

async function verifyEmailFromUrl() {
  const params = new URLSearchParams(window.location.search)
  const token = params.get('verifyEmail')
  if (!token) return

  try {
    const response = await fetch(`${apiBaseUrl}/api/auth/verify-email`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ token })
    })
    if (!response.ok) throw new Error('verify failed')

    if (currentUser.value) {
      currentUser.value = { ...currentUser.value, isEmailVerified: true }
      persistCurrentUser()
    }
    showAdminNotice('ელფოსტა წარმატებით დადასტურდა')
  } catch {
    showAdminNotice('დადასტურების ლინკი არასწორია ან ვადა გაუვიდა')
  } finally {
    params.delete('verifyEmail')
    const cleanUrl = `${window.location.pathname}${params.toString() ? `?${params}` : ''}${window.location.hash}`
    window.history.replaceState({}, '', cleanUrl)
  }
}

async function resendVerificationEmail() {
  if (!currentUser.value?.email) return

  try {
    await fetch(`${apiBaseUrl}/api/auth/resend-verification`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email: currentUser.value.email })
    })
    showAdminNotice('დადასტურების ლინკი ხელახლა გაიგზავნა')
  } catch {
    showAdminNotice('ლინკის გაგზავნა ვერ მოხერხდა')
  }
}

function logout() {
  if (authLogoutTimer) {
    window.clearTimeout(authLogoutTimer)
    authLogoutTimer = null
  }
  currentUser.value = null
  localStorage.removeItem(authStorageKey)
  accountPage.value = false
  adminPage.value = false
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  comparePage.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
  showAdminNotice('გამოხვედით ანგარიშიდან')
}

function handleAccountClick() {
  if (!currentUser.value) {
    openAuth('login')
    return
  }

  if (['admin', 'manager'].includes(currentUser.value.role)) {
    openAdmin()
    return
  }

  openAccount()
}

function openAdminOrder(order) {
  selectedAdminOrder.value = order
  refundForm.value = { amount: Number(order.total || 0).toFixed(2), reason: '' }
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function closeAdminOrder() {
  selectedAdminOrder.value = null
  refundConfirmOpen.value = false
  refundForm.value = { amount: '', reason: '' }
}

function formatOrderDate(value) {
  return new Intl.DateTimeFormat('ka-GE', {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  }).format(new Date(value))
}

function deliveryMethodLabel(value) {
  return value === 'pickup' ? 'ფილიალიდან გატანა' : 'კურიერით მიწოდება'
}

async function updateAdminOrderStatus(status) {
  if (!selectedAdminOrder.value || orderStatusSaving.value) return

  orderStatusSaving.value = true
  orderError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/orders/${selectedAdminOrder.value.id}/status`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify({ status })
    })
    if (!response.ok) throw new Error('status request failed')
    const updatedOrder = mapApiOrder(await response.json())
    apiOrders.value = apiOrders.value.map(order => order.id === updatedOrder.id ? updatedOrder : order)
    selectedAdminOrder.value = updatedOrder
  } catch {
    orderError.value = 'სტატუსის შეცვლა ვერ მოხერხდა'
  } finally {
    orderStatusSaving.value = false
  }
}

function openRefundConfirmation() {
  if (!selectedAdminOrder.value || refundSaving.value) return
  const amount = Number(refundForm.value.amount)
  if (!amount || amount <= 0) {
    orderError.value = 'დასაბრუნებელი თანხა სწორად მიუთითე.'
    return
  }

  orderError.value = ''
  refundConfirmOpen.value = true
}

function closeRefundConfirmation() {
  if (refundSaving.value) return
  refundConfirmOpen.value = false
}

async function refundAdminOrder() {
  if (!selectedAdminOrder.value || refundSaving.value) return

  const amount = Number(refundForm.value.amount)
  if (!amount || amount <= 0) {
    orderError.value = 'დასაბრუნებელი თანხა სწორად მიუთითე.'
    refundConfirmOpen.value = false
    return
  }

  refundSaving.value = true
  orderError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/liberty-pay/orders/${selectedAdminOrder.value.id}/refund`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify({
        amount: Number(amount.toFixed(2)),
        reason: refundForm.value.reason || null
      })
    })

    if (!response.ok) {
      const error = await response.json().catch(() => null)
      throw new Error(error?.message || 'refund request failed')
    }

    const refund = await response.json()
    selectedAdminOrder.value = {
      ...selectedAdminOrder.value,
      statusValue: refund.orderStatus,
      status: orderStatusLabel(refund.orderStatus),
      paymentStatus: refund.paymentStatus,
      paymentStatusLabel: paymentStatusLabel(refund.paymentStatus)
    }
    apiOrders.value = apiOrders.value.map(order =>
      order.id === selectedAdminOrder.value.id
        ? {
          ...order,
          statusValue: refund.orderStatus,
          status: orderStatusLabel(refund.orderStatus),
          paymentStatus: refund.paymentStatus,
          paymentStatusLabel: paymentStatusLabel(refund.paymentStatus)
        }
        : order
    )
    refundForm.value.amount = Math.max(0, Number(refund.paymentAmount) - Number(refund.refundedAmount)).toFixed(2)
    refundForm.value.reason = ''
    refundConfirmOpen.value = false
    adminNotice.value = `დაბრუნდა ${Number(refund.refundedAmount).toLocaleString()} ₾`
    window.clearTimeout(adminNoticeTimer)
    adminNoticeTimer = window.setTimeout(() => { adminNotice.value = '' }, 2600)
  } catch (error) {
    orderError.value = error.message || 'თანხის დაბრუნება ვერ მოხერხდა'
  } finally {
    refundSaving.value = false
  }
}

async function saveProduct() {
  if (!productForm.value.name.trim()) return

  productSaving.value = true
  productsError.value = ''

  const specs = productForm.value.specifications
    .map(spec => ({ key: spec.key.trim(), value: spec.value.trim() }))
    .filter(spec => spec.key && spec.value)
  const formData = new FormData()
  const basePrice = Number(productForm.value.price || 0)
  const salePrice = Number(productForm.value.salePrice || 0)
  const hasDiscount = basePrice > 0 && salePrice > 0 && salePrice < basePrice
  formData.append('name', productForm.value.name)
  formData.append('stockStatus', 'instock')
  if (productForm.value.sku) formData.append('sku', productForm.value.sku)
  if (basePrice > 0) formData.append('price', String(hasDiscount ? salePrice : basePrice))
  if (hasDiscount) {
    formData.append('regularPrice', String(basePrice))
    formData.append('salePrice', String(salePrice))
  }
  if (productForm.value.stockQuantity) formData.append('stockQuantity', productForm.value.stockQuantity)
  if (productForm.value.brandId) formData.append('brandId', productForm.value.brandId)
  if (productForm.value.categoryId) formData.append('categoryId', productForm.value.categoryId)
  if (productForm.value.categoryNames) formData.append('categoryNames', productForm.value.categoryNames)
  if (productForm.value.imageUrl) formData.append('imageUrl', productForm.value.imageUrl)
  formData.append('imageUrlsJson', JSON.stringify(productForm.value.imageUrls.slice(0, 3)))
  productForm.value.imageFiles.slice(0, Math.max(0, 3 - productForm.value.imageUrls.length)).forEach(file => {
    formData.append('images', file)
  })
  if (productForm.value.shortDescription) formData.append('shortDescription', productForm.value.shortDescription)
  if (specs.length) formData.append('specificationsJson', JSON.stringify(specs))

  const isEditing = Boolean(productForm.value.id)
  const url = isEditing
    ? `${apiBaseUrl}/api/products/${productForm.value.id}/form`
    : `${apiBaseUrl}/api/products/form`

  try {
    const response = await fetch(url, {
      method: isEditing ? 'PUT' : 'POST',
      headers: authHeaders(),
      body: formData
    })

    if (!response.ok) throw new Error('save product failed')

    resetProductForm()
    showAddProduct.value = false
    await Promise.all([loadProducts(), loadCategories(), loadBrands()])
    showAdminNotice(isEditing ? 'პროდუქტი წარმატებით დარედაქტირდა' : 'პროდუქტი წარმატებით დაემატა')
  } catch {
    productsError.value = 'პროდუქტის შენახვა ვერ მოხერხდა. შეამოწმე backend ჩართულია თუ არა.'
  } finally {
    productSaving.value = false
  }
}

function resetProductForm() {
  productForm.value.imagePreviews
    .filter(preview => !preview.existing)
    .forEach(preview => URL.revokeObjectURL(preview.url))

  productForm.value = {
    id: null,
    name: '',
    sku: '',
    price: '',
    regularPrice: '',
    salePrice: '',
    stockQuantity: '',
    brandId: '',
    categoryId: '',
    categoryNames: '',
    imageUrl: '',
    imageUrls: [],
    imageFiles: [],
    imagePreviews: [],
    shortDescription: '',
    specifications: [
      { key: '', value: '' }
    ]
  }
}

function closeAddProduct() {
  showAddProduct.value = false
  productsError.value = ''
  resetProductForm()
}

function openAddProduct() {
  productsError.value = ''
  resetProductForm()
  showAddProduct.value = true
}

function editProduct(product) {
  productsError.value = ''
  if (!product.backendId) {
    showAdminNotice('რედაქტირება ხელმისაწვდომია ბაზაში დამატებული პროდუქტებისთვის')
    return
  }

  const specs = parseSpecifications(product.specificationsJson)
  const imageUrls = product.imageUrls || parseProductImageUrls(product.imageUrl)
  productForm.value = {
    id: product.backendId,
    name: product.name || '',
    sku: product.sku || '',
    price: product.oldPrice || product.price || '',
    regularPrice: '',
    salePrice: '',
    stockQuantity: product.stock || '',
    brandId: product.brandId || '',
    categoryId: product.categoryId || '',
    categoryNames: product.category || '',
    imageUrl: '',
    imageUrls,
    imageFiles: [],
    imagePreviews: imageUrls.map(url => ({ url, existing: true })),
    shortDescription: product.shortDescription || '',
    specifications: specs.length ? specs : [{ key: '', value: '' }]
  }
  showAddProduct.value = true
}

async function toggleProductVisibility(product) {
  productsError.value = ''
  if (!product.backendId) {
    showAdminNotice('დამალვა ხელმისაწვდომია ბაზაში დამატებული პროდუქტებისთვის')
    return
  }

  if (productDeleting.value) return
  productDeleting.value = true

  try {
    const response = await fetch(`${apiBaseUrl}/api/products/${product.backendId}/visibility`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify({ isHidden: !product.isHidden })
    })
    if (!response.ok) throw new Error('visibility update failed')

    if (!product.isHidden && selectedProduct.value?.id === product.id) selectedProduct.value = null
    compareIds.value = compareIds.value.filter(id => id !== product.id)
    if (!product.isHidden) removeFromCart(product.id)
    await loadProducts()
    showAdminNotice(product.isHidden ? 'პროდუქტი გამოჩნდა საიტზე' : 'პროდუქტი დაიმალა საიტიდან')
  } catch {
    productsError.value = 'პროდუქტის სტატუსის შეცვლა ვერ მოხერხდა. შეამოწმე API.'
  } finally {
    productDeleting.value = false
  }
}

function resetDiscountForm() {
  discountForm.value = { productId: '', regularPrice: '', salePrice: '', percent: '' }
  discountProductSearch.value = ''
  discountProductPickerOpen.value = false
}

function openDiscountForm(product = null) {
  productsError.value = ''
  const target = product?.backendId ? product : null
  discountForm.value = {
    productId: target?.backendId ? String(target.backendId) : '',
    regularPrice: target ? String(target.oldPrice || target.price || '') : '',
    salePrice: target?.oldPrice ? String(target.price || '') : '',
    percent: ''
  }
  discountProductSearch.value = target?.name || ''
  discountProductPickerOpen.value = false
  showDiscountForm.value = true
}

function closeDiscountForm() {
  showDiscountForm.value = false
  resetDiscountForm()
}

function applyDiscountPercent() {
  const regularPrice = Number(discountForm.value.regularPrice || discountSelectedProduct.value?.price || 0)
  const percent = Number(discountForm.value.percent || 0)
  if (regularPrice > 0 && percent > 0) {
    discountForm.value.salePrice = (regularPrice - (regularPrice * percent / 100)).toFixed(2)
  }
}

function selectDiscountProduct(product) {
  discountForm.value.productId = String(product.backendId)
  discountForm.value.regularPrice = String(product.oldPrice || product.price || '')
  discountForm.value.salePrice = product.oldPrice ? String(product.price || '') : ''
  discountProductSearch.value = product.name || ''
  discountProductPickerOpen.value = false
}

function buildProductDiscountPayload(product, salePrice = null, regularPrice = null) {
  const formData = new FormData()
  formData.append('name', product.name)
  formData.append('stockStatus', 'instock')
  formData.append('price', String(salePrice ?? regularPrice ?? product.price ?? 0))
  if (regularPrice) formData.append('regularPrice', String(regularPrice))
  if (salePrice) formData.append('salePrice', String(salePrice))
  if (product.sku) formData.append('sku', product.sku)
  if (product.stock !== null && product.stock !== undefined) formData.append('stockQuantity', String(product.stock))
  if (product.categoryId) formData.append('categoryId', product.categoryId)
  if (product.category) formData.append('categoryNames', product.category)
  if (product.shortDescription) formData.append('shortDescription', product.shortDescription)
  if (product.specificationsJson) formData.append('specificationsJson', product.specificationsJson)
  formData.append('imageUrlsJson', JSON.stringify(product.imageUrls || []))
  return formData
}

async function saveDiscount() {
  const product = discountSelectedProduct.value
  const regularPrice = Number(discountForm.value.regularPrice || product?.price || 0)
  const salePrice = Number(discountForm.value.salePrice || 0)
  if (!product?.backendId || regularPrice <= 0 || salePrice <= 0 || salePrice >= regularPrice) {
    productsError.value = 'აირჩიე პროდუქტი და მიუთითე ფასდაკლებული ფასი ძველ ფასზე ნაკლები.'
    return
  }

  discountSaving.value = true
  productsError.value = ''
  try {
    const response = await fetch(`${apiBaseUrl}/api/products/${product.backendId}/form`, {
      method: 'PUT',
      headers: authHeaders(),
      body: buildProductDiscountPayload(product, salePrice, regularPrice)
    })
    if (!response.ok) throw new Error('discount failed')
    await loadProducts()
    closeDiscountForm()
    showAdminNotice('პროდუქტი ფასდაკლებებში დაემატა')
  } catch {
    productsError.value = 'ფასდაკლების შენახვა ვერ მოხერხდა'
  } finally {
    discountSaving.value = false
  }
}

async function removeDiscount(product) {
  if (!product?.backendId) return
  discountSaving.value = true
  productsError.value = ''
  try {
    const regularPrice = Number(product.oldPrice || product.price || 0)
    const response = await fetch(`${apiBaseUrl}/api/products/${product.backendId}/form`, {
      method: 'PUT',
      headers: authHeaders(),
      body: buildProductDiscountPayload(product, null, regularPrice)
    })
    if (!response.ok) throw new Error('remove discount failed')
    await loadProducts()
    showAdminNotice('ფასდაკლება მოიხსნა')
  } catch {
    productsError.value = 'ფასდაკლების მოხსნა ვერ მოხერხდა'
  } finally {
    discountSaving.value = false
  }
}

function setProductImage(event) {
  const files = Array.from(event.target.files || [])
  const availableSlots = Math.max(0, 3 - productForm.value.imagePreviews.length)
  const acceptedFiles = files.slice(0, availableSlots)

  productForm.value.imageFiles.push(...acceptedFiles)
  productForm.value.imagePreviews.push(...acceptedFiles.map(file => ({
    url: URL.createObjectURL(file),
    file,
    existing: false
  })))
  event.target.value = ''
}

function removeProductImage(index) {
  const preview = productForm.value.imagePreviews[index]
  if (!preview) return

  if (preview.existing) {
    productForm.value.imageUrls = productForm.value.imageUrls.filter(url => url !== preview.url)
  } else {
    productForm.value.imageFiles = productForm.value.imageFiles.filter(file => file !== preview.file)
    URL.revokeObjectURL(preview.url)
  }

  productForm.value.imagePreviews.splice(index, 1)
}

function parseProductImageUrls(value) {
  if (!value) return []
  return String(value)
    .split('|')
    .map(url => url.trim())
    .filter(Boolean)
    .slice(0, 3)
}

function useFallbackImage(event) {
  if (event.target.src !== productImageFallback) {
    event.target.src = productImageFallback
  }
}

function addSpecification() {
  productForm.value.specifications.push({ key: '', value: '' })
}

function removeSpecification(index) {
  productForm.value.specifications.splice(index, 1)
  if (!productForm.value.specifications.length) addSpecification()
}

function applySpecificationPreset(categoryName = productForm.value.categoryNames) {
  const preset = specificationPresets[categoryName]
  if (!preset) return

  productForm.value.categoryNames = categoryName
  productForm.value.specifications = preset.map(key => ({ key, value: '' }))
}

function selectProductCategory() {
  const category = flatCategories.value.find(item => String(item.id) === String(productForm.value.categoryId))
  productForm.value.categoryNames = category?.name || ''
  if (category?.name && specificationPresets[category.name]) {
    applySpecificationPreset(category.name)
  }
}

function resetCategoryForm() {
  categoryForm.value = { id: null, name: '', parentId: '', sortOrder: 0, imageUrl: '', imageFile: null }
  categoriesError.value = ''
  focusCategoryName()
}

async function focusCategoryName() {
  await nextTick()
  categoryNameInput.value?.focus()
}

function editCategory(category) {
  categoryForm.value = {
    id: category.id,
    name: category.name,
    parentId: category.parentId || '',
    sortOrder: category.sortOrder || 0,
    imageUrl: category.imageUrl || '',
    imageFile: null
  }
  focusCategoryName()
}

function setCategoryImage(event) {
  const file = event.target.files?.[0]
  if (!file) return
  categoryForm.value.imageFile = file
}

async function uploadCategoryImageIfNeeded() {
  if (!categoryForm.value.imageFile) return categoryForm.value.imageUrl || null

  const formData = new FormData()
  formData.append('image', categoryForm.value.imageFile)

  const response = await fetch(`${apiBaseUrl}/api/categories/upload-image`, {
    method: 'POST',
    headers: authHeaders(),
    body: formData
  })
  if (!response.ok) throw new Error('category image upload failed')
  const result = await response.json()
  return result.imageUrl
}

function showAdminNotice(message) {
  adminNotice.value = message
  if (adminNoticeTimer) window.clearTimeout(adminNoticeTimer)
  adminNoticeTimer = window.setTimeout(() => {
    adminNotice.value = ''
    adminNoticeTimer = null
  }, 3000)
}

async function saveCategory() {
  if (!categoryForm.value.name.trim()) {
    categoriesError.value = 'ჯერ ჩაწერე კატეგორიის სახელი.'
    focusCategoryName()
    return
  }

  categorySaving.value = true
  categoriesError.value = ''

  let imageUrl = categoryForm.value.imageUrl || null
  try {
    imageUrl = await uploadCategoryImageIfNeeded()
  } catch {
    categorySaving.value = false
    categoriesError.value = 'კატეგორიის ფოტო ვერ აიტვირთა.'
    return
  }

  const payload = {
    name: categoryForm.value.name,
    parentId: categoryForm.value.parentId ? Number(categoryForm.value.parentId) : null,
    sortOrder: Number(categoryForm.value.sortOrder) || 0,
    imageUrl
  }
  const url = categoryForm.value.id
    ? `${apiBaseUrl}/api/categories/${categoryForm.value.id}`
    : `${apiBaseUrl}/api/categories`
  const successMessage = categoryForm.value.id
    ? 'კატეგორია წარმატებით დარედაქტირდა'
    : 'კატეგორია წარმატებით დაემატა'

  try {
    const response = await fetch(url, {
      method: categoryForm.value.id ? 'PUT' : 'POST',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify(payload)
    })
    if (!response.ok) throw new Error('save category failed')
    resetCategoryForm()
    await loadCategories()
    showAdminNotice(successMessage)
  } catch {
    categoriesError.value = 'კატეგორია ვერ შეინახა. შეამოწმე, რომ ლოკალური API გაშვებულია localhost:5088-ზე.'
  } finally {
    categorySaving.value = false
  }
}

function handleCategoryHeaderAction() {
  if (categoryForm.value.name.trim()) {
    saveCategory()
    return
  }

  resetCategoryForm()
}

function deleteCategory(category) {
  categoryDeleteTarget.value = category
  categoriesError.value = ''
}

function cancelCategoryDelete() {
  if (categoryDeleting.value) return
  categoryDeleteTarget.value = null
}

async function confirmCategoryDelete() {
  if (!categoryDeleteTarget.value || categoryDeleting.value) return

  const category = categoryDeleteTarget.value
  categoryDeleting.value = true
  categoriesError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/categories/${category.id}`, {
      method: 'DELETE',
      headers: authHeaders()
    })
    if (!response.ok) throw new Error('delete category failed')
    categoryDeleteTarget.value = null
    await loadCategories()
    showAdminNotice('კატეგორია წარმატებით წაიშალა')
  } catch {
    categoriesError.value = 'კატეგორია ვერ წაიშალა. შეამოწმე API, ან კატეგორიას შეიძლება შვილები/პროდუქტები აქვს.'
  } finally {
    categoryDeleting.value = false
  }
}

function resetBrandForm() {
  brandForm.value = { id: null, name: '', sortOrder: 0 }
  brandsError.value = ''
}

function editBrand(brand) {
  brandForm.value = {
    id: brand.id,
    name: brand.name,
    sortOrder: brand.sortOrder || 0
  }
}

async function saveBrand() {
  if (!brandForm.value.name.trim()) {
    brandsError.value = 'ჯერ ჩაწერე ბრენდის სახელი.'
    return
  }

  brandSaving.value = true
  brandsError.value = ''

  const payload = {
    name: brandForm.value.name,
    sortOrder: Number(brandForm.value.sortOrder) || 0
  }
  const url = brandForm.value.id
    ? `${apiBaseUrl}/api/brands/${brandForm.value.id}`
    : `${apiBaseUrl}/api/brands`
  const successMessage = brandForm.value.id
    ? 'ბრენდი წარმატებით დარედაქტირდა'
    : 'ბრენდი წარმატებით დაემატა'

  try {
    const response = await fetch(url, {
      method: brandForm.value.id ? 'PUT' : 'POST',
      headers: { 'Content-Type': 'application/json', ...authHeaders() },
      body: JSON.stringify(payload)
    })
    if (!response.ok) throw new Error('save brand failed')
    resetBrandForm()
    await Promise.all([loadBrands(), loadProducts()])
    showAdminNotice(successMessage)
  } catch {
    brandsError.value = 'ბრენდი ვერ შეინახა. შეამოწმე API.'
  } finally {
    brandSaving.value = false
  }
}

function deleteBrand(brand) {
  brandDeleteTarget.value = brand
  brandsError.value = ''
}

function cancelBrandDelete() {
  if (brandDeleting.value) return
  brandDeleteTarget.value = null
}

async function confirmBrandDelete() {
  if (!brandDeleteTarget.value || brandDeleting.value) return

  const brand = brandDeleteTarget.value
  brandDeleting.value = true
  brandsError.value = ''

  try {
    const response = await fetch(`${apiBaseUrl}/api/brands/${brand.id}`, {
      method: 'DELETE',
      headers: authHeaders()
    })
    if (!response.ok) throw new Error('delete brand failed')
    brandDeleteTarget.value = null
    await Promise.all([loadBrands(), loadProducts()])
    showAdminNotice('ბრენდი წარმატებით წაიშალა')
  } catch {
    brandsError.value = 'ბრენდი ვერ წაიშალა. თუ პროდუქტზეა მიბმული, ჯერ პროდუქტიდან მოხსენი.'
  } finally {
    brandDeleting.value = false
  }
}

function toggleWishlist(id) {
  const next = new Set(wishlist.value)
  next.has(id) ? next.delete(id) : next.add(id)
  wishlist.value = next
}

function toggleCompare(product) {
  const exists = compareIds.value.includes(product.id)
  if (exists) {
    compareIds.value = compareIds.value.filter(id => id !== product.id)
    if (compareIds.value.length < 2) comparePage.value = false
    return
  }

  compareIds.value = [...compareIds.value.slice(-1), product.id]
}

function clearCompare() {
  compareIds.value = []
  comparePage.value = false
}

function openCompare() {
  if (compareIds.value.length !== 2) return
  comparePage.value = true
}

function showProduct(product) {
  selectedProduct.value = product
  detailQty.value = 1
  selectedProductImageIndex.value = 0
  detailTab.value = 'description'
  productFaqOpen.value = 0
}

function currentPageState() {
  if (selectedProduct.value) return { page: 'product', productId: selectedProduct.value.id }
  if (checkoutStep.value) return { page: 'checkout', step: checkoutStep.value, orderPlaced: orderPlaced.value }
  if (cartPage.value) return { page: 'cart' }
  if (productsPage.value) return { page: 'products', category: activeCategory.value }
  if (adminPage.value) return { page: 'admin' }
  if (accountPage.value) return { page: 'account' }
  return { page: 'home' }
}

function readStoredPageState() {
  try {
    const value = sessionStorage.getItem(pageStateStorageKey)
    if (!value) return null
    const state = JSON.parse(value)
    return state?.page ? state : null
  } catch {
    return null
  }
}

function persistPageState(state) {
  try {
    sessionStorage.setItem(pageStateStorageKey, JSON.stringify(state))
  } catch {
    // Session storage can be unavailable in private or restricted browser modes.
  }
}

function rememberPage(replace = false) {
  if (applyingHistoryState.value) return
  const state = currentPageState()
  persistPageState(state)
  const method = replace ? 'replaceState' : 'pushState'
  window.history[method](state, '', window.location.pathname + window.location.search)
}

function applyPageState(state = { page: 'home' }) {
  applyingHistoryState.value = true
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  orderPlaced.value = false

  if (state.page === 'product') {
    selectedProduct.value = allProducts.value.find(product => product.id === state.productId) || null
    detailQty.value = 1
    selectedProductImageIndex.value = 0
  } else if (state.page === 'products') {
    activeCategory.value = state.category || 'ყველა'
    productsPage.value = true
  } else if (state.page === 'cart') {
    cartPage.value = true
  } else if (state.page === 'checkout') {
    checkoutStep.value = state.step || 2
    orderPlaced.value = Boolean(state.orderPlaced)
  } else if (state.page === 'admin') {
    adminPage.value = true
  } else if (state.page === 'account') {
    accountPage.value = true
    syncProfileForm()
    loadUserOrders()
  }

  window.scrollTo({ top: 0, behavior: 'smooth' })
  window.setTimeout(() => applyingHistoryState.value = false, 0)
}

function handleBrowserBack(event) {
  const state = event.state || { page: 'home' }
  persistPageState(state)
  applyPageState(state)
}

function setHeroOffer(index) {
  heroOfferIndex.value = index
}

function nextHeroOffer() {
  if (heroOfferProducts.value.length < 2) return
  heroOfferIndex.value = (heroOfferIndex.value + 1) % heroOfferProducts.value.length
}

onMounted(async () => {
  privacyConsentOpen.value = !hasCookie(privacyConsentCookie)
  readPaymentResultFromUrl()
  verifyEmailFromUrl()
  scheduleAutoLogout()
  const initialPageState = window.history.state?.page
    ? window.history.state
    : readStoredPageState() || { page: 'home' }

  window.history.replaceState(initialPageState, '', window.location.pathname + window.location.search)

  const productsPromise = loadProducts()
  loadCategories()
  loadBrands()
  loadOrders()
  loadUsers()
  await productsPromise
  applyPageState(initialPageState)
  window.addEventListener('popstate', handleBrowserBack)
  heroOfferTimer = window.setInterval(nextHeroOffer, 4500)
})

onBeforeUnmount(() => {
  window.removeEventListener('popstate', handleBrowserBack)
  if (adminNoticeTimer) window.clearTimeout(adminNoticeTimer)
  if (authLogoutTimer) window.clearTimeout(authLogoutTimer)
  if (heroOfferTimer) window.clearInterval(heroOfferTimer)
})

function openProductPage(product) {
  showProduct(product)
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openSearchProduct(product) {
  showProduct(product)
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  searchFocused.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openStockProduct(product) {
  adminTab.value = 'stock'
  stockFilter.value = 'all'
  stockSearch.value = product?.sku || product?.name || ''
  stockVisibleCount.value = 24
  nextTick(() => window.scrollTo({ top: 0, behavior: 'smooth' }))
}

function submitSearch() {
  if (!search.value.trim()) return
  selectedProduct.value = null
  productsPage.value = true
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  activeCategory.value = 'ყველა'
  visibleCount.value = 12
  searchFocused.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function addToCart(product, qty = 1) {
  if (!product) return
  const quantity = Math.max(1, Number(qty) || 1)
  const stock = Number(product.stock ?? 0)

  if (stock <= 0) {
    orderError.value = 'ეს პროდუქტი მარაგში აღარ არის.'
    return
  }

  const existingItem = cartItems.value.find(item => item.id === product.id)
  if (existingItem) {
    if (existingItem.qty + quantity > stock) {
      orderError.value = `მარაგში მხოლოდ ${stock} ცალია.`
      return
    }
    existingItem.qty += quantity
  } else {
    if (quantity > stock) {
      orderError.value = `მარაგში მხოლოდ ${stock} ცალია.`
      return
    }
    cartItems.value = [...cartItems.value, { ...product, qty: quantity }]
  }

  orderError.value = ''
  addedToast.value = true
  window.setTimeout(() => addedToast.value = false, 2200)
}

function removeFromCart(id) {
  cartItems.value = cartItems.value.filter(item => item.id !== id)
}

function submitLibertyPayment(paymentUrl) {
  const url = new URL(paymentUrl)
  const form = document.createElement('form')
  form.method = 'POST'
  form.action = `${url.origin}${url.pathname}`
  form.target = '_blank'
  form.style.display = 'none'

  url.searchParams.forEach((value, key) => {
    const input = document.createElement('input')
    input.type = 'hidden'
    input.name = key
    input.value = value
    form.appendChild(input)
  })

  document.body.appendChild(form)
  form.submit()
  form.remove()
}

function openPaymentWindow(paymentUrl) {
  window.location.href = paymentUrl
}

function isBogPaymentMethod(method = paymentMethod.value) {
  return ['bog-installment', 'bog-parts'].includes(method)
}

function loadBogSdk() {
  if (window.BOG?.Calculator?.open) return Promise.resolve(window.BOG)
  if (bogSdkPromise) return bogSdkPromise

  bogSdkPromise = new Promise((resolve, reject) => {
    const script = document.createElement('script')
    script.src = `https://webstatic.bog.ge/bog-sdk/bog-sdk.js?version=2&client_id=${encodeURIComponent(bogClientId)}`
    script.async = true
    script.onload = () => {
      if (window.BOG?.Calculator?.open) {
        resolve(window.BOG)
        return
      }
      reject(new Error('BOG-ის ფანჯრის ჩატვირთვა ვერ მოხერხდა.'))
    }
    script.onerror = () => reject(new Error('BOG-ის ფანჯრის ჩატვირთვა ვერ მოხერხდა.'))
    document.head.appendChild(script)
  })

  return bogSdkPromise
}

async function createOrder(payload) {
  const response = await fetch(`${apiBaseUrl}/api/orders`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', ...authHeaders() },
    body: JSON.stringify(payload)
  })
  if (!response.ok) {
    const errorBody = await response.json().catch(() => null)
    throw new Error(errorBody?.message || 'order request failed')
  }
  return mapApiOrder(await response.json())
}

async function startBogPayment(createdOrder, selected) {
  const paymentResponse = await fetch(`${apiBaseUrl}/api/bog-pay/orders/${createdOrder.id}/start`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', ...authHeaders() },
    body: JSON.stringify({
      installmentMonth: Number(selected?.month || 0),
      installmentType: selected?.discount_code || selected?.discountCode || 'STANDARD'
    })
  })

  if (!paymentResponse.ok) {
    const errorBody = await paymentResponse.json().catch(() => null)
    throw new Error(errorBody?.message || 'payment start request failed')
  }

  const payment = await paymentResponse.json()
  if (!payment.providerOrderId) {
    throw new Error('payment order id missing')
  }
  return payment
}

async function startTbcPayment(createdOrder) {
  const paymentResponse = await fetch(`${apiBaseUrl}/api/tbc-pay/orders/${createdOrder.id}/start`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', ...authHeaders() },
    body: JSON.stringify({ installmentMonth: null })
  })

  if (!paymentResponse.ok) {
    const errorBody = await paymentResponse.json().catch(() => null)
    throw new Error(errorBody?.message || 'payment start request failed')
  }

  const payment = await paymentResponse.json()
  if (!payment.paymentUrl) {
    throw new Error('payment url missing')
  }
  return payment
}
async function openBogCalculator(payload) {
  await loadBogSdk()

  return new Promise((resolve, reject) => {
    let finished = false

    window.BOG.Calculator.open({
      amount: Number(orderTotal.value.toFixed(2)),
      bnpl: paymentMethod.value === 'bog-parts',
      onClose: () => {
        if (!finished) reject(new Error('BOG-ის ფანჯარა დაიხურა.'))
      },
      onRequest: async (selected, successCb, closeCb) => {
        try {
          const createdOrder = await createOrder(payload)
          placedOrder.value = createdOrder

          const payment = await startBogPayment(createdOrder, selected)
          clearCart()
          await loadOrders()

          successCb(payment.providerOrderId)
        } catch (error) {
          closeCb?.()
          reject(error)
        }
      },
      onComplete: ({ redirectUrl } = {}) => {
        finished = true
        if (redirectUrl) {
          openPaymentWindow(redirectUrl)
        } else {
          orderPlaced.value = true
          rememberPage()
          window.scrollTo({ top: 0, behavior: 'smooth' })
        }
        resolve()
        return false
      }
    })
  })
}

function readPaymentResultFromUrl() {
  const params = new URLSearchParams(window.location.search)
  const result = params.get('payment')
  if (!['success', 'error', 'cancel'].includes(result)) return

  paymentResult.value = result
  paymentResultOrder.value = params.get('order') || ''
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  window.history.replaceState({ page: 'payment-result' }, '', window.location.pathname)
}

function clearFilters() {
  minPrice.value = ''
  maxPrice.value = ''
  selectedBrand.value = 'ყველა'
  onlyInStock.value = false
  onlyDiscounted.value = false
  sortBy.value = 'popular'
  visibleCount.value = 12
}

function openCart() {
  selectedProduct.value = null
  productsPage.value = false
  comparePage.value = false
  accountPage.value = false
  cartPage.value = true
  checkoutStep.value = 0
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function startCheckout() {
  fillCheckoutFromCurrentUser()
  cartPage.value = false
  checkoutStep.value = 2
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function nextToPayment() {
  orderError.value = ''
  if (!checkoutForm.value.firstName.trim() || !checkoutForm.value.lastName.trim() || !checkoutForm.value.phone.trim()) {
    orderError.value = 'სახელი, გვარი და ტელეფონი აუცილებელია.'
    return
  }

  if (deliveryMethod.value === 'courier' && (!checkoutForm.value.city.trim() || !checkoutForm.value.address.trim())) {
    orderError.value = 'კურიერით მიწოდებისთვის ქალაქი და მისამართი აუცილებელია.'
    return
  }

  checkoutStep.value = 3
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openAdmin() {
  if (!currentUser.value?.token || !['admin', 'manager'].includes(currentUser.value.role)) {
    openAuth('login')
    return
  }

  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  comparePage.value = false
  adminPage.value = true
  accountPage.value = false
  loadOrders()
  loadUsers()
  loadActivityLogs()
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function goHome() {
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  orderPlaced.value = false
  paymentResult.value = null
  paymentResultOrder.value = ''
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openProductsPage(category = activeCategory.value) {
  activeCategory.value = category
  visibleCount.value = 12
  selectedProduct.value = null
  productsPage.value = true
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  catalogOpen.value = false
  mobileMenu.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openOffersPage() {
  openProductsPage('ყველა')
  onlyDiscounted.value = true
  sortBy.value = 'discount'
}

function scrollToSection(id) {
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  mobileMenu.value = false
  catalogOpen.value = false
  rememberPage()
  window.setTimeout(() => document.getElementById(id)?.scrollIntoView({ behavior: 'smooth', block: 'start' }), 0)
}

function chooseCategory(category) {
  openProductsPage(category)
  catalogOpen.value = false
  mobileMenu.value = false
}

function backToProducts() {
  selectedProduct.value = null
  productsPage.value = true
  checkoutStep.value = 0
  cartPage.value = false
  adminPage.value = false
  accountPage.value = false
  comparePage.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function backToCheckoutDetails() {
  checkoutStep.value = 2
  orderPlaced.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

async function placeOrder() {
  if (orderSaving.value) return

  orderSaving.value = true
  orderError.value = ''

  const unavailableItem = cartItems.value.find(item => Number(item.stock ?? 0) < Number(item.qty ?? 0))
  if (unavailableItem) {
    orderError.value = `${unavailableItem.name} მარაგში საკმარისი რაოდენობით აღარ არის.`
    orderSaving.value = false
    return
  }

  const payload = {
    userId: currentUser.value?.id || null,
    customer: {
      firstName: checkoutForm.value.firstName,
      lastName: checkoutForm.value.lastName,
      phone: checkoutForm.value.phone,
      email: checkoutForm.value.email
    },
    delivery: {
      method: deliveryMethod.value,
      city: checkoutForm.value.city,
      address: checkoutForm.value.address,
      pickupBranch: deliveryMethod.value === 'pickup' ? checkoutForm.value.pickupBranch : null,
      note: checkoutForm.value.note
    },
    paymentMethod: paymentMethod.value,
    deliveryPrice: deliveryPrice.value,
    items: cartItems.value.map(item => ({
      productId: item.backendId || null,
      productName: item.name,
      productSku: item.sku || null,
      productImageUrl: item.image,
      productCategory: item.category,
      unitPrice: item.price,
      quantity: item.qty
    }))
  }

  try {
    if (isBogPaymentMethod()) {
      await openBogCalculator(payload)
      return
    }

    const createdOrder = await createOrder(payload)
    placedOrder.value = createdOrder

    if (paymentMethod.value === 'liberty') {
      const paymentResponse = await fetch(`${apiBaseUrl}/api/liberty-pay/orders/${createdOrder.id}/start`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', ...authHeaders() },
        body: JSON.stringify({ browser: getLibertyBrowserInfo() })
      })

      if (!paymentResponse.ok) {
        throw new Error('payment start request failed')
      }

      const payment = await paymentResponse.json()
      if (!payment.paymentUrl) {
        throw new Error('payment url missing')
      }

      clearCart()
      await loadOrders()
      submitLibertyPayment(payment.paymentUrl)
      return
    }

    if (paymentMethod.value === 'tbc') {
      const payment = await startTbcPayment(createdOrder)
      clearCart()
      await loadOrders()
      openPaymentWindow(payment.paymentUrl)
      return
    }

    orderPlaced.value = true
    clearCart()
    await loadOrders()
    rememberPage()
    window.scrollTo({ top: 0, behavior: 'smooth' })
  } catch (error) {
    await loadProducts()
    orderError.value = error.message?.includes('payment')
      ? 'შეკვეთა შეიქმნა, მაგრამ გადახდის დაწყება ვერ მოხერხდა.'
      : error.message?.startsWith('Not enough stock for ')
        ? `${error.message.replace('Not enough stock for ', '')} მარაგში საკმარისი რაოდენობით აღარ არის.`
        : error.message || 'შეკვეთის შენახვა ვერ მოხერხდა. შეამოწმე რომ API გაშვებულია localhost:5283-ზე.'
  } finally {
    orderSaving.value = false
  }
}

</script>

<template>
  <div class="site-shell">
    <div class="announcement"><span><Zap :size="15" /> უფასო მიწოდება თბილისში 300₾-დან</span><span class="announcement-right">საგარანტიო მომსახურება • ოფიციალური პროდუქცია</span></div>
    <header>
      <div class="header-main wrap">
        <button class="icon-btn mobile-only" @click="mobileMenu = true"><Menu /></button>
        <a class="logo" href="#" @click.prevent="goHome"><span class="logo-mark brand-logo-mark"><img src="/agrotrade_logo.png" alt="Agro Trade"></span><span>AGRO<span>TRADE</span><small>ტექნიკა, რომელიც მუშაობს</small></span></a>
        <div class="search-box" @focusin="searchFocused=true" @focusout="setTimeout(()=>searchFocused=false,180)"><Search :size="20" /><input v-model="search" @keydown.enter="submitSearch" placeholder="მოძებნე პროდუქტი, ბრენდი ან კატეგორია..." /><button v-if="search" class="search-clear" @click="search=''">×</button><kbd>Enter</kbd><div v-if="searchFocused && search.length >= 2" class="search-dropdown"><div class="search-label"><span>ძიების შედეგები</span><b>{{ allProducts.filter(p => p.name.toLowerCase().includes(search.toLowerCase()) || p.category.toLowerCase().includes(search.toLowerCase())).length }} პროდუქტი</b></div><button v-for="product in searchResults" :key="product.id" @click="openSearchProduct(product)"><img :src="product.image"><span><small>{{product.category}}</small><strong>{{product.name}}</strong></span><b>{{product.price.toLocaleString()}} ₾</b><ChevronRight :size="16" /></button><div v-if="!searchResults.length" class="search-empty"><Search :size="22" /><span>„{{search}}“ ვერ მოიძებნა</span></div><button v-else class="all-results" @click="submitSearch">ყველა შედეგის ნახვა <ArrowRight :size="16" /></button></div></div>
        <div class="header-actions">
          <a class="phone" href="tel:+995598850503"><Phone :size="19" /><span><small>დაგვიკავშირდი</small>+995 598 850 503</span></a>
          <button class="icon-btn account-button" :title="currentUser ? `${currentUser.firstName} • ${currentUser.role}` : 'შესვლა / რეგისტრაცია'" @click="handleAccountClick"><CircleUserRound /><small v-if="currentUser">{{ currentUser.role }}</small></button>
          <button class="icon-btn cart-button" @click="openCart"><ShoppingBag /><b>{{ cartItems.reduce((sum,item)=>sum+item.qty,0) }}</b></button>
        </div>
      </div>
      <nav class="wrap desktop-nav">
        <button class="catalog-btn" :class="{open:catalogOpen}" @click="catalogOpen=!catalogOpen"><Menu :size="18" /> ყველა კატეგორია <ChevronDown :size="16" /></button>
        <a href="#" @click.prevent="openProductsPage('ყველა')">პროდუქცია</a><a href="#categories">კატეგორიები</a><a href="#" @click.prevent="openOffersPage">სპეც. შეთავაზებები</a><a href="#" @click.prevent="scrollToSection('service-info')">ჩვენს შესახებ</a><a href="#contact">კონტაქტი</a>
        <span class="nav-location"><MapPin :size="16" /> თბილისი</span>
      </nav>
      <transition name="catalog-fade">
        <div v-if="catalogOpen" class="catalog-menu">
          <div class="wrap catalog-grid">
            <div class="catalog-main-list">
              <button class="catalog-card catalog-all" @click="chooseCategory('ყველა')">
                <span class="catalog-icon">✦</span>
                <span><strong>ყველა პროდუქტი</strong><small>{{ allProducts.length }} პროდუქტი</small></span>
                <ChevronRight :size="16" />
              </button>
              <div v-for="(category,index) in catalogCategories" :key="category.id || category.name" class="catalog-item" :class="{active:selectedCatalogCategory?.name===category.name}" @mouseenter="setCatalogPreview(category)" @focusin="setCatalogPreview(category)">
                <button class="catalog-card" @click="chooseCategory(category.name)">
                  <span class="catalog-icon">{{ catalogIcon(index + 1) }}</span>
                  <span><strong>{{ category.name }}</strong><small>{{ countCategoryTreeProducts(category) }} პროდუქტი</small></span>
                  <ChevronRight :size="16" />
                </button>
                <div v-if="category.children?.length" class="catalog-flyout">
                  <div class="catalog-flyout-head"><strong>{{ category.name }}</strong><button @click="chooseCategory(category.name)">ყველას ნახვა</button></div>
                  <button v-for="child in category.children" :key="child.id || child.name" @click="chooseCategory(child.name)">
                    <span>{{ child.name }}</span>
                    <small>{{ countCategoryTreeProducts(child) }} პროდუქტი</small>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </header>

    <main v-if="!paymentResultContent && !selectedProduct && !productsPage && !cartPage && !checkoutStep && !adminPage && !accountPage">
      <section class="hero wrap">
        <div class="hero-content">
          <span class="eyebrow"><span></span> ახალი სეზონის შეთავაზებები</span>
          <h1>ძლიერი ტექნიკა.<br><em>მარტივი არჩევანი.</em></h1>
          <p>ყველაფერი შენი მეურნეობისთვის — სანდო ბრენდები, პროფესიონალური კონსულტაცია და სწრაფი მიწოდება.</p>
          <div class="hero-actions"><a class="primary-btn" href="#" @click.prevent="openProductsPage('ყველა')">პროდუქციის ნახვა <ArrowRight :size="19" /></a>
            <!-- <button class="play-link"><span>▶</span> როგორ შევარჩიოთ?</button> -->
          </div>
          <div class="hero-stats"><div><strong>{{ companyYearsOnMarket }}+</strong><span>წელი ბაზარზე</span></div><div><strong>{{ categories.length.toLocaleString() }}</strong><span>კატეგორია</span></div><div><strong>{{ allProducts.length.toLocaleString() }}</strong><span>პროდუქტი</span></div></div>
        </div>
        <div class="hero-visual">
          <div class="hero-blob"></div>
          <img v-if="heroOfferProduct" :src="heroOfferProduct.image" :alt="heroOfferProduct.name" @error="useFallbackImage" />
          <img v-else src="/agrotrade_logo.png" alt="Agro Trade" />
          <button v-if="hasHeroOffer" class="floating-card price-card hero-offer-card" @click="openProductPage(heroOfferProduct)"><small>{{ heroOfferLabel }} — {{ discountPercent(heroOfferProduct) }}%</small><strong>{{ heroOfferProduct.price.toLocaleString() }} ₾</strong><span>{{ heroOfferProduct.oldPrice.toLocaleString() }} ₾</span></button>
          <div v-if="heroOfferProducts.length > 1" class="hero-offer-dots">
            <button v-for="(product, index) in heroOfferProducts" :key="product.id || product.name" :class="{active:index===heroOfferIndex}" @click="setHeroOffer(index)" :aria-label="`${product.name} ჩვენება`"></button>
          </div>
        </div>
      </section>

      <section class="benefits wrap" id="services">
        <div><Truck /><span><strong>სწრაფი მიწოდება</strong><small>მთელი საქართველოს მასშტაბით</small></span></div>
        <div><ShieldCheck /><span><strong>ოფიციალური გარანტია</strong><small>ყველა ტექნიკაზე</small></span></div>
        <div><PackageCheck /><span><strong>ნაწილები და სერვისი</strong><small>ერთ სივრცეში</small></span></div>
        <div><Phone /><span><strong>პროფესიონალური რჩევა</strong><small>დაგეხმარებით არჩევაში</small></span></div>
      </section>

      <section v-if="brands.length" class="brand-strip wrap" aria-label="ბრენდები"><span>ბრენდები, რომლებსაც ვენდობით</span><div class="brand-marquee"><div><b v-for="(brand,index) in [...brands, ...brands]" :key="brand + index">{{ brand }}</b></div></div></section>

      <section class="section wrap" id="categories">
        <div class="section-head"><div><span class="eyebrow">პოპულარული არჩევანი</span><h2>იპოვე კატეგორიის მიხედვით</h2></div><a href="#" @click.prevent="openProductsPage('ყველა')">ყველა კატეგორია <ArrowRight :size="18" /></a></div>
        <div v-if="categories.length" class="category-grid">
          <article v-for="category in categories" :key="category.name" @click="openProductsPage(category.name)">
            <img :src="category.image" :alt="category.name" />
            <div><small>{{ category.count }} პროდუქტი</small><h3>{{ category.name }}</h3><span>ნახვა <ArrowRight :size="16" /></span></div>
          </article>
        </div>
      </section>

      <section class="home-products section wrap">
        <div class="section-head"><div><span class="eyebrow">ხშირად ყიდულობენ</span><h2>ყველაზე მოთხოვნადი ტექნიკა</h2><small class="result-count">სწრაფი არჩევანი სეზონური სამუშაოებისთვის</small></div><a href="#" @click.prevent="openProductsPage('ყველა')">სრული კატალოგი <ArrowRight :size="18" /></a></div>
        <div v-if="bestSellerProducts.length" class="home-product-grid">
          <article v-for="product in bestSellerProducts" :key="product.id" class="home-product-card">
            <div class="home-product-media" @click="openProductPage(product)"><span class="badge" :class="{ out: !isInStock(product) }">{{ productBadge(product) }}</span><img :src="product.image" :alt="product.name"></div>
            <div class="home-product-info">
              <small>{{ product.category }}</small>
              <h3 @click="openProductPage(product)">{{ product.name }}</h3>
              <div class="home-product-bottom"><strong>{{ product.price.toLocaleString() }} ₾</strong><button :disabled="!isInStock(product)" @click="addToCart(product)"><ShoppingBag :size="18" /> {{ isInStock(product) ? 'დამატება' : 'არ არის' }}</button></div>
            </div>
          </article>
        </div>
      </section>

      <section class="consult-cta wrap">
        <div>
          <span class="eyebrow light">ვერ არჩევ?</span>
          <h2>გვითხარი რა სამუშაო გაქვს და სწორ ტექნიკას შეგარჩევინებთ</h2>
          <p>მიწის ფართობი, საწვავი, სიმძლავრე, ბიუჯეტი — კონსულტანტი სწრაფად დაგეხმარება არჩევაში.</p>
        </div>
        <div class="consult-actions">
          <a href="tel:+995598850503"><Phone :size="18" /> დარეკვა</a>
          <button @click="openProductsPage('მოტობლოკები')">მოტობლოკების ნახვა <ArrowRight :size="17" /></button>
        </div>
      </section>

      <section v-if="false" class="section products-section" id="products">
        <div class="wrap">
          <div class="section-head"><div><span class="eyebrow">{{ activeCategory === 'ყველა' ? 'რჩეული პროდუქცია' : 'კატეგორია' }}</span><h2>{{ activeCategory === 'ყველა' ? 'ყველაზე მოთხოვნადი' : activeCategory }}</h2><small class="result-count">ნაპოვნია {{ filteredProducts.length }} პროდუქტი</small></div><button class="filter-button"><SlidersHorizontal :size="17" /> ფილტრი</button></div>
          <div class="tabs"><button v-for="cat in topCategoryTabs" :key="cat" :class="{active: activeCategory === cat}" @click="activeCategory = cat">{{ cat }}</button></div>
          <div v-if="filteredProducts.length" class="product-grid">
            <article v-for="product in displayedProducts" :key="product.id" class="product-card">
              <div class="product-media" @click="openProductPage(product)"><span class="badge" :class="{ out: !isInStock(product) }">{{ productBadge(product) }}</span><button class="wish" :class="{active: wishlist.has(product.id)}" @click.stop="toggleWishlist(product.id)"><Heart :size="19" /></button><img :src="product.image" :alt="product.name" /></div>
              <div class="product-info"><small>{{ product.category }}</small><h3 @click="openProductPage(product)">{{ product.name }}</h3><div class="card-trust"><span>განვადება</span><span>გარანტია</span></div><div class="price-row"><div><strong>{{ product.price.toLocaleString() }} ₾</strong><del v-if="product.oldPrice">{{ product.oldPrice.toLocaleString() }} ₾</del></div><button :disabled="!isInStock(product)" @click="addToCart(product)"><ShoppingBag :size="19" /></button></div><button class="compare-toggle" :class="{active: compareIds.includes(product.id)}" @click="toggleCompare(product)"><Scale :size="16" /> {{ compareIds.includes(product.id) ? 'შედარებიდან მოხსნა' : 'შედარება' }}</button><p class="stock" :class="{ out: !isInStock(product) }"><Check v-if="isInStock(product)" :size="14" /><X v-else :size="14" /> {{ stockLabel(product) }}</p></div>
            </article>
          </div>
          <div v-if="visibleCount < filteredProducts.length" class="load-more"><p>ნაჩვენებია {{ visibleCount }} / {{ filteredProducts.length }} პროდუქტი</p><div><span :style="{width:(visibleCount/filteredProducts.length*100)+'%'}"></span></div><button @click="visibleCount += 12">მეტის ჩვენება <Plus :size="17" /></button></div>
          <div v-else class="empty-state"><Search /><h3>პროდუქტი ვერ მოიძებნა</h3><button @click="search=''; activeCategory='ყველა'">გასუფთავება</button></div>
        </div>
      </section>

      <section class="guide section wrap">
        <div class="section-head"><div><span class="eyebrow">მარტივი არჩევანი</span><h2>რისთვის გჭირდება ტექნიკა?</h2></div><p>აირჩიე სამუშაო — ჩვენ სწორ ტექნიკას გაჩვენებთ</p></div>
        <div class="guide-grid">
          <article class="guide-large"><div><span>დიდი ნაკვეთისთვის</span><h3>მიწის დამუშავება</h3><p>მოტობლოკები, კულტივატორები და ყველა საჭირო აქსესუარი.</p><button @click="openProductsPage('მოტობლოკები')">შერჩევა <ArrowRight :size="17" /></button></div></article>
          <article class="guide-small guide-power"><div><span>ენერგია ყველგან</span><h3>ელექტრო მომარაგება</h3><button @click="openProductsPage('გენერატორები')">გენერატორები <ChevronRight :size="16" /></button></div></article>
          <article class="guide-small guide-garden"><div><span>მოვლილი გარემო</span><h3>ბაღის მოვლა</h3><button @click="openProductsPage('ბაღის ტექნიკა')">ტექნიკის ნახვა <ChevronRight :size="16" /></button></div></article>
        </div>
      </section>

      <section class="promo wrap" id="offers"><div><span class="eyebrow light">შეზღუდული შეთავაზება</span><h2>მოამზადე მეურნეობა<br>ახალი სეზონისთვის</h2><p>არჩეულ ბაღის ტექნიკაზე ფასდაკლება 20%-მდე.</p><button @click="openOffersPage">შეთავაზებების ნახვა <ArrowRight :size="18" /></button></div><div class="promo-number"><small>ფასდაკლება</small><strong>20<sup>%</sup></strong><span>მდე</span></div></section>

      <section class="story-section about-section" id="service-info"><div class="wrap about-content"><div class="about-intro"><span class="eyebrow">თქვენი პარტნიორი სოფლის მეურნეობაში</span><h2>ჩვენ შესახებ</h2><p>„აგრო თრეიდი“ 2019 წლიდან აწვდის ქართულ ბაზარს სასოფლო-სამეურნეო ტექნიკას, სათადარიგო ნაწილებსა და პრაქტიკულ კონსულტაციას. ვარჩევთ ტექნიკას ისე, რომ მოერგოს რეალურ სამუშაოს, ბიუჯეტს და სეზონს.</p></div><div class="about-highlight"><strong>{{ companyYearsOnMarket }}+</strong><span>წელი ბაზარზე</span></div><div class="about-cards"><article><h3>რას გთავაზობთ</h3><p>მოტობლოკები, კულტივატორები, ტრაქტორები, ბაღის ტექნიკა, სათიბები, სათადარიგო ნაწილები და სერვისი.</p></article><article><h3>რატომ ჩვენ</h3><p>გამოცდილი გუნდი, სანდო ბრენდები, ხელმისაწვდომი პირობები და მხარდაჭერა შეძენის შემდეგაც.</p></article><article><h3>ფილიალები</h3><p>თბილისი — წერეთლის გამზ. N147<br>ზესტაფონი — რუსთაველის ქ. N60<br>ოკამი — მე-40 კმ</p></article></div><div v-if="brands.length" class="about-brands"><span>ბრენდები</span><p>{{ brands.join(', ') }}</p></div></div></section>

      <section class="faq-section wrap"><div><span class="eyebrow">ხშირი კითხვები</span><h2>ყველაფერი, რაც შეძენამდე უნდა იცოდე</h2><p>ვერ იპოვე პასუხი? დაგვირეკე და ჩვენი კონსულტანტი დაგეხმარება.</p><a href="tel:+995598850503"><Phone :size="17" /> +995 598 850 503</a></div><div class="faq-list"><article v-for="(faq, index) in [['რამდენ ხანში ხდება მიწოდება?', 'თბილისში შეკვეთა ბარდება 1–2 სამუშაო დღეში, რეგიონებში — 2–4 სამუშაო დღეში.'],['აქვს თუ არა ტექნიკას გარანტია?', 'დიახ, ყველა ტექნიკას ახლავს ოფიციალური გარანტია. ვადა დამოკიდებულია კონკრეტულ ბრენდსა და მოდელზე.'],['შესაძლებელია ადგილზე კონსულტაცია?', 'რა თქმა უნდა. ჩვენს შოურუმში სპეციალისტი ტექნიკას ადგილზე გაჩვენებთ და შერჩევაში დაგეხმარებათ.'],['გაქვთ სათადარიგო ნაწილები და სერვისი?', 'დიახ, გვაქვს როგორც საგარანტიო სერვისი, ისე ყველაზე მოთხოვნადი სათადარიგო ნაწილები.']]" :key="faq[0]" :class="{open:openFaq===index}"><button @click="openFaq = openFaq === index ? -1 : index"><span>{{ faq[0] }}</span><Plus :size="20" /></button><p>{{ faq[1] }}</p></article></div></section>

      <section class="contact-section wrap" id="contact">
        <div>
          <span class="eyebrow light">კონტაქტი</span>
          <h2>გვეწვიე ან დაგვიკავშირდი</h2>
          <p>შოურუმში ადგილზე ნახავ ტექნიკას, მიიღებ კონსულტაციას და შეარჩევ სწორ მოდელს.</p>
        </div>
        <div class="contact-card">
          <div class="contact-main-links">
            <a class="contact-phone" href="tel:+995598850503"><Phone :size="19" /> +995 598 850 503</a>
            <a href="mailto:agrotradegeo@gmail.com">Email: agrotradegeo@gmail.com</a>
          </div>
          <div class="contact-socials" aria-label="სოციალური ქსელები">
            <a href="https://www.facebook.com/AGROTRADEGEO" target="_blank" rel="noopener">Facebook</a>
            <a href="https://www.instagram.com/agrotradegeo/" target="_blank" rel="noopener">Instagram</a>
            <a href="https://www.tiktok.com/@agro.trade3" target="_blank" rel="noopener">TikTok</a>
          </div>
          <div class="contact-locations">
            <p><MapPin :size="16" /><span><strong>თბილისი</strong> წერეთლის გამზ. N147 <a href="https://maps.app.goo.gl/PvjEHT4oDRApxGAeA" target="_blank" rel="noopener">რუკაზე ნახვა</a></span></p>
            <p><MapPin :size="16" /><span><strong>ოკამი</strong> თბილისი-სენაკი-ლესელიძის მე-40 კილომეტრი <a href="https://maps.app.goo.gl/3NDu29YprotRacKc9" target="_blank" rel="noopener">რუკაზე ნახვა</a></span></p>
            <p><MapPin :size="16" /><span><strong>ზესტაფონი</strong> რუსთაველის ქ. N60 <a href="https://maps.app.goo.gl/BevpdezxUeabjfyn9" target="_blank" rel="noopener">რუკაზე ნახვა</a></span></p>
          </div>
        </div>
      </section>
    </main>

    <main v-else-if="productsPage" class="products-page">
      <section class="section products-section products-page-section" id="products">
        <div class="wrap">
          <button class="back-link products-back" @click="goHome"><ArrowLeft :size="18" /> მთავარზე დაბრუნება</button>
          <div class="section-head"><div><span class="eyebrow">{{ activeCategory === 'ყველა' ? 'ყველა პროდუქტი' : 'კატეგორია' }}</span><h2>{{ activeCategory === 'ყველა' ? 'პროდუქციის კატალოგი' : activeCategory }}</h2><small class="result-count">ნაპოვნია {{ filteredProducts.length }} პროდუქტი</small></div><button class="filter-button" @click="filterOpen=true"><SlidersHorizontal :size="17" /> ფილტრი</button></div>
          <div class="catalog-layout">
            <aside class="filter-sidebar">
              <section class="filter-group">
                <h3>ფასის ფილტრი</h3>
                <div class="price-filter">
                  <label><span>მინ.</span><input v-model="minPrice" type="number" min="0" placeholder="0" @input="visibleCount=12"></label>
                  <label><span>მაქს.</span><input v-model="maxPrice" type="number" min="0" placeholder="15000" @input="visibleCount=12"></label>
                </div>
              </section>
              <section class="filter-group">
                <h3>პროდუქტის კატეგორიები</h3>
                <div class="category-filter-list">
                  <button class="category-filter-row" :class="{active:activeCategory==='ყველა'}" @click="selectFilterCategory('ყველა')"><span>ყველა</span><b>{{ allProducts.length }}</b></button>
                  <div v-for="category in catalogCategories" :key="category.id || category.name" class="category-filter-node">
                    <div class="category-filter-row" :class="{active:activeCategory===category.name}">
                      <button type="button" @click="selectFilterCategory(category.name)"><span>{{ category.name }}</span><b>{{ countCategoryTreeProducts(category) }}</b></button>
                      <button v-if="category.children?.length" type="button" class="category-filter-arrow" :class="{open:isFilterCategoryExpanded(category)}" @click.stop="toggleFilterCategory(category)"><ChevronDown :size="14" /></button>
                    </div>
                    <div v-if="category.children?.length && isFilterCategoryExpanded(category)" class="category-filter-children">
                      <button v-for="child in category.children" :key="child.id || child.name" :class="{active:activeCategory===child.name}" @click="selectFilterCategory(child.name)"><span>{{ child.name }}</span><b>{{ countCategoryTreeProducts(child) }}</b></button>
                    </div>
                  </div>
                </div>
              </section>
              <section class="filter-group">
                <h3>ბრენდი</h3>
                <div class="filter-pills">
                  <button v-for="brand in brandFilterOptions" :key="brand" :class="{active:selectedBrand===brand}" @click="selectedBrand=brand; visibleCount=12">{{ brand }}</button>
                </div>
              </section>
              <section class="filter-group">
                <h3>მდგომარეობა</h3>
                <label class="filter-check"><input v-model="onlyInStock" type="checkbox" @change="visibleCount=12"><span></span> მხოლოდ მარაგში</label>
                <label class="filter-check"><input v-model="onlyDiscounted" type="checkbox" @change="visibleCount=12"><span></span> ფასდაკლებული</label>
              </section>
              <section class="filter-group">
                <h3>სორტირება</h3>
                <select v-model="sortBy" @change="visibleCount=12">
                  <option value="popular">პოპულარული</option>
                  <option value="price-low">იაფიდან ძვირისკენ</option>
                  <option value="price-high">ძვირიდან იაფისკენ</option>
                  <option value="discount">ფასდაკლებით</option>
                  <option value="new">ახალი</option>
                </select>
              </section>
              <button class="sidebar-clear" @click="clearFilters">გასუფთავება</button>
            </aside>
            <div class="catalog-results">
              <div class="tabs"><button v-for="cat in topCategoryTabs" :key="cat" :class="{active: activeCategory === cat}" @click="activeCategory = cat; visibleCount = 12">{{ cat }}</button></div>
              <div v-if="filteredProducts.length" class="product-grid">
                <article v-for="product in displayedProducts" :key="product.id" class="product-card">
                  <div class="product-media" @click="openProductPage(product)"><span class="badge" :class="{ out: !isInStock(product) }">{{ productBadge(product) }}</span><button class="wish" :class="{active: wishlist.has(product.id)}" @click.stop="toggleWishlist(product.id)"><Heart :size="19" /></button><img :src="product.image" :alt="product.name" /></div>
                  <div class="product-info"><small>{{ product.category }}</small><h3 @click="openProductPage(product)">{{ product.name }}</h3><div class="card-trust"><span>განვადება</span><span>გარანტია</span></div><div class="price-row"><div><strong>{{ product.price.toLocaleString() }} ₾</strong><del v-if="product.oldPrice">{{ product.oldPrice.toLocaleString() }} ₾</del></div><button :disabled="!isInStock(product)" @click="addToCart(product)"><ShoppingBag :size="19" /></button></div><button class="compare-toggle" :class="{active: compareIds.includes(product.id)}" @click="toggleCompare(product)"><Scale :size="16" /> {{ compareIds.includes(product.id) ? 'შედარებიდან მოხსნა' : 'შედარება' }}</button><p class="stock" :class="{ out: !isInStock(product) }"><Check v-if="isInStock(product)" :size="14" /><X v-else :size="14" /> {{ stockLabel(product) }}</p></div>
                </article>
              </div>
              <div v-if="visibleCount < filteredProducts.length" class="load-more"><p>ნაჩვენებია {{ visibleCount }} / {{ filteredProducts.length }} პროდუქტი</p><div><span :style="{width:(visibleCount/filteredProducts.length*100)+'%'}"></span></div><button @click="visibleCount += 12">მეტის ჩვენება <Plus :size="17" /></button></div>
              <div v-else class="empty-state"><Search /><h3>პროდუქტი ვერ მოიძებნა</h3><button @click="search=''; activeCategory='ყველა'; clearFilters()">გასუფთავება</button></div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <main v-else-if="paymentResultContent" class="checkout-page wrap">
      <div class="success-card" :class="`payment-result-${paymentResult}`">
        <span><Check v-if="paymentResult === 'success'" /><X v-else /></span>
        <small>{{ paymentResultContent.eyebrow }}</small>
        <h1>{{ paymentResultContent.title }}</h1>
        <p>{{ paymentResultContent.text }}</p>
        <p v-if="paymentResultOrder">შეკვეთის ნომერი: <strong>#{{ paymentResultOrder }}</strong></p>
        <button class="primary-btn" @click="goHome">მთავარ გვერდზე დაბრუნება</button>
      </div>
    </main>

    <main v-else-if="selectedProduct" class="product-page wrap">
      <button class="back-link" @click="backToProducts"><ArrowLeft :size="18" /> პროდუქტებზე დაბრუნება</button>
      <div class="breadcrumbs">მთავარი <ChevronRight :size="14" /> {{ selectedProduct.category }} <ChevronRight :size="14" /> {{ selectedProduct.name }}</div>
      <section class="product-detail">
        <div class="gallery"><div class="thumbs"><button v-for="(image, index) in selectedProductImages" :key="image" :class="{active:index===selectedProductImageIndex}" @click="selectedProductImageIndex=index"><img :src="image" :alt="selectedProduct.name" @error="useFallbackImage" /></button></div><div class="main-image zoomable" @click="zoomOpen=true"><span class="badge" :class="{ out: !isInStock(selectedProduct) }">{{ productBadge(selectedProduct) }}</span><span class="zoom-hint">⌕ ფოტოს გადიდება</span><img :src="selectedProductImage" :alt="selectedProduct.name" @error="useFallbackImage" /></div></div>
        <div class="detail-info"><span class="detail-category">{{ selectedProduct.category }} • SKU: {{ selectedProduct.sku || selectedProduct.id }}</span><h1>{{ selectedProduct.name }}</h1><p class="detail-copy">{{ selectedProductDescription }}</p><div class="detail-price"><strong>{{ selectedProduct.price.toLocaleString() }} ₾</strong><del v-if="selectedProduct.oldPrice">{{ selectedProduct.oldPrice.toLocaleString() }} ₾</del><span v-if="selectedProduct.oldPrice">ზოგავ {{ formatMoney(discountAmount(selectedProduct)) }} ₾</span></div><div class="monthly-payment"><CreditCard :size="18" /><span><small>განვადებით</small><strong>თვეში {{ monthlyPayment.toLocaleString() }} ₾-დან</strong></span><em>24 თვემდე</em></div><div class="installment-strip"><CreditCard :size="17" /><span><strong>განვადება ხელმისაწვდომია</strong><small>BOG / TBC  პირობებით</small></span></div><div class="availability" :class="{ out: !isInStock(selectedProduct) }"><span><Check v-if="isInStock(selectedProduct)" /><X v-else /> {{ stockLabel(selectedProduct) }}</span><small v-if="isInStock(selectedProduct)"><Clock3 :size="15" /> გაგზავნა დღესვე</small><small v-else>დაგვიკავშირდი ხელმისაწვდომობისთვის</small></div><ul class="spec-list"><li v-for="spec in selectedProduct.specs"><Check :size="16" /> {{ spec }}</li></ul><div class="buy-row"><div class="quantity"><button @click="detailQty=Math.max(1,detailQty-1)"><Minus :size="16" /></button><span>{{ detailQty }}</span><button @click="detailQty++"><Plus :size="16" /></button></div><button class="primary-btn buy-button" :disabled="!isInStock(selectedProduct)" @click="addToCart(selectedProduct, detailQty)"><ShoppingBag :size="19" /> {{ isInStock(selectedProduct) ? 'კალათაში დამატება' : 'არ არის მარაგში' }}</button><button class="icon-btn detail-wish" @click="toggleWishlist(selectedProduct.id)"><Heart :fill="wishlist.has(selectedProduct.id) ? 'currentColor' : 'none'" /></button></div><div class="product-contact-actions"><a href="tel:+995598850503"><Phone :size="17" /> დარეკვა</a><a :href="`https://wa.me/995598850503?text=${encodeURIComponent('გამარჯობა, მაინტერესებს: ' + selectedProduct.name)}`" target="_blank" rel="noopener">WhatsApp-ში კითხვა</a></div><button class="detail-compare" :class="{active: compareIds.includes(selectedProduct.id)}" @click="toggleCompare(selectedProduct)"><Scale :size="17" /> {{ compareIds.includes(selectedProduct.id) ? 'შედარებიდან მოხსნა' : 'პროდუქტის შედარება' }}</button><div class="safe-buy"><ShieldCheck /><span><strong>უსაფრთხო შენაძენი</strong><small>ოფიციალური გარანტია და დაბრუნება 14 დღის განმავლობაში</small></span></div></div>
      </section>
      <section class="product-description-panel">
        <div class="description-tabs"><button :class="{active:detailTab==='description'}" @click="detailTab='description'">აღწერა</button><button :class="{active:detailTab==='specs'}" @click="detailTab='specs'">მახასიათებლები</button><button :class="{active:detailTab==='delivery'}" @click="detailTab='delivery'">მიწოდება და გარანტია</button></div>
        <div v-if="detailTab==='description'" class="description-grid">
          <article class="description-copy">
            <span class="eyebrow">პროდუქტის აღწერა</span>
            <h2>{{ selectedProduct.name }}</h2>
            <p>{{ selectedProductDescription }}</p>
            <p>შეძენამდე შეგიძლია დაგვირეკო ან მოგვწერო WhatsApp-ში. კონსულტანტი შეგირჩევს სწორ სიმძლავრეს, აგიხსნის განვადების პირობებს და გეტყვის მიწოდების ზუსტ ვადას.</p>
          </article>
          <aside class="description-highlights">
            <div><Check :size="17" /><span><strong>ოფიციალური გარანტია</strong><small>სერვისი და მხარდაჭერა შეძენის შემდეგაც</small></span></div>
            <div><Truck :size="17" /><span><strong>მიწოდება რეგიონებში</strong><small>თბილისი, ოკამი, ზესტაფონი და სხვა ქალაქები</small></span></div>
            <div><CreditCard :size="17" /><span><strong>მოქნილი გადახდა</strong><small>ბარათი, განვადება და ნაწილ-ნაწილ გადახდა</small></span></div>
          </aside>
        </div>
        <div v-else-if="detailTab==='specs'" class="description-grid">
          <article class="description-copy">
            <span class="eyebrow">ტექნიკური დეტალები</span>
            <h2>მახასიათებლები</h2>
            <ul class="description-specs"><li v-for="spec in selectedProduct.specs" :key="spec"><Check :size="16" /> {{ spec }}</li><li><Check v-if="isInStock(selectedProduct)" :size="16" /><X v-else :size="16" /> {{ stockLabel(selectedProduct) }}</li><li><Check :size="16" /> SKU: AG-{{ 3600 + selectedProduct.id }}</li></ul>
          </article>
          <aside class="description-highlights">
            <div><PackageCheck :size="17" /><span><strong>შეფუთვა შემოწმებულია</strong><small>გაცემამდე პროდუქტი მოწმდება ვიზუალურად</small></span></div>
            <div><Scale :size="17" /><span><strong>შედარება შეგიძლია</strong><small>შეადარე ორ პროდუქტს შორის ფასი და პარამეტრები</small></span></div>
            <div><Phone :size="17" /><span><strong>კონსულტაცია შერჩევამდე</strong><small>გეტყვით შეესაბამება თუ არა შენს სამუშაოს</small></span></div>
          </aside>
        </div>
        <div v-else class="description-grid">
          <article class="description-copy">
            <span class="eyebrow">მიწოდება და გარანტია</span>
            <h2>როგორ მიიღებ პროდუქტს</h2>
            <p>შეკვეთის შემდეგ დაგიკავშირდებით დეტალების დასაზუსტებლად. შესაძლებელია კურიერით მიწოდება ან მაღაზიიდან გატანა თბილისის, ოკამის და ზესტაფონის ფილიალებიდან.</p>
            <p>პროდუქტზე ვრცელდება ოფიციალური გარანტია. პრობლემის შემთხვევაში მიიღებ კონსულტაციას, სერვისს და საჭიროებისას დაბრუნების პირობებს 14 დღის ფარგლებში.</p>
          </article>
          <aside class="description-highlights">
            <div><Truck :size="17" /><span><strong>მიწოდება რეგიონებში</strong><small>ვადას ოპერატორი დაგიზუსტებს შეკვეთისას</small></span></div>
            <div><MapPin :size="17" /><span><strong>ფილიალიდან გატანა</strong><small>თბილისი, ოკამი ან ზესტაფონი</small></span></div>
            <div><ShieldCheck :size="17" /><span><strong>გარანტია და დაბრუნება</strong><small>ოფიციალური მხარდაჭერა შეძენის შემდეგ</small></span></div>
          </aside>
        </div>
      </section>
      <section v-if="relatedProducts.length" class="related-products">
        <div class="section-head"><div><span class="eyebrow">მსგავსი პროდუქტები</span><h2>შეიძლება ესეც დაგაინტერესოს</h2></div></div>
        <div class="related-grid">
          <article v-for="product in relatedProducts" :key="product.id" @click="openProductPage(product)">
            <img :src="product.image" :alt="product.name">
            <span>{{ product.category }}</span>
            <strong>{{ product.name }}</strong>
            <b>{{ product.price.toLocaleString() }} ₾</b>
          </article>
        </div>
      </section>
      <section class="product-faq-panel">
        <div class="section-head"><div><span class="eyebrow">ხშირი კითხვები</span><h2>რას კითხულობენ ამ პროდუქტზე?</h2></div></div>
        <div class="product-faq-list">
          <article v-for="(faq,index) in [
            ['შეიძლება განვადებით ყიდვა?', 'კი, შესაძლებელია BOG/TBC პირობებით. ზუსტი თვიური თანხა ბანკის პირობაზე და არჩეულ ვადაზეა დამოკიდებული.'],
            ['მიწოდება რეგიონებში გაქვთ?', 'კი, შეკვეთის შემდეგ ოპერატორი დაგიზუსტებს მისამართს, ვადას და მიწოდების ღირებულებას.'],
            ['როგორ მივხვდე ეს მოდელი მჭირდება თუ არა?', 'დაგვირეკე ან WhatsApp-ში მოგვწერე. გკითხავთ სამუშაოს ტიპს და ნაკვეთის ზომას, შემდეგ სწორ მოდელს შეგირჩევთ.'],
            ['გარანტია აქვს?', 'პროდუქტზე მოქმედებს ოფიციალური გარანტია და შეძენის შემდეგაც შეგიძლია სერვისის/კონსულტაციის მიღება.']
          ]" :key="faq[0]" :class="{open:productFaqOpen===index}">
            <button @click="productFaqOpen = productFaqOpen === index ? -1 : index"><span>{{ faq[0] }}</span><Plus :size="17" /></button>
            <p>{{ faq[1] }}</p>
          </article>
        </div>
      </section>
    </main>

    <main v-else-if="cartPage" class="cart-page wrap" @click="event => event.target.closest('.checkout-button') && startCheckout()">
      <div class="cart-title"><div><button class="back-link" @click="goHome"><ArrowLeft :size="18" /> შოპინგის გაგრძელება</button><h1>შენი კალათა</h1><p>{{ cartItems.length }} პროდუქტი</p></div><div class="checkout-steps"><span class="active"><b>1</b> კალათა</span><i></i><span><b>2</b> მონაცემები</span><i></i><span><b>3</b> გადახდა</span></div></div>
      <div class="cart-layout">
        <section class="cart-products">
          <div class="cart-head"><span>პროდუქტი</span><span>რაოდენობა</span><span>ჯამი</span></div>
          <article v-for="item in cartItems" :key="item.id" class="cart-item"><div class="cart-product"><div class="cart-image"><img :src="item.image" :alt="item.name"></div><div><small>{{ item.category }}</small><h3>{{ item.name }}</h3><span class="stock" :class="{ out: !isInStock(item) }"><Check v-if="isInStock(item)" :size="13" /><X v-else :size="13" /> {{ stockLabel(item) }}</span></div></div><div class="quantity"><button @click="item.qty=Math.max(1,item.qty-1)"><Minus :size="15" /></button><span>{{ item.qty }}</span><button @click="item.qty++"><Plus :size="15" /></button></div><strong>{{ (item.price*item.qty).toLocaleString() }} ₾</strong><button class="remove-item" @click="removeFromCart(item.id)"><Trash2 :size="18" /></button></article>
          <div v-if="!cartItems.length" class="cart-empty"><ShoppingBag :size="28" /><h3>კალათა ცარიელია</h3><button @click="openProductsPage('ყველა')">პროდუქციის ნახვა</button></div>
          <div v-else class="cart-note"><PackageCheck /><div><strong>მიწოდების პირობები</strong><p>{{ deliveryNoteText }}</p></div></div>
          <div v-if="cartItems.length" class="cart-recommendations">
            <h3>ხშირად ამატებენ შეკვეთას</h3>
            <article v-for="product in cartRecommendations" :key="product.id">
              <img :src="product.image" :alt="product.name">
              <span><strong>{{ product.name }}</strong><small>{{ product.price.toLocaleString() }} ₾</small></span>
              <button @click="addToCart(product)">დამატება</button>
            </article>
          </div>
        </section>
        <aside class="order-summary"><h2>შეკვეთის შეჯამება</h2><div class="summary-line"><span>პროდუქტები</span><strong>{{ cartSubtotal.toLocaleString() }} ₾</strong></div><div class="summary-line"><span>მიწოდება</span><strong :class="{free: !deliveryPrice}">{{ deliveryPriceText }}</strong></div><div class="summary-total"><span>სულ</span><strong>{{ orderTotal.toLocaleString() }} ₾</strong></div><div class="next-payment-note"><CreditCard :size="18" /><span><strong>გადახდის მეთოდს შემდეგ ნაბიჯზე აირჩევ</strong><small>განვადება, ნაწილ-ნაწილ ან ბარათით გადახდა</small></span></div><button class="checkout-button">შეკვეთის გაგრძელება <ArrowRight :size="19" /></button><p class="secure-note"><ShieldCheck :size="15" /> დაცული და უსაფრთხო გადახდა</p></aside>
      </div>
    </main>

    <main v-else-if="accountPage" class="account-page wrap"><div class="account-head"><button class="back-link" @click="goHome"><ArrowLeft :size="18" /> მთავარზე დაბრუნება</button><div><small>პირადი კაბინეტი</small><h1>{{ currentUser?.firstName }} {{ currentUser?.lastName }}</h1></div><button @click="logout">გამოსვლა</button></div><div class="account-grid"><section class="account-panel"><div class="panel-head"><div><h2>პირადი ინფორმაცია</h2><p>სახელი, ელფოსტა და მობილური</p></div><button @click="profileEditing=!profileEditing">{{ profileEditing ? 'დახურვა' : 'რედაქტირება' }}</button></div><div v-if="!profileEditing" class="account-info"><span><small>სახელი</small><strong>{{ currentUser?.firstName }} {{ currentUser?.lastName }}</strong></span><span><small>მობილური</small><strong>{{ currentUser?.phone }}</strong></span><span><small>ელფოსტა</small><strong>{{ currentUser?.email }}</strong><em class="email-verify-badge" :class="{verified: currentUser?.isEmailVerified}">{{ currentUser?.isEmailVerified ? 'დადასტურებულია' : 'დასადასტურებელია' }}</em><button v-if="!currentUser?.isEmailVerified" type="button" class="email-verify-resend" @click="resendVerificationEmail">ლინკის გაგზავნა</button></span><span><small>მისამართი</small><strong>{{ currentUser?.city || 'ქალაქი არ არის მითითებული' }}</strong><em>{{ buildProfileAddress(currentUser) || 'მისამართი არ არის მითითებული' }}</em><em v-if="currentUser?.addressNote">{{ currentUser.addressNote }}</em></span></div><form v-else class="account-form" @submit.prevent="saveProfile"><label><span>სახელი</span><input v-model="profileForm.firstName"></label><label><span>გვარი</span><input v-model="profileForm.lastName"></label><label><span>მობილური</span><input v-model="profileForm.phone"></label><label><span>ელფოსტა</span><input v-model="profileForm.email" type="email" :disabled="currentUser?.isEmailVerified"><small v-if="currentUser?.isEmailVerified">დადასტურებული ელფოსტა არ რედაქტირდება.</small></label><label><span>ქალაქი / სოფელი</span><input v-model="profileForm.city" placeholder="მაგ: თბილისი"></label><label><span>რეგიონი / უბანი</span><input v-model="profileForm.region" placeholder="მაგ: დიდუბე"></label><label><span>ქუჩა</span><input v-model="profileForm.street" placeholder="ქუჩის დასახელება"></label><label><span>კორპუსი / ბინა</span><input v-model="profileForm.building" placeholder="კორპუსი, სადარბაზო, ბინა"></label><label class="full"><span>დამატებითი მინიშნება</span><input v-model="profileForm.addressNote" placeholder="მაგ: შესასვლელი ეზოდან"></label><button type="button" @click="profileEditing=false; syncProfileForm()">გაუქმება</button><button type="submit">შენახვა</button></form></section><section class="account-panel account-orders"><div v-if="!selectedUserOrder"><div class="panel-head"><div><h2>ჩემი შეკვეთები</h2><p>{{ userOrdersLoading ? 'იტვირთება...' : `${userOrders.length} შეკვეთა` }}</p></div><button @click="loadUserOrders">განახლება</button></div><OrderTable :orders="userOrders" :labels="orderTableLabels" show-date :format-date="formatOrderDate" :empty-text="!userOrdersLoading ? 'შეკვეთები ჯერ არ გაქვს.' : ''" @open="openUserOrder" /></div><div v-else class="account-order-detail"><div class="admin-order-detail-head"><button class="back-link" @click="closeUserOrder"><ArrowLeft :size="18" /> შეკვეთებში დაბრუნება</button><div><small>{{ formatOrderDate(selectedUserOrder.createdAt) }}</small><h2>{{ selectedUserOrder.orderNumber }}</h2></div><em>{{ selectedUserOrder.status }}</em></div><div class="admin-order-detail-grid"><article><small>გადახდა</small><strong>{{ selectedUserOrder.paymentLabel }}</strong><span>ჯამი: {{ Number(selectedUserOrder.total).toLocaleString() }} ₾</span></article><article><small>მიწოდება</small><strong>{{ deliveryMethodLabel(selectedUserOrder.delivery?.method) }}</strong><span>{{ selectedUserOrder.delivery?.method === 'pickup' ? selectedUserOrder.delivery?.pickupBranch : `${selectedUserOrder.delivery?.city}, ${selectedUserOrder.delivery?.address}` }}</span></article><article><small>სტატუსი</small><strong>{{ selectedUserOrder.status }}</strong><span>{{ selectedUserOrder.orderNumber }}</span></article></div><div class="admin-order-items"><h3>შეძენილი პროდუქტები</h3><div v-for="item in selectedUserOrder.items" :key="item.id" class="admin-order-item"><img :src="item.productImageUrl || productImageFallback" :alt="item.productName" @error="useFallbackImage"><span><strong>{{ item.productName }}</strong><small>{{ item.productSku || item.productCategory || 'პროდუქტი' }}</small></span><b>{{ item.quantity }} × {{ Number(item.unitPrice).toLocaleString() }} ₾</b><strong>{{ Number(item.lineTotal).toLocaleString() }} ₾</strong></div></div><div class="admin-order-totals"><span><small>პროდუქტები</small><strong>{{ Number(selectedUserOrder.subtotal).toLocaleString() }} ₾</strong></span><span><small>მიწოდება</small><strong>{{ Number(selectedUserOrder.deliveryPrice).toLocaleString() }} ₾</strong></span><span><small>სულ</small><strong>{{ Number(selectedUserOrder.total).toLocaleString() }} ₾</strong></span></div></div></section></div></main>

    <main v-else-if="checkoutStep === 2" class="checkout-page wrap"><div class="checkout-page-head"><button class="back-link" @click="openCart"><ArrowLeft :size="18" /> კალათაში დაბრუნება</button><div class="checkout-steps"><span><b>1</b> კალათა</span><i></i><span class="active"><b>2</b> მონაცემები</span><i></i><span><b>3</b> გადახდა</span></div></div><div class="checkout-grid"><section class="checkout-form-card"><div class="form-heading"><b>01</b><div><h1>საკონტაქტო ინფორმაცია</h1><p>შეკვეთის დასადასტურებლად</p></div></div><div class="form-grid"><label><span>სახელი *</span><input v-model="checkoutForm.firstName"></label><label><span>გვარი *</span><input v-model="checkoutForm.lastName"></label><label><span>ტელეფონი *</span><input v-model="checkoutForm.phone"></label><label><span>ელფოსტა</span><input v-model="checkoutForm.email"></label></div><div class="form-heading second"><b>02</b><div><h1>მიწოდების მისამართი</h1><p>სად მოგაწოდოთ შეკვეთა?</p></div></div><div class="delivery-tabs"><button :class="{active:deliveryMethod==='courier'}" @click="deliveryMethod='courier'"><Truck :size="17" /> კურიერით</button><button :class="{active:deliveryMethod==='pickup'}" @click="deliveryMethod='pickup'"><MapPin :size="17" /> მაღაზიიდან გატანა</button></div><div v-if="deliveryMethod==='pickup'" class="branch-options"><label><input v-model="checkoutForm.pickupBranch" type="radio" name="branch" value="tbilisi"><span><strong>თბილისი</strong><small>წერეთლის გამზ. N147</small></span></label><label><input v-model="checkoutForm.pickupBranch" type="radio" name="branch" value="okami"><span><strong>ოკამი</strong><small>მე-40 კილომეტრი</small></span></label><label><input v-model="checkoutForm.pickupBranch" type="radio" name="branch" value="zestafoni"><span><strong>ზესტაფონი</strong><small>რუსთაველის ქ. N60</small></span></label></div><div class="form-grid"><label><span>ქალაქი / სოფელი *</span><input v-model="checkoutForm.city" placeholder="ქალაქი ან სოფელი"></label><label><span>მისამართი *</span><input v-model="checkoutForm.address"></label><label class="full"><span>შენიშვნა</span><textarea v-model="checkoutForm.note" placeholder="დამატებითი ინფორმაცია"></textarea></label></div><p v-if="orderError" class="admin-form-error">{{ orderError }}</p><button class="checkout-button wide" @click="nextToPayment">გადახდაზე გადასვლა <ArrowRight :size="19" /></button></section><aside class="mini-summary"><h2>შენი შეკვეთა</h2><div v-for="item in cartItems" class="mini-item"><img :src="item.image"><span><strong>{{item.name}}</strong><small>{{item.qty}} × {{item.price.toLocaleString()}} ₾</small></span></div><div class="summary-line"><span>მიწოდება</span><strong :class="{free: !deliveryPrice}">{{ deliveryPriceText }}</strong></div><div class="summary-total"><span>სულ</span><strong>{{orderTotal.toLocaleString()}} ₾</strong></div></aside></div></main>

    <main v-else-if="checkoutStep === 3" class="checkout-page wrap"><div v-if="!orderPlaced"><div class="checkout-page-head"><button class="back-link" @click="backToCheckoutDetails"><ArrowLeft :size="18" /> მონაცემებზე დაბრუნება</button><div class="checkout-steps"><span><b>1</b> კალათა</span><i></i><span><b>2</b> მონაცემები</span><i></i><span class="active"><b>3</b> გადახდა</span></div></div><div class="checkout-grid"><section class="checkout-form-card"><div class="form-heading"><b>03</b><div><h1>გადახდის მეთოდი</h1><p>აირჩიე სასურველი ბანკი</p></div></div><div class="payment-options checkout-payments"><label v-for="m in [{id:'bog-installment',logo:'BOG',logoSrc:publicAssetUrl('bank-logos/bog.png'),cls:'bog',name:'BOG განვადება',sub:'3–48 თვე'},{id:'bog-parts',logo:'BOG',logoSrc:publicAssetUrl('bank-logos/bog.png'),cls:'bog',name:'BOG ნაწილ-ნაწილ',sub:'4 თანაბარი გადახდა'},{id:'tbc',logo:'TBC',logoSrc:publicAssetUrl('bank-logos/tbc.png'),cls:'tbc',name:'TBC განვადება',sub:'მოქნილი პირობები'},{id:'liberty',logo:'Liberty',logoSrc:publicAssetUrl('bank-logos/liberty.png'),cls:'liberty',name:'Liberty გადახდა',sub:'უსაფრთხო გადახდა'}]" :class="{selected:paymentMethod===m.id}"><input v-model="paymentMethod" type="radio" :value="m.id"><span class="bank-logo" :class="[m.cls, {'has-image': m.logoSrc}]"><img v-if="m.logoSrc" :src="m.logoSrc" :alt="m.logo"><template v-else>{{m.logo}}</template></span><span><strong>{{m.name}}</strong><small>{{m.sub}}</small></span><i></i></label></div><div class="demo-notice"><ShieldCheck /><p><strong>უსაფრთხო გადახდა</strong><br>BOG განვადება, Liberty და სხვა მეთოდები გაიხსნება ბანკის დაცულ გვერდზე.</p></div><p v-if="orderError" class="admin-form-error">{{ orderError }}</p><button class="checkout-button wide" :disabled="orderSaving" @click="placeOrder">{{ orderSaving ? 'ინახება...' : `შეკვეთის დადასტურება — ${orderTotal.toLocaleString()} ₾` }}</button></section><aside class="mini-summary"><h2>მიწოდება</h2><p>{{ checkoutForm.firstName }} {{ checkoutForm.lastName }}<br>{{ checkoutForm.phone }}<br>{{ deliveryMethod === 'pickup' ? 'გატანა ფილიალიდან' : `${checkoutForm.city}, ${checkoutForm.address}` }}</p><div class="summary-line"><span>პროდუქტები</span><strong>{{ cartSubtotal.toLocaleString() }} ₾</strong></div><div class="summary-line"><span>მიწოდება</span><strong :class="{free: !deliveryPrice}">{{ deliveryPriceText }}</strong></div><div class="summary-total"><span>სულ</span><strong>{{orderTotal.toLocaleString()}} ₾</strong></div></aside></div></div><div v-else class="success-card"><span><Check /></span><small>შეკვეთა მიღებულია</small><h1>მადლობა შენაძენისთვის!</h1><p>შეკვეთის ნომერია <strong>#{{ placedOrder?.orderNumber || 'AT' }}</strong></p><button class="primary-btn" @click="goHome">მთავარ გვერდზე დაბრუნება</button></div></main>

    <main v-else class="admin-shell">
      <aside class="admin-sidebar"><a class="logo image-logo admin-image-logo" href="#" @click.prevent="goHome"><img src="/agrotrade_logo.png" alt="Agro Trade Admin"></a><nav><button v-for="item in adminNavItems" :key="item.id" :class="{active:adminTab===item.id}" @click="selectAdminTab(item.id)"><span>{{ item.label }}</span><b v-if="item.count">{{ item.count }}</b></button></nav><button class="admin-exit" @click="goHome"><ArrowLeft :size="17" /> მთავარზე დაბრუნება</button></aside>
      <section class="admin-content"><header class="admin-header"><div><h1>{{ adminTabTitle }}</h1></div><div class="admin-account-actions"><button><CircleUserRound /> {{ currentUser?.firstName || 'Admin' }} • {{ currentUser?.role || 'admin' }}</button><button class="admin-logout-button" @click="logout">გამოსვლა</button></div></header>
        <template v-if="adminTab==='overview'">
          <div class="stat-grid"><article v-for="stat in adminStats" :key="stat.label"><span>{{ stat.label }}</span><strong>{{ stat.value }}</strong><small>{{ stat.note }}</small></article></div>
          <div class="admin-grid"><section class="admin-panel"><div class="panel-head"><div><h2>ბოლო შეკვეთები</h2><p>{{ ordersLoading ? 'იტვირთება...' : 'დღევანდელი აქტივობა' }}</p></div><button @click="adminTab='orders'; loadOrders()">ყველას ნახვა</button></div><div class="order-table"><div class="table-head"><span>შეკვეთა</span><span>მომხმარებელი</span><span>თანხა</span><span>გადახდა</span><span>ბანკის სტატუსი</span><span>სტატუსი</span></div><div v-for="order in adminOrders.slice(0,8)" :key="order.id || order.orderNumber" class="table-row"><strong>{{order.orderNumber}}</strong><span>{{order.customerName}}</span><strong>{{Number(order.total).toLocaleString()}} ₾</strong><span>{{order.paymentLabel}}</span><StatusBadge tone="payment" :label="order.paymentStatusLabel" /><StatusBadge :label="order.status" /></div></div><p v-if="!ordersLoading && !adminOrders.length" class="admin-empty-text">შეკვეთები ჯერ არ არის.</p></section><aside class="admin-panel low-stock"><div class="panel-head"><div><h2>მარაგი იწურება</h2><p>2 ცალზე ნაკლები ან ტოლი</p></div><button @click="adminTab='stock'; stockFilter='all'; stockSearch=''; stockVisibleCount=24">ყველა</button></div><div v-for="item in lowStockPreview" :key="item.id" class="low-stock-row" @click="openStockProduct(item)"><img :src="item.image" :alt="item.name" @error="useFallbackImage"><span><strong>{{item.name}}</strong><small>{{ Number(item.stock || 0) <= 0 ? 'არ არის მარაგში' : `დარჩა ${item.stock} ცალი` }}</small></span><button @click.stop="editProduct(item)">+</button></div><p v-if="!lowStockPreview.length" class="admin-empty-text">მარაგი ამ ზღვარზე არ იწურება.</p></aside></div>
        </template>
        <section v-else-if="adminTab==='products'" class="admin-panel admin-products-panel"><div class="panel-head"><div><h2>პროდუქტების მართვა</h2><p>{{ productsLoading ? 'იტვირთება ლოკალური ბაზიდან...' : productsError || 'დამატება, ფასი, ფოტო და მარაგი' }}</p></div><button @click="loadProducts">განახლება</button></div><button class="admin-add-product" @click="openAddProduct"><Plus :size="20" /> ახალი პროდუქტი</button><div class="admin-product-cards"><article v-for="item in products" :key="item.id"><div class="admin-product-image"><img :src="item.image" :alt="item.name" @error="useFallbackImage"></div><div class="admin-product-card-body"><small>{{ item.category }}<template v-if="item.source"> • {{ item.source }}</template><template v-if="item.isHidden"> • დამალულია</template></small><strong>{{ item.name }}</strong><div class="admin-product-meta"><span>მარაგი: {{ item.stock }} ცალი</span><b>{{ item.price.toLocaleString() }} ₾</b></div></div><div class="admin-product-actions"><button class="admin-edit-button" @click="editProduct(item)"><Pencil :size="14" /> რედაქტირება</button><button class="admin-delete-button" :class="{restore:item.isHidden}" :disabled="productDeleting" @click="toggleProductVisibility(item)">{{ item.isHidden ? 'გამოჩენა' : 'დამალვა' }}</button></div></article></div></section>
        <section v-else-if="adminTab==='orders'" class="admin-panel"><div v-if="!selectedAdminOrder"><div class="panel-head"><div><h2>შეკვეთები</h2><p>{{ ordersLoading ? 'იტვირთება...' : orderError || `${reportOrders.length} შეკვეთა არჩეულ პერიოდში` }}</p></div><button @click="loadOrders">განახლება</button></div><div class="order-report-bar"><div class="order-report-controls"><div class="report-segments"><button v-for="option in reportPeriodOptions" :key="option.value" :class="{active: orderReportPeriod === option.value}" @click="orderReportPeriod = option.value">{{ option.label }}</button></div><div class="report-dates"><label><span>დან</span><input v-model="orderReportPickerFrom" type="date"></label><label><span>მდე</span><input v-model="orderReportPickerTo" type="date"></label></div></div><div class="order-report-grid"><article v-for="item in orderReportStats" :key="item.label"><span>{{ item.label }}</span><strong>{{ item.value }}</strong><small>{{ item.note }}</small></article></div></div><OrderTable :orders="paginatedReportOrders" :labels="orderTableLabels" show-date :format-date="formatOrderDate" :empty-text="!ordersLoading ? 'არჩეულ პერიოდში შეკვეთები არ არის.' : ''" @open="openAdminOrder" /><div v-if="reportOrders.length > adminPageSize" class="admin-pagination"><span>{{ (ordersPage - 1) * adminPageSize + 1 }}-{{ Math.min(ordersPage * adminPageSize, reportOrders.length) }} / {{ reportOrders.length }}</span><div><button :disabled="ordersPage===1" @click="setAdminPage('orders', ordersPage - 1)">წინა</button><button :disabled="ordersPage===ordersPageCount" @click="setAdminPage('orders', ordersPage + 1)">შემდეგი</button></div></div></div><div v-else class="admin-order-detail"><div class="admin-order-detail-head"><button class="back-link" @click="closeAdminOrder"><ArrowLeft :size="18" /> შეკვეთებში დაბრუნება</button><div><small>{{ formatOrderDate(selectedAdminOrder.createdAt) }}</small><h2>{{ selectedAdminOrder.orderNumber }}</h2></div><label><span>სტატუსი</span><select :value="selectedAdminOrder.statusValue" :disabled="orderStatusSaving" @change="updateAdminOrderStatus($event.target.value)"><option v-for="option in orderStatusOptions" :key="option.value" :value="option.value">{{ option.label }}</option></select></label></div><p v-if="orderError" class="admin-form-error">{{ orderError }}</p><div class="admin-order-detail-grid"><article><small>მომხმარებელი</small><strong>{{ selectedAdminOrder.customerName || 'სახელი არ არის' }}</strong><span>{{ selectedAdminOrder.customer?.phone }}</span><span>{{ selectedAdminOrder.customer?.email || 'ელფოსტა არ არის' }}</span></article><article><small>მიწოდება</small><strong>{{ deliveryMethodLabel(selectedAdminOrder.delivery?.method) }}</strong><span>{{ selectedAdminOrder.delivery?.method === 'pickup' ? selectedAdminOrder.delivery?.pickupBranch : `${selectedAdminOrder.delivery?.city}, ${selectedAdminOrder.delivery?.address}` }}</span><span>{{ selectedAdminOrder.delivery?.note || 'შენიშვნა არ არის' }}</span></article><article><small>გადახდის მეთოდი</small><strong>{{ selectedAdminOrder.paymentLabel }}</strong><span>ბანკის სტატუსი: {{ selectedAdminOrder.paymentStatusLabel }}</span></article></div><form v-if="selectedAdminOrder.paymentMethod === 'liberty'" class="admin-refund-form" @submit.prevent="openRefundConfirmation"><div><small>თანხის დაბრუნება</small><strong>Refund</strong><span>Liberty გადახდაზე შეგიძლია სრული ან ნაწილობრივი დაბრუნება.</span></div><label><span>თანხა ₾</span><input v-model="refundForm.amount" type="number" min="0.01" step="0.01"></label><label><span>მიზეზი</span><input v-model="refundForm.reason" placeholder="მაგ: მომხმარებელმა გააუქმა"></label><button type="submit" :disabled="refundSaving">{{ refundSaving ? 'ბრუნდება...' : 'თანხის დაბრუნება' }}</button></form><div class="admin-order-items"><h3>პროდუქტები</h3><div v-for="item in selectedAdminOrder.items" :key="item.id" class="admin-order-item"><img :src="item.productImageUrl || 'https://agro-trade.ge/wp-content/uploads/2022/01/lawnmower.png'" :alt="item.productName"><span><strong>{{ item.productName }}</strong><small>{{ item.productSku || item.productCategory || 'პროდუქტი' }}</small></span><b>{{ item.quantity }} × {{ Number(item.unitPrice).toLocaleString() }} ₾</b><strong>{{ Number(item.lineTotal).toLocaleString() }} ₾</strong></div></div><div class="admin-order-totals"><span><small>პროდუქტები</small><strong>{{ Number(selectedAdminOrder.subtotal).toLocaleString() }} ₾</strong></span><span><small>მიწოდება</small><strong>{{ Number(selectedAdminOrder.deliveryPrice).toLocaleString() }} ₾</strong></span><span><small>სულ</small><strong>{{ Number(selectedAdminOrder.total).toLocaleString() }} ₾</strong></span></div></div></section>
        <section v-else-if="adminTab==='users'" class="admin-panel"><div class="panel-head"><div><h2>მომხმარებლები</h2><p>{{ usersLoading ? 'იტვირთება...' : userError || `${apiUsers.length} დარეგისტრირებული მომხმარებელი` }}</p></div><div class="panel-actions"><button @click="loadUsers">განახლება</button><button v-if="currentUser?.role === 'admin'" @click="openUserForm">+ მომხმარებელი</button></div></div><form v-if="showUserForm" class="admin-user-form" @submit.prevent="createAdminUser"><label><span>სახელი</span><input v-model="userForm.firstName" required></label><label><span>გვარი</span><input v-model="userForm.lastName" required></label><label><span>მობილური</span><input v-model="userForm.phone" required></label><label><span>ელფოსტა</span><input v-model="userForm.email" type="email" required></label><label><span>პაროლი</span><input v-model="userForm.password" type="password" required minlength="6"></label><label><span>როლი</span><select v-model="userForm.role"><option value="user">user</option><option value="manager">manager</option><option value="admin">admin</option></select></label><div><button type="button" @click="closeUserForm">გაუქმება</button><button type="submit" :disabled="userSaving">{{ userSaving ? 'ემატება...' : 'დამატება' }}</button></div></form><p v-if="userError" class="admin-form-error">{{ userError }}</p><div class="admin-user-cards"><article v-for="user in paginatedUsers" :key="user.id" :class="[`role-${user.role}`, {blocked:user.isBlocked}]"><div><strong>{{ user.firstName }} {{ user.lastName }}</strong><small :class="`user-role user-role-${user.role}`">{{ user.isBlocked ? 'blocked' : user.role }}</small></div><span>{{ user.email }}</span><span>{{ user.phone }}</span><label v-if="currentUser?.role === 'admin'" class="admin-user-role-select"><span>როლი</span><select :value="user.role" :disabled="userRoleSaving === user.id || user.isBlocked" @change="updateUserRole(user, $event.target.value)"><option value="user">user</option><option value="manager">manager</option><option value="admin">admin</option></select></label><div v-if="currentUser?.role === 'admin'" class="admin-user-card-actions"><button type="button" :disabled="userActionSaving === user.id || currentUser?.id === user.id" @click="updateUserBlock(user)">{{ user.isBlocked ? 'გახსნა' : 'დაბლოკვა' }}</button><button type="button" :disabled="userActionSaving === user.id || currentUser?.id === user.id" @click="deleteUser(user)">წაშლა</button></div></article></div><div v-if="apiUsers.length > adminPageSize" class="admin-pagination"><span>{{ (usersPage - 1) * adminPageSize + 1 }}-{{ Math.min(usersPage * adminPageSize, apiUsers.length) }} / {{ apiUsers.length }}</span><div><button :disabled="usersPage===1" @click="setAdminPage('users', usersPage - 1)">წინა</button><button :disabled="usersPage===usersPageCount" @click="setAdminPage('users', usersPage + 1)">შემდეგი</button></div></div><p v-if="!usersLoading && !apiUsers.length" class="admin-empty-text">მომხმარებლები ჯერ არ არიან დარეგისტრირებული.</p></section>
        <section v-else-if="adminTab==='logs'" class="admin-panel"><div class="panel-head"><div><h2>მნიშვნელოვანი ლოგები</h2><p>{{ activityLogsLoading ? 'იტვირთება...' : activityLogsError || `${activityLogs.length} ბოლო მოვლენა` }}</p></div><button @click="loadActivityLogs">განახლება</button></div><div class="admin-log-list"><article v-for="log in paginatedLogs" :key="log.id" :class="`severity-${log.severity}`"><div class="admin-log-main"><span>{{ formatOrderDate(log.createdAt) }}</span><strong>{{ log.message }}</strong><small>{{ log.orderNumber || 'შეკვეთის გარეშე' }}<template v-if="log.provider"> • {{ log.provider }}</template><template v-if="log.status"> • {{ log.status }}</template></small></div><em>{{ log.severity }}</em><p v-if="log.details">{{ log.details }}</p></article></div><div v-if="activityLogs.length > adminPageSize" class="admin-pagination"><span>{{ (logsPage - 1) * adminPageSize + 1 }}-{{ Math.min(logsPage * adminPageSize, activityLogs.length) }} / {{ activityLogs.length }}</span><div><button :disabled="logsPage===1" @click="setAdminPage('logs', logsPage - 1)">წინა</button><button :disabled="logsPage===logsPageCount" @click="setAdminPage('logs', logsPage + 1)">შემდეგი</button></div></div><p v-if="!activityLogsLoading && !activityLogs.length" class="admin-empty-text">მნიშვნელოვანი ლოგი ჯერ არ არის.</p></section>
        <section v-else-if="adminTab==='categories'" class="admin-panel"><div class="panel-head"><div><h2>კატეგორიები</h2><p>{{ categoriesError || '2-3 დონიანი კატეგორიების მართვა' }}</p></div></div><form class="admin-category-form" @submit.prevent="saveCategory"><label><span>სახელი</span><input ref="categoryNameInput" v-model="categoryForm.name" required placeholder="მაგ: კუთხსახეხები"></label><label><span>მშობელი კატეგორია</span><select v-model="categoryForm.parentId"><option value="">მთავარი კატეგორია</option><option v-for="cat in flatCategories.filter(item => item.id !== categoryForm.id)" :key="cat.id" :value="cat.id">{{ '— '.repeat(cat.level) }}{{ cat.name }}</option></select></label><label><span>რიგითობა</span><input v-model="categoryForm.sortOrder" type="number" min="0"></label><label class="admin-category-photo-picker"><span>კატეგორიის ფოტო</span><input type="file" accept="image/*" @change="setCategoryImage"><b>ფოტოს არჩევა</b><small v-if="categoryForm.imageFile">{{ categoryForm.imageFile.name }}</small><small v-else-if="categoryForm.imageUrl">ფოტო დამატებულია</small><small v-else>აირჩიე ფოტო კატეგორიის ბარათისთვის</small></label><div v-if="categoryForm.imageUrl" class="admin-category-image-preview"><img :src="categoryForm.imageUrl" alt="კატეგორიის ფოტო"><button type="button" @click="categoryForm.imageUrl=''; categoryForm.imageFile=null">წაშლა</button></div><div class="admin-category-form-actions"><button type="button" @click="resetCategoryForm">გაუქმება</button><button type="submit" :disabled="categorySaving">{{ categoryForm.id ? 'შენახვა' : 'დამატება' }}</button></div></form><div class="admin-category-list"><article v-for="cat in flatCategories" :key="cat.id" :style="{ '--level': cat.level }"><span><strong>{{ cat.name }}</strong><small>{{ cat.parentId ? 'ქვეკატეგორია' : 'მთავარი კატეგორია' }} • {{ cat.productCount }} პროდუქტი</small></span><div class="admin-category-actions"><button @click="editCategory(cat)">რედაქტირება</button><button @click="deleteCategory(cat)">წაშლა</button></div></article><p v-if="!flatCategories.length">კატეგორიები ჯერ არ არის დამატებული.</p></div></section>
        <section v-else-if="adminTab==='brands'" class="admin-panel"><div class="panel-head"><div><h2>ბრენდები</h2><p>{{ brandsError || 'ბრენდების მართვა და carousel-ში გამოჩენა' }}</p></div></div><form class="admin-category-form" @submit.prevent="saveBrand"><label><span>სახელი</span><input v-model="brandForm.name" required placeholder="მაგ: STIHL"></label><label><span>რიგითობა</span><input v-model="brandForm.sortOrder" type="number" min="0"></label><div class="admin-category-form-actions"><button type="button" @click="resetBrandForm">გაუქმება</button><button type="submit" :disabled="brandSaving">{{ brandForm.id ? 'შენახვა' : 'დამატება' }}</button></div></form><div class="admin-category-list"><article v-for="brand in apiBrands" :key="brand.id"><span><strong>{{ brand.name }}</strong><small>{{ brand.productCount }} პროდუქტი</small></span><div class="admin-category-actions"><button @click="editBrand(brand)">რედაქტირება</button><button @click="deleteBrand(brand)">წაშლა</button></div></article><p v-if="!apiBrands.length">ბრენდები ჯერ არ არის დამატებული.</p></div></section>
        <section v-else-if="adminTab==='discounts'" class="admin-panel"><div class="panel-head"><div><h2>ფასდაკლებები</h2><p>აირჩიე ბაზაში დამატებული პროდუქტი და მიუთითე ფასდაკლებული ფასი</p></div><button @click="openDiscountForm()">+ აქცია</button></div><form v-if="showDiscountForm" class="admin-discount-form" @submit.prevent="saveDiscount"><label class="admin-discount-product-picker" @focusout="setTimeout(()=>discountProductPickerOpen=false,120)"><span>პროდუქტი</span><input v-model="discountProductSearch" type="search" placeholder="მოძებნე პროდუქტი..." @focus="discountProductPickerOpen=true" @input="discountProductPickerOpen=true"><input v-model="discountForm.productId" type="hidden"><div v-if="discountProductPickerOpen" class="admin-discount-product-menu"><button v-for="item in discountProductOptions" :key="item.backendId" type="button" :class="{active:String(item.backendId)===String(discountForm.productId)}" @mousedown.prevent="selectDiscountProduct(item)"><img :src="item.image" :alt="item.name" @error="useFallbackImage"><span><strong>{{ item.name }}</strong><small>{{ item.category }} • {{ item.price.toLocaleString() }} ₾</small></span></button><p v-if="!discountProductOptions.length">პროდუქტი ვერ მოიძებნა.</p></div></label><label><span>ძველი ფასი ₾</span><input v-model="discountForm.regularPrice" type="number" min="0.01" step="0.01"></label><label><span>ფასდაკლებული ფასი ₾</span><input v-model="discountForm.salePrice" type="number" min="0.01" step="0.01"></label><label><span>პროცენტი</span><input v-model="discountForm.percent" type="number" min="1" max="99" step="1" @input="applyDiscountPercent"></label><div class="admin-discount-actions"><button type="button" @click="closeDiscountForm">გაუქმება</button><button type="submit" :disabled="discountSaving">{{ discountSaving ? 'ინახება...' : 'ფასდაკლებაში დამატება' }}</button></div></form><p v-if="productsError" class="admin-form-error">{{ productsError }}</p><div class="admin-discount-list"><article v-for="item in discountProducts" :key="item.id"><img :src="item.image" :alt="item.name" @error="useFallbackImage"><span><strong>{{ item.name }}</strong><small>{{ item.oldPrice.toLocaleString() }} ₾ → {{ item.price.toLocaleString() }} ₾</small></span><b>-{{ Math.round((item.oldPrice - item.price) / item.oldPrice * 100) }}%</b><button @click="openDiscountForm(item)">შეცვლა</button><button @click="removeDiscount(item)">მოხსნა</button></article><p v-if="!discountProducts.length">ფასდაკლებული პროდუქტი ჯერ არ არის.</p></div></section>
        <section v-else-if="adminTab==='stock'" class="admin-panel"><div class="panel-head"><div><h2>მარაგები</h2><p>{{ stockProducts.length }} პროდუქტი • ჯერ ამოწურული, მერე იწურება</p></div><button @click="loadProducts">განახლება</button></div><div class="admin-stock-toolbar"><label><Search :size="15" /><input v-model="stockSearch" placeholder="პროდუქტის, SKU-ს ან ბრენდის ძებნა" @input="stockVisibleCount=24"></label><div><button :class="{active:stockFilter==='all'}" @click="stockFilter='all'; stockVisibleCount=24">ყველა</button><button :class="{active:stockFilter==='low'}" @click="stockFilter='low'; stockVisibleCount=24">იწურება</button><button :class="{active:stockFilter==='out'}" @click="stockFilter='out'; stockVisibleCount=24">არ არის</button></div></div><div class="admin-stock-grid"><article v-for="item in displayedStockProducts" :key="item.id" :class="{empty:Number(item.stock || 0)<=0}"><img :src="item.image" :alt="item.name" @error="useFallbackImage"><strong>{{ item.name }}</strong><small>{{ item.category }}<template v-if="item.brand"> • {{ item.brand }}</template></small><div class="stock-total"><span>საერთო მარაგი</span><b>{{ item.stock }} ცალი</b></div><div class="admin-stock-actions"><em>{{ Number(item.stock || 0) <= 0 ? 'არ არის მარაგში' : Number(item.stock || 0) <= 2 ? 'მარაგი იწურება' : 'მარაგშია' }}</em><button @click="editProduct(item)">შევსება</button></div></article></div><div v-if="stockVisibleCount < stockProducts.length" class="load-more admin-stock-more"><p>ნაჩვენებია {{ displayedStockProducts.length }} / {{ stockProducts.length }}</p><button @click="stockVisibleCount += 24">მეტის ჩვენება <Plus :size="17" /></button></div><p v-if="!stockProducts.length" class="admin-empty-text">პროდუქტი ვერ მოიძებნა.</p></section>
        <section v-else class="admin-panel"><div class="panel-head"><div><h2>პარამეტრები</h2><p>მაღაზიის ძირითადი მონაცემები</p></div><button>შენახვა</button></div><div class="admin-settings"><label><span>ტელეფონი</span><input value="+995 598 850 503"></label><label><span>მიწოდების ტექსტი</span><input value="უფასო მიწოდება თბილისში 300₾-დან"></label><label><span>სამუშაო საათები</span><input value="ორშ–შაბ: 09:00–18:00"></label></div></section>
      </section>
    </main>

    <transition name="compare-drawer">
      <aside v-if="compareProducts.length && !adminPage" class="compare-bar">
        <div>
          <span><Scale :size="17" /> შედარება</span>
          <strong>{{ compareProducts.length }} / 2 პროდუქტი</strong>
        </div>
        <div class="compare-mini-list">
          <article v-for="product in compareProducts" :key="product.id">
            <img :src="product.image" :alt="product.name">
            <span>{{ product.name }}</span>
            <button @click="toggleCompare(product)"><X :size="14" /></button>
          </article>
          <article v-if="compareProducts.length < 2" class="compare-placeholder">აირჩიე კიდევ ერთი პროდუქტი</article>
        </div>
        <button class="compare-open" :disabled="compareProducts.length < 2" @click="openCompare">შედარება</button>
        <button class="compare-clear" @click="clearCompare"><X :size="17" /></button>
      </aside>
    </transition>

    <transition name="filter-fade">
      <div v-if="filterOpen" class="filter-overlay" @click.self="filterOpen=false">
        <aside class="filter-panel">
          <div class="filter-head">
            <div><span>ფილტრი</span><strong>პროდუქციის შერჩევა</strong></div>
            <button @click="filterOpen=false"><X /></button>
          </div>

          <section class="filter-group">
            <h3>ფასი</h3>
            <div class="price-filter">
              <label><span>მინ.</span><input v-model="minPrice" type="number" min="0" placeholder="0" @input="visibleCount=12"></label>
              <label><span>მაქს.</span><input v-model="maxPrice" type="number" min="0" placeholder="15000" @input="visibleCount=12"></label>
            </div>
          </section>

          <section class="filter-group">
            <h3>ბრენდი</h3>
            <div class="filter-pills">
              <button v-for="brand in brandFilterOptions" :key="brand" :class="{active:selectedBrand===brand}" @click="selectedBrand=brand; visibleCount=12">{{ brand }}</button>
            </div>
          </section>

          <section class="filter-group">
            <h3>მდგომარეობა</h3>
            <label class="filter-check"><input v-model="onlyInStock" type="checkbox" @change="visibleCount=12"><span></span> მხოლოდ მარაგში</label>
            <label class="filter-check"><input v-model="onlyDiscounted" type="checkbox" @change="visibleCount=12"><span></span> ფასდაკლებული</label>
          </section>

          <section class="filter-group">
            <h3>სორტირება</h3>
            <select v-model="sortBy" @change="visibleCount=12">
              <option value="popular">პოპულარული</option>
              <option value="price-low">იაფიდან ძვირისკენ</option>
              <option value="price-high">ძვირიდან იაფისკენ</option>
              <option value="discount">ფასდაკლებით</option>
              <option value="new">ახალი</option>
            </select>
          </section>

          <div class="filter-actions">
            <button @click="clearFilters">გასუფთავება</button>
            <button @click="filterOpen=false">ჩვენება — {{ filteredProducts.length }}</button>
          </div>
        </aside>
      </div>
    </transition>

    <transition name="lightbox">
      <div v-if="comparePage" class="compare-modal" @click.self="comparePage=false">
        <section class="compare-panel">
          <button class="compare-close" @click="comparePage=false"><X /></button>
          <div class="compare-head">
            <span class="eyebrow"><span></span> პროდუქტის შედარება</span>
            <h2>შეადარე არჩეული პროდუქტები</h2>
            <p>ნახე ფასი, მარაგი და მთავარი მახასიათებლები ერთ ფანჯარაში.</p>
          </div>
          <div class="compare-table">
            <div class="compare-row compare-products">
              <b>პროდუქტი</b>
              <article v-for="product in compareProducts" :key="product.id">
                <img :src="product.image" :alt="product.name">
                <strong>{{ product.name }}</strong>
                <small>{{ product.category }}</small>
              </article>
            </div>
            <div class="compare-row"><b>ფასი</b><span v-for="product in compareProducts" :key="product.id">{{ product.price.toLocaleString() }} ₾</span></div>
            <div class="compare-row"><b>ფასდაკლება</b><span v-for="product in compareProducts" :key="product.id">{{ product.oldPrice ? `${formatMoney(discountAmount(product))} ₾` : 'არ აქვს' }}</span></div>
            <div class="compare-row"><b>მარაგი</b><span v-for="product in compareProducts" :key="product.id">{{ product.stock }} ცალი</span></div>
            <div class="compare-row compare-specs"><b>მახასიათებლები</b><ul v-for="product in compareProducts" :key="product.id"><li v-for="spec in product.specs" :key="spec">{{ spec }}</li></ul></div>
          </div>
          <div class="compare-actions">
            <button v-for="product in compareProducts" :key="product.id" @click="comparePage=false; openProductPage(product)">ნახე {{ product.name }}</button>
          </div>
        </section>
      </div>
    </transition>

    <transition name="modal-fade">
      <div v-if="authOpen" class="admin-product-modal" @click.self="closeAuth">
        <form class="auth-modal" @submit.prevent="submitAuth">
          <div class="admin-product-form-head">
            <div><small>ანგარიში</small><h2>{{ authMode === 'login' ? 'ავტორიზაცია' : 'რეგისტრაცია' }}</h2></div>
            <button type="button" @click="closeAuth"><X :size="18" /></button>
          </div>
          <div class="auth-tabs"><button type="button" :class="{active:authMode==='login'}" @click="authMode='login'; authError=''">შესვლა</button><button type="button" :class="{active:authMode==='register'}" @click="authMode='register'; authError=''">რეგისტრაცია</button></div>
          <div class="auth-form-grid">
            <label v-if="authMode==='register'"><span>სახელი</span><input v-model="authForm.firstName" required></label>
            <label v-if="authMode==='register'"><span>გვარი</span><input v-model="authForm.lastName" required></label>
            <label v-if="authMode==='register'" class="full"><span>მობილურის ნომერი</span><input v-model="authForm.phone" required placeholder="+995 5XX XX XX XX"></label>
            <label class="full"><span>ელფოსტა</span><input v-model="authForm.email" type="email" required></label>
            <label class="full"><span>პაროლი</span><input v-model="authForm.password" type="password" required minlength="6"></label>
            <label v-if="authMode==='register'" class="full"><span>გაიმეორე პაროლი</span><input v-model="authForm.confirmPassword" type="password" required minlength="6"></label>
          </div>
          <p v-if="authError" class="admin-form-error">{{ authError }}</p>
          <div class="admin-product-form-actions"><button type="button" @click="closeAuth">გაუქმება</button><button type="submit" :disabled="authSaving">{{ authSaving ? 'იტვირთება...' : (authMode === 'login' ? 'შესვლა' : 'რეგისტრაცია') }}</button></div>
        </form>
      </div>
    </transition>

    <footer v-if="!adminPage">
      <div class="wrap footer-grid"><div><a class="logo image-logo footer-logo" href="#" @click.prevent="goHome"><img src="/agrotrade_logo.png" alt="Agro Trade"></a><p>ხარისხიანი სასოფლო-სამეურნეო ტექნიკა და პროფესიონალური მომსახურება.</p></div><div><h4>ნავიგაცია</h4><a href="#">საიტის რუკა</a><a href="#" @click.prevent="goHome">მთავარი</a><a href="#" @click.prevent="scrollToSection('service-info')">ჩვენ შესახებ</a><a href="#" @click.prevent="privacyConsentOpen=true; privacyConsentExpanded=true">კონფიდენციალურობის პოლიტიკა</a><a href="#" @click.prevent="returnPolicyOpen=true">დაბრუნების პოლიტიკა</a><a href="#" @click.prevent="deliveryPolicyOpen=true">მიწოდების პოლიტიკა</a><a href="#" @click.prevent="openTermsPolicy">წესები და პირობები</a><a href="#" @click.prevent="installmentPolicyOpen=true">განვადება</a><a href="#" @click.prevent="paymentPolicyOpen=true">გადახდა</a><a href="#" @click.prevent="warrantyPolicyOpen=true">გარანტია</a><a  :href="publicAssetUrl('return-form.pdf')" target="_blank" rel="noopener">დაბრუნების ფორმა</a></div><div class="footer-contact"><h4>კონტაქტი</h4><a href="https://www.facebook.com/AGROTRADEGEO" target="_blank" rel="noopener">Facebook</a><a href="https://www.instagram.com/agrotradegeo/" target="_blank" rel="noopener">Instagram</a><a href="https://www.tiktok.com/@agro.trade3" target="_blank" rel="noopener">TikTok</a><a href="tel:+995598850503">Phone: +995 598 850 503</a><a href="mailto:agrotradegeo@gmail.com">Email: agrotradegeo@gmail.com</a></div><div class="footer-addresses"><h4>მისამართები</h4><a href="https://maps.app.goo.gl/PvjEHT4oDRApxGAeA" target="_blank" rel="noopener">თბილისი, წერეთლის გამზ. N147</a><a href="https://maps.app.goo.gl/3NDu29YprotRacKc9" target="_blank" rel="noopener">ოკამი, თბილისი-სენაკი-ლესელიძის მე-40 კილომეტრი</a><a href="https://maps.app.goo.gl/BevpdezxUeabjfyn9" target="_blank" rel="noopener">ზესტაფონი, რუსთაველის ქ. N60</a><span>ორშ–შაბ: 09:00–18:00</span></div></div><div class="wrap copyright">© 2026 Agro Trade. ყველა უფლება დაცულია.</div>
    </footer>

    <transition name="modal-fade">
      <div v-if="showAddProduct" class="admin-product-modal" @click.self="closeAddProduct">
        <form class="admin-product-form" @submit.prevent="saveProduct">
          <div class="admin-product-form-head">
            <div>
              <small>ლოკალური ბაზა</small>
              <h2>{{ productForm.id ? 'პროდუქტის რედაქტირება' : 'ახალი პროდუქტის დამატება' }}</h2>
            </div>
            <button type="button" @click="closeAddProduct"><X :size="18" /></button>
          </div>
          <div class="admin-product-form-grid">
            <label class="full"><span>პროდუქტის სახელი *</span><input v-model="productForm.name" required placeholder="მაგ: მოტობლოკი AGRI 177F"></label>
            <label><span>SKU / კოდი</span><input v-model="productForm.sku" placeholder="AG-177F"></label>
            <label><span>ფასი ₾</span><input v-model="productForm.price" type="number" min="0" step="0.01" placeholder="1700"></label><label><span>ფასდაკლებული ფასი ₾</span><input v-model="productForm.salePrice" type="number" min="0" step="0.01"></label>
            <label><span>მარაგი</span><input v-model="productForm.stockQuantity" type="number" min="0" step="1" placeholder=""></label>
            <label><span>ბრენდი</span><select v-model="productForm.brandId"><option value="">აირჩიე ბრენდი</option><option v-for="brand in apiBrands" :key="brand.id" :value="brand.id">{{ brand.name }}</option></select></label>
            <label v-if="flatCategories.length"><span>კატეგორია</span><select v-model="productForm.categoryId" @change="selectProductCategory"><option value="">აირჩიე კატეგორია</option><option v-for="cat in flatCategories" :key="cat.id" :value="cat.id">{{ '— '.repeat(cat.level) }}{{ cat.name }}</option></select></label>
            <label v-else><span>კატეგორია</span><input v-model="productForm.categoryNames" placeholder="მოტობლოკები"></label>
            <label class="full"><span>ფოტოების ატვირთვა (მაქს. 3)</span><input type="file" accept="image/*" multiple :disabled="productForm.imagePreviews.length >= 3" @change="setProductImage"></label>
            <div class="admin-product-photo-list full">
              <article v-for="(photo, index) in productForm.imagePreviews" :key="photo.url">
                <img :src="photo.url" alt="პროდუქტის ფოტო" @error="useFallbackImage">
                <button type="button" @click="removeProductImage(index)">წაშლა</button>
              </article>
              <p v-if="!productForm.imagePreviews.length">ფოტო ჯერ არ არის დამატებული.</p>
            </div>
            <label class="full"><span>მოკლე აღწერა</span><textarea v-model="productForm.shortDescription" placeholder="მოკლე აღწერა, რომელიც პროდუქტის გვერდზე გამოჩნდება"></textarea></label>
          </div>
          <div class="admin-spec-builder">
            <div class="admin-spec-head">
              <div>
                <h3>მახასიათებლები</h3>
                <p>დაამატე მხოლოდ ის ველები, რაც ამ პროდუქტს სჭირდება.</p>
              </div>
              <button type="button" @click="addSpecification">+ ველი</button>
            </div>
            <div class="admin-spec-presets">
              <button v-for="(_, name) in specificationPresets" :key="name" type="button" @click="applySpecificationPreset(name)">{{ name }}</button>
            </div>
            <div class="admin-spec-row" v-for="(spec, index) in productForm.specifications" :key="index">
              <input v-model="spec.key" placeholder="მაგ: სიმძლავრე">
              <input v-model="spec.value" placeholder="მაგ: 9 ცხ.ძ. / 3600">
              <button type="button" @click="removeSpecification(index)"><X :size="15" /></button>
            </div>
          </div>
          <p v-if="productsError" class="admin-form-error">{{ productsError }}</p>
          <div class="admin-product-form-actions">
            <button type="button" @click="closeAddProduct">გაუქმება</button>
            <button type="submit" :disabled="productSaving">{{ productSaving ? 'ინახება...' : (productForm.id ? 'შენახვა' : 'პროდუქტის დამატება') }}</button>
          </div>
        </form>
      </div>
    </transition>
    <transition name="modal-fade">
      <div v-if="categoryDeleteTarget" class="admin-product-modal" @click.self="cancelCategoryDelete">
        <section class="admin-confirm-modal">
          <div class="admin-product-form-head">
            <div>
              <small>დადასტურება</small>
              <h2>წავშალოთ კატეგორია?</h2>
              <p>{{ categoryDeleteTarget.name }} წაშლის შემდეგ სიიდან გაქრება.</p>
            </div>
            <button type="button" @click="cancelCategoryDelete"><X :size="18" /></button>
          </div>
          <div class="admin-product-form-actions">
            <button type="button" @click="cancelCategoryDelete">გაუქმება</button>
            <button type="button" :disabled="categoryDeleting" @click="confirmCategoryDelete">{{ categoryDeleting ? 'იშლება...' : 'წაშლა' }}</button>
          </div>
        </section>
      </div>
    </transition>
    <transition name="modal-fade">
      <div v-if="brandDeleteTarget" class="admin-product-modal" @click.self="cancelBrandDelete">
        <section class="admin-confirm-modal">
          <div class="admin-product-form-head">
            <div>
              <small>დადასტურება</small>
              <h2>წავშალოთ ბრენდი?</h2>
              <p>{{ brandDeleteTarget.name }} წაშლის შემდეგ carousel-იდანაც გაქრება.</p>
            </div>
            <button type="button" @click="cancelBrandDelete"><X :size="18" /></button>
          </div>
          <div class="admin-product-form-actions">
            <button type="button" @click="cancelBrandDelete">გაუქმება</button>
            <button type="button" :disabled="brandDeleting" @click="confirmBrandDelete">{{ brandDeleting ? 'იშლება...' : 'წაშლა' }}</button>
          </div>
        </section>
      </div>
    </transition>
    <ConfirmModal :open="refundConfirmOpen" title="დავაბრუნოთ თანხა?" :description="`${selectedAdminOrder?.orderNumber} შეკვეთაზე დაბრუნდება ${Number(refundForm.amount).toLocaleString()} ₾.`" cancel-text="არა" :confirm-text="refundSaving ? 'ბრუნდება...' : 'კი, დაბრუნება'" :loading="refundSaving" @cancel="closeRefundConfirmation" @confirm="refundAdminOrder" />
    <PolicyModals
      :terms-policy-open="termsPolicyOpen"
      :terms-policy-loading="termsPolicyLoading"
      :terms-policy-text="termsPolicyText"
      :installment-policy-open="installmentPolicyOpen"
      :payment-policy-open="paymentPolicyOpen"
      :delivery-policy-open="deliveryPolicyOpen"
      :warranty-policy-open="warrantyPolicyOpen"
      :return-policy-open="returnPolicyOpen"
      :privacy-consent-open="privacyConsentOpen"
      :privacy-consent-expanded="privacyConsentExpanded"
      :return-form-url="publicAssetUrl('return-form.pdf')"
      @close="closePolicyModal"
      @toggle-privacy-expanded="privacyConsentExpanded=!privacyConsentExpanded"
      @accept-privacy="acceptPrivacyConsent"
    />
    <transition name="toast"><div v-if="adminNotice" class="admin-notice"><span><Check :size="17" /></span>{{ adminNotice }}</div></transition>
    <transition name="toast"><div v-if="addedToast" class="toast"><span><Check /></span> პროდუქტი დაემატა კალათაში</div></transition>
    <transition name="lightbox"><div v-if="zoomOpen && selectedProduct" class="image-lightbox" @click.self="zoomOpen=false"><button class="lightbox-close" @click="zoomOpen=false"><X /></button><div class="lightbox-content"><img :src="selectedProductImage" :alt="selectedProduct.name" @error="useFallbackImage"><div><span>{{ selectedProduct.category }}</span><strong>{{ selectedProduct.name }}</strong><small>დააჭირე ფონს დასახურად</small></div></div></div></transition>
    <div v-if="mobileMenu" class="mobile-drawer"><div class="drawer-head"><span>მენიუ</span><button @click="mobileMenu=false"><X /></button></div><button class="mobile-catalog-toggle" @click="catalogOpen=!catalogOpen"><span>კატეგორიები</span><ChevronDown :class="{rotated:catalogOpen}" /></button><div v-if="catalogOpen" class="mobile-category-list"><button v-for="cat in categoryFilterItems" :key="cat.name" :style="{ paddingLeft: `${8 + cat.level * 12}px` }" @click="chooseCategory(cat.name)">{{ cat.name }}</button></div><a href="#" @click.prevent="openProductsPage('ყველა')">პროდუქცია</a><a href="#" @click.prevent="openOffersPage">შეთავაზებები</a><a href="#" @click.prevent="scrollToSection('service-info')">ჩვენს შესახებ</a><a href="#contact" @click="mobileMenu=false">კონტაქტი</a></div>
    <div v-if="mobileMenu" class="overlay" @click="mobileMenu=false"></div>
  </div>
</template>


























