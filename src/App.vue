<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import {
  ArrowLeft, ArrowRight, Check, ChevronDown, ChevronRight, CircleUserRound, CreditCard,
  Clock3, Heart, Leaf, MapPin, Menu, Minus, PackageCheck, Phone, Plus,
  Scale, Search, ShieldCheck, ShoppingBag, SlidersHorizontal, Star, Trash2, Truck, X, Zap
} from 'lucide-vue-next'

const activeCategory = ref('ყველა')
const visibleCount = ref(12)
const search = ref('')
const searchFocused = ref(false)
const cartCount = ref(2)
const wishlist = ref(new Set([2]))
const selectedProduct = ref(null)
const detailQty = ref(1)
const detailTab = ref('description')
const productFaqOpen = ref(0)
const zoomOpen = ref(false)
const cartPage = ref(false)
const checkoutStep = ref(0)
const adminPage = ref(false)
const adminTab = ref('overview')
const orderPlaced = ref(false)
const paymentMethod = ref('bog-installment')
const deliveryMethod = ref('courier')
const mobileMenu = ref(false)
const catalogOpen = ref(false)
const addedToast = ref(false)
const compareIds = ref([])
const comparePage = ref(false)
const productsPage = ref(false)
const filterOpen = ref(false)
const minPrice = ref('')
const maxPrice = ref('')
const selectedBrand = ref('ყველა')
const onlyInStock = ref(false)
const onlyDiscounted = ref(false)
const sortBy = ref('popular')
const applyingHistoryState = ref(false)

const categories = [
  { name: 'მოტობლოკები', image: 'https://agro-trade.ge/wp-content/uploads/2026/04/BUFFALO-177F-1700.jpeg', count: 24 },
  { name: 'გენერატორები', image: 'https://agro-trade.ge/wp-content/uploads/2026/04/AGRI-177C-M-9-1600-₾.jpeg', count: 18 },
  { name: 'ბენზოხერხები', image: 'https://agro-trade.ge/wp-content/uploads/2022/01/chain-saw.png', count: 31 },
  { name: 'ტუმბოები', image: 'https://agro-trade.ge/wp-content/uploads/2022/01/pump.png', count: 16 },
]

const products = [
  { id: 1, name: 'მოტობლოკი BUFFALO 177F', category: 'მოტობლოკები', price: 1700, oldPrice: 1890, rating: 4.9, reviews: 18, badge: '-10%', stock: 7, image: 'https://agro-trade.ge/wp-content/uploads/2026/04/BUFFALO-177F-1700.jpeg', specs: ['ძრავი: 9 ცხ.ძ.', 'საწვავი: ბენზინი', 'სტარტერი და განათება'] },
  { id: 2, name: 'ამური 170GS-L', category: 'მოტობლოკები', price: 990, oldPrice: null, rating: 4.8, reviews: 12, badge: 'პოპულარული', stock: 11, image: 'https://agro-trade.ge/wp-content/uploads/2026/09/7-cx.Z.-amuri_990.jpg', specs: ['ძრავი: 7 ცხ.ძ.', 'სიჩქარე: 2+1', 'კომპაქტური კორპუსი'] },
  { id: 3, name: 'ბენზინის გენერატორი 3.5 kW', category: 'გენერატორები', price: 1290, oldPrice: 1450, rating: 4.7, reviews: 9, badge: 'ახალი', stock: 4, image: 'https://agro-trade.ge/wp-content/uploads/2026/04/AGRI-177C-M-9-1600-₾.jpeg', specs: ['სიმძლავრე: 3.5 kW', 'ძაბვა: 220V', 'ავტომატური დამცავი'] },
  { id: 4, name: 'პროფესიონალური ბენზოხერხი 58cc', category: 'ბენზოხერხები', price: 349, oldPrice: 419, rating: 4.9, reviews: 27, badge: '-17%', stock: 15, image: 'https://agro-trade.ge/wp-content/uploads/2022/01/chain-saw.png', specs: ['ძრავი: 58cc', 'შინა: 50 სმ', 'ანტივიბრაციული სისტემა'] },
  { id: 5, name: 'წყლის ტუმბო 3\" WP-30', category: 'ტუმბოები', price: 459, oldPrice: 520, rating: 4.8, reviews: 16, badge: '-12%', stock: 9, image: 'https://agro-trade.ge/wp-content/uploads/2022/01/pump.png', specs: ['დიამეტრი: 3 ინჩი', 'წარმადობა: 60 მ³/სთ', 'აწევის სიმაღლე: 28 მ'] },
  { id: 6, name: 'გაზონის სათიბი თვითმავალი', category: 'ბაღის ტექნიკა', price: 780, oldPrice: null, rating: 4.6, reviews: 8, badge: 'ახალი', stock: 6, image: 'https://agro-trade.ge/wp-content/uploads/2022/01/lawnmower.png', specs: ['სიგანე: 51 სმ', 'თვითმავალი', 'ბალახის შემგროვებელი'] },
  { id: 7, name: 'შესასხურებელი აპარატი Pandora', category: 'ინსტრუმენტები', price: 629, oldPrice: 699, rating: 4.9, reviews: 21, badge: 'TOP', stock: 12, image: 'https://agro-trade.ge/wp-content/uploads/2022/02/PandoraSprayer-1-1.jpg', specs: ['წნევა: 180 bar', 'სიმძლავრე: 2400 W', 'შლანგი: 8 მ'] },
  { id: 8, name: 'საბურავი 6.00-12', category: 'ინსტრუმენტები', price: 389, oldPrice: 440, rating: 4.7, reviews: 14, badge: '-11%', stock: 19, image: 'https://agro-trade.ge/wp-content/uploads/2022/03/6.00-121231.jpg', specs: ['ზომა: 6.00-12', 'გამძლე პროტექტორი', 'სასოფლო ტექნიკისთვის'] },
]
const cartItems = ref([
  { ...products[0], qty: 1 },
  { ...products[3], qty: 1 }
])

const tractorImages = [
  'https://agro-trade.ge/wp-content/uploads/2026/04/HIROMIKI-195F.jpg',
  'https://agro-trade.ge/wp-content/uploads/2026/04/HIROMIKI-192F-1.jpg',
  'https://agro-trade.ge/wp-content/uploads/2026/04/BUFFALO-170FBL.jpeg'
]
const tractors = Array.from({ length: 50 }, (_, index) => ({
  id: 100 + index,
  name: `${['მინი ტრაქტორი','დიზელის ტრაქტორი','ბაღის ტრაქტორი','უნივერსალური ტრაქტორი'][index % 4]} ${24 + (index % 8) * 5} HP`,
  category: 'ტრაქტორები', price: 8900 + (index % 10) * 750,
  oldPrice: index % 3 === 0 ? 10500 + (index % 10) * 750 : null,
  rating: [4.7, 4.8, 4.9][index % 3], reviews: 5 + (index % 24),
  badge: index % 5 === 0 ? 'ახალი' : index % 3 === 0 ? '-10%' : 'მარაგშია',
  stock: 2 + (index % 8), image: tractorImages[index % tractorImages.length],
  specs: [`ძრავი: ${24 + (index % 8) * 5} ცხ.ძ.`, 'საწვავი: დიზელი', 'გარანტია: 2 წელი']
}))

const allProducts = computed(() => [...products, ...tractors])

const brands = ['STIHL', 'BUFFALO', 'HONDA', 'KÄRCHER', 'TOTAL', 'INGCO']
const openFaq = ref(0)

function getProductBrand(product) {
  return brands.find(brand => product.name.toUpperCase().includes(brand)) || 'სხვა'
}

const filteredProducts = computed(() => allProducts.value.filter(p => {
  const categoryMatch = activeCategory.value === 'ყველა' || p.category === activeCategory.value
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
  if (sortBy.value === 'rating') return b.rating - a.rating
  if (sortBy.value === 'discount') return (b.oldPrice ? b.oldPrice - b.price : 0) - (a.oldPrice ? a.oldPrice - a.price : 0)
  if (sortBy.value === 'new') return b.id - a.id
  return b.reviews - a.reviews
}))
const displayedProducts = computed(() => filteredProducts.value.slice(0, visibleCount.value))
const searchResults = computed(() => search.value.trim().length < 2 ? [] : allProducts.value.filter(p => p.name.toLowerCase().includes(search.value.trim().toLowerCase()) || p.category.toLowerCase().includes(search.value.trim().toLowerCase())).slice(0, 6))
const cartSubtotal = computed(() => cartItems.value.reduce((sum, item) => sum + item.price * item.qty, 0))
const compareProducts = computed(() => compareIds.value.map(id => allProducts.value.find(product => product.id === id)).filter(Boolean))
const bestSellerProducts = computed(() => [...allProducts.value].sort((a, b) => b.reviews - a.reviews).slice(0, 4))
const relatedProducts = computed(() => {
  if (!selectedProduct.value) return []
  const sameCategory = allProducts.value.filter(product => product.category === selectedProduct.value.category && product.id !== selectedProduct.value.id)
  const fallback = [...allProducts.value]
    .filter(product => product.id !== selectedProduct.value.id && !sameCategory.some(item => item.id === product.id))
    .sort((a, b) => b.reviews - a.reviews)
  return [...sameCategory, ...fallback].slice(0, 4)
})
const cartRecommendations = computed(() => allProducts.value.filter(product => !cartItems.value.some(item => item.id === product.id)).slice(0, 3))
const monthlyPayment = computed(() => selectedProduct.value ? Math.ceil(selectedProduct.value.price / 24) : 0)

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
  detailTab.value = 'description'
  productFaqOpen.value = 0
}

function currentPageState() {
  if (selectedProduct.value) return { page: 'product', productId: selectedProduct.value.id }
  if (checkoutStep.value) return { page: 'checkout', step: checkoutStep.value, orderPlaced: orderPlaced.value }
  if (cartPage.value) return { page: 'cart' }
  if (productsPage.value) return { page: 'products', category: activeCategory.value }
  if (adminPage.value) return { page: 'admin' }
  return { page: 'home' }
}

function rememberPage(replace = false) {
  if (applyingHistoryState.value) return
  const state = currentPageState()
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
  comparePage.value = false
  orderPlaced.value = false

  if (state.page === 'product') {
    selectedProduct.value = allProducts.value.find(product => product.id === state.productId) || null
    detailQty.value = 1
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
  }

  window.scrollTo({ top: 0, behavior: 'smooth' })
  window.setTimeout(() => applyingHistoryState.value = false, 0)
}

function handleBrowserBack(event) {
  applyPageState(event.state || { page: 'home' })
}

onMounted(() => {
  rememberPage(true)
  window.addEventListener('popstate', handleBrowserBack)
})

onBeforeUnmount(() => {
  window.removeEventListener('popstate', handleBrowserBack)
})

function openProductPage(product) {
  showProduct(product)
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
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
  comparePage.value = false
  searchFocused.value = false
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function submitSearch() {
  if (!search.value.trim()) return
  selectedProduct.value = null
  productsPage.value = true
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
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

  const existingItem = cartItems.value.find(item => item.id === product.id)
  if (existingItem) {
    existingItem.qty += quantity
  } else {
    cartItems.value = [...cartItems.value, { ...product, qty: quantity }]
  }

  addedToast.value = true
  window.setTimeout(() => addedToast.value = false, 2200)
}

function removeFromCart(id) {
  cartItems.value = cartItems.value.filter(item => item.id !== id)
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
  cartPage.value = true
  checkoutStep.value = 0
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function startCheckout() {
  cartPage.value = false
  checkoutStep.value = 2
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function nextToPayment() {
  checkoutStep.value = 3
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function openAdmin() {
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  comparePage.value = false
  adminPage.value = true
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function goHome() {
  selectedProduct.value = null
  productsPage.value = false
  cartPage.value = false
  checkoutStep.value = 0
  adminPage.value = false
  comparePage.value = false
  orderPlaced.value = false
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
  comparePage.value = false
  mobileMenu.value = false
  catalogOpen.value = false
  rememberPage()
  window.setTimeout(() => document.getElementById(id)?.scrollIntoView({ behavior: 'smooth', block: 'start' }), 0)
}

function chooseCategory(category) {
  openProductsPage(category)
}

function backToProducts() {
  selectedProduct.value = null
  productsPage.value = true
  checkoutStep.value = 0
  cartPage.value = false
  adminPage.value = false
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

function placeOrder() {
  orderPlaced.value = true
  rememberPage()
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

</script>

<template>
  <div class="site-shell">
    <div class="announcement"><span><Zap :size="15" /> უფასო მიწოდება თბილისში 300₾-დან</span><span class="announcement-right">საგარანტიო მომსახურება • ოფიციალური პროდუქცია</span></div>
    <header>
      <div class="header-main wrap">
        <button class="icon-btn mobile-only" @click="mobileMenu = true"><Menu /></button>
        <a class="logo" href="#" @click.prevent="goHome"><span class="logo-mark"><Leaf /></span><span>AGRO<span>TRADE</span><small>ტექნიკა, რომელიც მუშაობს</small></span></a>
        <div class="search-box" @focusin="searchFocused=true" @focusout="setTimeout(()=>searchFocused=false,180)"><Search :size="20" /><input v-model="search" @keydown.enter="submitSearch" placeholder="მოძებნე პროდუქტი, ბრენდი ან კატეგორია..." /><button v-if="search" class="search-clear" @click="search=''">×</button><kbd>Enter</kbd><div v-if="searchFocused && search.length >= 2" class="search-dropdown"><div class="search-label"><span>ძიების შედეგები</span><b>{{ allProducts.filter(p => p.name.toLowerCase().includes(search.toLowerCase()) || p.category.toLowerCase().includes(search.toLowerCase())).length }} პროდუქტი</b></div><button v-for="product in searchResults" :key="product.id" @click="openSearchProduct(product)"><img :src="product.image"><span><small>{{product.category}}</small><strong>{{product.name}}</strong></span><b>{{product.price.toLocaleString()}} ₾</b><ChevronRight :size="16" /></button><div v-if="!searchResults.length" class="search-empty"><Search :size="22" /><span>„{{search}}“ ვერ მოიძებნა</span></div><button v-else class="all-results" @click="submitSearch">ყველა შედეგის ნახვა <ArrowRight :size="16" /></button></div></div>
        <div class="header-actions">
          <a class="phone" href="tel:+995598850503"><Phone :size="19" /><span><small>დაგვიკავშირდი</small>+995 598 850 503</span></a>
          <button class="icon-btn" title="ადმინ პანელი" @click="openAdmin"><CircleUserRound /></button>
          <button class="icon-btn cart-button" @click="openCart"><ShoppingBag /><b>{{ cartItems.reduce((sum,item)=>sum+item.qty,0) }}</b></button>
        </div>
      </div>
      <nav class="wrap desktop-nav">
        <button class="catalog-btn" :class="{open:catalogOpen}" @click="catalogOpen=!catalogOpen"><Menu :size="18" /> ყველა კატეგორია <ChevronDown :size="16" /></button>
        <a href="#" @click.prevent="openProductsPage('ყველა')">პროდუქცია</a><a href="#categories">კატეგორიები</a><a href="#" @click.prevent="openOffersPage">სპეც. შეთავაზებები</a><a href="#" @click.prevent="scrollToSection('service-info')">სერვისი</a><a href="#contact">კონტაქტი</a>
        <span class="nav-location"><MapPin :size="16" /> თბილისი</span>
      </nav>
      <transition name="catalog-fade"><div v-if="catalogOpen" class="catalog-menu"><div class="wrap catalog-grid"><button @click="chooseCategory('ყველა')"><span class="catalog-icon">✦</span><span><strong>ყველა პროდუქტი</strong><small>სრული კატალოგი</small></span><ChevronRight :size="16" /></button><button v-for="item in [{name:'ტრაქტორები',icon:'◈',sub:'50 პროდუქტი'},{name:'მოტობლოკები',icon:'◉',sub:'24 პროდუქტი'},{name:'გენერატორები',icon:'⚡',sub:'18 პროდუქტი'},{name:'ბენზოხერხები',icon:'⌁',sub:'31 პროდუქტი'},{name:'ტუმბოები',icon:'◌',sub:'16 პროდუქტი'},{name:'ბაღის ტექნიკა',icon:'♧',sub:'27 პროდუქტი'},{name:'ინსტრუმენტები',icon:'✣',sub:'42 პროდუქტი'}]" :key="item.name" @click="chooseCategory(item.name)"><span class="catalog-icon">{{ item.icon }}</span><span><strong>{{ item.name }}</strong><small>{{ item.sub }}</small></span><ChevronRight :size="16" /></button></div></div></transition>
    </header>

    <main v-if="!selectedProduct && !productsPage && !cartPage && !checkoutStep && !adminPage">
      <section class="hero wrap">
        <div class="hero-content">
          <span class="eyebrow"><span></span> გაზაფხულის კოლექცია 2026</span>
          <h1>ძლიერი ტექნიკა.<br><em>მარტივი არჩევანი.</em></h1>
          <p>ყველაფერი შენი მეურნეობისთვის — სანდო ბრენდები, პროფესიონალური კონსულტაცია და სწრაფი მიწოდება.</p>
          <div class="hero-actions"><a class="primary-btn" href="#" @click.prevent="openProductsPage('ყველა')">პროდუქციის ნახვა <ArrowRight :size="19" /></a><button class="play-link"><span>▶</span> როგორ შევარჩიოთ?</button></div>
          <div class="hero-stats"><div><strong>15+</strong><span>წელი ბაზარზე</span></div><div><strong>5,000+</strong><span>კმაყოფილი მომხმარებელი</span></div><div><strong>200+</strong><span>პროდუქტი</span></div></div>
        </div>
        <div class="hero-visual">
          <div class="hero-blob"></div>
          <img src="https://agro-trade.ge/wp-content/uploads/2026/04/BUFFALO-177F-1700.jpeg" alt="მოტობლოკი" />
          <div class="floating-card price-card"><small>კვირის შეთავაზება</small><strong>1,700 ₾</strong><span>1,890 ₾</span></div>
          <div class="floating-card rating-card"><span class="stars">★★★★★</span><strong>4.9</strong><small>მომხმარებლების შეფასება</small></div>
        </div>
      </section>

      <section class="benefits wrap" id="services">
        <div><Truck /><span><strong>სწრაფი მიწოდება</strong><small>მთელი საქართველოს მასშტაბით</small></span></div>
        <div><ShieldCheck /><span><strong>ოფიციალური გარანტია</strong><small>ყველა ტექნიკაზე</small></span></div>
        <div><PackageCheck /><span><strong>ნაწილები და სერვისი</strong><small>ერთ სივრცეში</small></span></div>
        <div><Phone /><span><strong>პროფესიონალური რჩევა</strong><small>დაგეხმარებით არჩევაში</small></span></div>
      </section>

      <section class="brand-strip wrap" aria-label="ბრენდები"><span>ბრენდები, რომლებსაც ვენდობით</span><div class="brand-marquee"><div><b v-for="(brand,index) in [...brands, ...brands]" :key="brand + index">{{ brand }}</b></div></div></section>

      <section class="section wrap" id="categories">
        <div class="section-head"><div><span class="eyebrow">პოპულარული არჩევანი</span><h2>იპოვე კატეგორიის მიხედვით</h2></div><a href="#" @click.prevent="openProductsPage('ყველა')">ყველა კატეგორია <ArrowRight :size="18" /></a></div>
        <div class="category-grid">
          <article v-for="category in categories" :key="category.name" @click="openProductsPage(category.name)">
            <img :src="category.image" :alt="category.name" />
            <div><small>{{ category.count }} პროდუქტი</small><h3>{{ category.name }}</h3><span>ნახვა <ArrowRight :size="16" /></span></div>
          </article>
        </div>
      </section>

      <section class="home-products section wrap">
        <div class="section-head"><div><span class="eyebrow">ხშირად ყიდულობენ</span><h2>ყველაზე მოთხოვნადი ტექნიკა</h2><small class="result-count">სწრაფი არჩევანი სეზონური სამუშაოებისთვის</small></div><a href="#" @click.prevent="openProductsPage('ყველა')">სრული კატალოგი <ArrowRight :size="18" /></a></div>
        <div class="home-product-grid">
          <article v-for="product in bestSellerProducts" :key="product.id" class="home-product-card">
            <div class="home-product-media" @click="openProductPage(product)"><span class="badge">{{ product.badge }}</span><img :src="product.image" :alt="product.name"></div>
            <div class="home-product-info">
              <small>{{ product.category }}</small>
              <h3 @click="openProductPage(product)">{{ product.name }}</h3>
              <div class="rating"><Star :size="15" fill="currentColor" /> {{ product.rating }} <span>({{ product.reviews }})</span></div>
              <div class="home-product-bottom"><strong>{{ product.price.toLocaleString() }} ₾</strong><button @click="addToCart(product)"><ShoppingBag :size="18" /> დამატება</button></div>
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
          <div class="tabs"><button v-for="cat in ['ყველა','მოტობლოკები','გენერატორები','ბენზოხერხები']" :class="{active: activeCategory === cat}" @click="activeCategory = cat">{{ cat }}</button></div>
          <div v-if="filteredProducts.length" class="product-grid">
            <article v-for="product in displayedProducts" :key="product.id" class="product-card">
              <div class="product-media" @click="openProductPage(product)"><span class="badge">{{ product.badge }}</span><button class="wish" :class="{active: wishlist.has(product.id)}" @click.stop="toggleWishlist(product.id)"><Heart :size="19" /></button><img :src="product.image" :alt="product.name" /></div>
              <div class="product-info"><small>{{ product.category }}</small><h3 @click="openProductPage(product)">{{ product.name }}</h3><div class="rating"><Star :size="15" fill="currentColor" /> {{ product.rating }} <span>({{ product.reviews }})</span></div><div class="card-trust"><span>განვადება</span><span>გარანტია</span></div><div class="price-row"><div><strong>{{ product.price.toLocaleString() }} ₾</strong><del v-if="product.oldPrice">{{ product.oldPrice.toLocaleString() }} ₾</del></div><button @click="addToCart(product)"><ShoppingBag :size="19" /></button></div><button class="compare-toggle" :class="{active: compareIds.includes(product.id)}" @click="toggleCompare(product)"><Scale :size="16" /> {{ compareIds.includes(product.id) ? 'შედარებიდან მოხსნა' : 'შედარება' }}</button><p class="stock"><Check :size="14" /> მარაგშია</p></div>
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

      <section class="story-section" id="service-info"><div class="wrap story-grid"><div class="story-photo"><img src="https://images.unsplash.com/photo-1500937386664-56d1dfef3854?auto=format&fit=crop&w=1000&q=85" alt="ქართული მეურნეობა"><span><strong>15+</strong> წელი თქვენთან ერთად</span></div><div class="story-copy"><span class="eyebrow">ჩვენი გამოცდილება</span><h2>ვყიდით არა უბრალოდ ტექნიკას — ვპოულობთ სწორ გადაწყვეტას</h2><p>ჩვენი გუნდი დაგეხმარება სიმძლავრის, დანიშნულებისა და ბიუჯეტის მიხედვით საუკეთესო მოდელის შერჩევაში. შენაძენის შემდეგ კი სერვისი და სათადარიგო ნაწილებიც ადგილზე დაგხვდება.</p><div class="quote"><div class="quote-stars">★★★★★</div><blockquote>„კონსულტანტმა ზუსტად ის მოტობლოკი შემირჩია, რაც ჩემს ნაკვეთს სჭირდებოდა. მიწოდებაც მეორე დღესვე მივიღე.“</blockquote><strong>გიორგი მ. <span>• კახეთი</span></strong></div></div></div></section>

      <section class="faq-section wrap"><div><span class="eyebrow">ხშირი კითხვები</span><h2>ყველაფერი, რაც შეძენამდე უნდა იცოდე</h2><p>ვერ იპოვე პასუხი? დაგვირეკე და ჩვენი კონსულტანტი დაგეხმარება.</p><a href="tel:+995598850503"><Phone :size="17" /> +995 598 850 503</a></div><div class="faq-list"><article v-for="(faq, index) in [['რამდენ ხანში ხდება მიწოდება?', 'თბილისში შეკვეთა ბარდება 1–2 სამუშაო დღეში, რეგიონებში — 2–4 სამუშაო დღეში.'],['აქვს თუ არა ტექნიკას გარანტია?', 'დიახ, ყველა ტექნიკას ახლავს ოფიციალური გარანტია. ვადა დამოკიდებულია კონკრეტულ ბრენდსა და მოდელზე.'],['შესაძლებელია ადგილზე კონსულტაცია?', 'რა თქმა უნდა. ჩვენს შოურუმში სპეციალისტი ტექნიკას ადგილზე გაჩვენებთ და შერჩევაში დაგეხმარებათ.'],['გაქვთ სათადარიგო ნაწილები და სერვისი?', 'დიახ, გვაქვს როგორც საგარანტიო სერვისი, ისე ყველაზე მოთხოვნადი სათადარიგო ნაწილები.']]" :key="faq[0]" :class="{open:openFaq===index}"><button @click="openFaq = openFaq === index ? -1 : index"><span>{{ faq[0] }}</span><Plus :size="20" /></button><p>{{ faq[1] }}</p></article></div></section>

      <section class="contact-section wrap" id="contact">
        <div>
          <span class="eyebrow light">კონტაქტი</span>
          <h2>გვეწვიე ან დაგვიკავშირდი</h2>
          <p>შოურუმში ადგილზე ნახავ ტექნიკას, მიიღებ კონსულტაციას და შეარჩევ სწორ მოდელს.</p>
        </div>
        <div class="contact-card">
          <a class="contact-phone" href="tel:+995598850503"><Phone :size="19" /> +995 598 850 503</a>
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
                  <button v-for="cat in ['ყველა','ტრაქტორები','მოტობლოკები','გენერატორები','ბენზოხერხები','ტუმბოები','ბაღის ტექნიკა','ინსტრუმენტები']" :key="cat" :class="{active:activeCategory===cat}" @click="activeCategory=cat; visibleCount=12"><span>{{ cat }}</span><b>{{ cat === 'ყველა' ? allProducts.length : allProducts.filter(p => p.category === cat).length }}</b></button>
                </div>
              </section>
              <section class="filter-group">
                <h3>ბრენდი</h3>
                <div class="filter-pills">
                  <button v-for="brand in ['ყველა', ...brands, 'სხვა']" :key="brand" :class="{active:selectedBrand===brand}" @click="selectedBrand=brand; visibleCount=12">{{ brand }}</button>
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
                  <option value="rating">რეიტინგით</option>
                  <option value="discount">ფასდაკლებით</option>
                  <option value="new">ახალი</option>
                </select>
              </section>
              <button class="sidebar-clear" @click="clearFilters">გასუფთავება</button>
            </aside>
            <div class="catalog-results">
              <div class="tabs"><button v-for="cat in ['ყველა','ტრაქტორები','მოტობლოკები','გენერატორები','ბენზოხერხები','ტუმბოები']" :class="{active: activeCategory === cat}" @click="activeCategory = cat; visibleCount = 12">{{ cat }}</button></div>
              <div v-if="filteredProducts.length" class="product-grid">
                <article v-for="product in displayedProducts" :key="product.id" class="product-card">
                  <div class="product-media" @click="openProductPage(product)"><span class="badge">{{ product.badge }}</span><button class="wish" :class="{active: wishlist.has(product.id)}" @click.stop="toggleWishlist(product.id)"><Heart :size="19" /></button><img :src="product.image" :alt="product.name" /></div>
                  <div class="product-info"><small>{{ product.category }}</small><h3 @click="openProductPage(product)">{{ product.name }}</h3><div class="rating"><Star :size="15" fill="currentColor" /> {{ product.rating }} <span>({{ product.reviews }})</span></div><div class="card-trust"><span>განვადება</span><span>გარანტია</span></div><div class="price-row"><div><strong>{{ product.price.toLocaleString() }} ₾</strong><del v-if="product.oldPrice">{{ product.oldPrice.toLocaleString() }} ₾</del></div><button @click="addToCart(product)"><ShoppingBag :size="19" /></button></div><button class="compare-toggle" :class="{active: compareIds.includes(product.id)}" @click="toggleCompare(product)"><Scale :size="16" /> {{ compareIds.includes(product.id) ? 'შედარებიდან მოხსნა' : 'შედარება' }}</button><p class="stock"><Check :size="14" /> მარაგშია</p></div>
                </article>
              </div>
              <div v-if="visibleCount < filteredProducts.length" class="load-more"><p>ნაჩვენებია {{ visibleCount }} / {{ filteredProducts.length }} პროდუქტი</p><div><span :style="{width:(visibleCount/filteredProducts.length*100)+'%'}"></span></div><button @click="visibleCount += 12">მეტის ჩვენება <Plus :size="17" /></button></div>
              <div v-else class="empty-state"><Search /><h3>პროდუქტი ვერ მოიძებნა</h3><button @click="search=''; activeCategory='ყველა'; clearFilters()">გასუფთავება</button></div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <main v-else-if="selectedProduct" class="product-page wrap">
      <button class="back-link" @click="backToProducts"><ArrowLeft :size="18" /> პროდუქტებზე დაბრუნება</button>
      <div class="breadcrumbs">მთავარი <ChevronRight :size="14" /> {{ selectedProduct.category }} <ChevronRight :size="14" /> {{ selectedProduct.name }}</div>
      <section class="product-detail">
        <div class="gallery"><div class="thumbs"><button v-for="n in 3" :class="{active:n===1}" @click="zoomOpen=true"><img :src="selectedProduct.image" /></button></div><div class="main-image zoomable" @click="zoomOpen=true"><span class="badge">{{ selectedProduct.badge }}</span><span class="zoom-hint">⌕ ფოტოს გადიდება</span><img :src="selectedProduct.image" :alt="selectedProduct.name" /></div></div>
        <div class="detail-info"><span class="detail-category">{{ selectedProduct.category }} • SKU: AG-{{ 3600 + selectedProduct.id }}</span><h1>{{ selectedProduct.name }}</h1><div class="detail-rating"><span>★★★★★</span><strong>{{ selectedProduct.rating }}</strong><small>{{ selectedProduct.reviews }} შეფასება</small></div><p class="detail-copy">ძლიერი და საიმედო ტექნიკა ყოველდღიური სამუშაოებისთვის. ეკონომიური ძრავი, გამძლე კონსტრუქცია და მარტივი მართვა.</p><div class="detail-price"><strong>{{ selectedProduct.price.toLocaleString() }} ₾</strong><del v-if="selectedProduct.oldPrice">{{ selectedProduct.oldPrice.toLocaleString() }} ₾</del><span v-if="selectedProduct.oldPrice">ზოგავ {{ selectedProduct.oldPrice - selectedProduct.price }} ₾</span></div><div class="monthly-payment"><CreditCard :size="18" /><span><small>განვადებით</small><strong>თვეში {{ monthlyPayment.toLocaleString() }} ₾-დან</strong></span><em>24 თვემდე</em></div><div class="installment-strip"><CreditCard :size="17" /><span><strong>განვადება ხელმისაწვდომია</strong><small>BOG / TBC / Liberty პირობებით</small></span></div><div class="availability"><span><Check /> მარაგშია — {{ selectedProduct.stock }} ცალი</span><small><Clock3 :size="15" /> გაგზავნა დღესვე</small></div><ul class="spec-list"><li v-for="spec in selectedProduct.specs"><Check :size="16" /> {{ spec }}</li></ul><div class="buy-row"><div class="quantity"><button @click="detailQty=Math.max(1,detailQty-1)"><Minus :size="16" /></button><span>{{ detailQty }}</span><button @click="detailQty++"><Plus :size="16" /></button></div><button class="primary-btn buy-button" @click="addToCart(selectedProduct, detailQty)"><ShoppingBag :size="19" /> კალათაში დამატება</button><button class="icon-btn detail-wish" @click="toggleWishlist(selectedProduct.id)"><Heart :fill="wishlist.has(selectedProduct.id) ? 'currentColor' : 'none'" /></button></div><div class="product-contact-actions"><a href="tel:+995598850503"><Phone :size="17" /> დარეკვა</a><a :href="`https://wa.me/995598850503?text=${encodeURIComponent('გამარჯობა, მაინტერესებს: ' + selectedProduct.name)}`" target="_blank" rel="noopener">WhatsApp-ში კითხვა</a></div><button class="detail-compare" :class="{active: compareIds.includes(selectedProduct.id)}" @click="toggleCompare(selectedProduct)"><Scale :size="17" /> {{ compareIds.includes(selectedProduct.id) ? 'შედარებიდან მოხსნა' : 'პროდუქტის შედარება' }}</button><div class="safe-buy"><ShieldCheck /><span><strong>უსაფრთხო შენაძენი</strong><small>ოფიციალური გარანტია და დაბრუნება 14 დღის განმავლობაში</small></span></div></div>
      </section>
      <section class="product-description-panel">
        <div class="description-tabs"><button :class="{active:detailTab==='description'}" @click="detailTab='description'">აღწერა</button><button :class="{active:detailTab==='specs'}" @click="detailTab='specs'">მახასიათებლები</button><button :class="{active:detailTab==='delivery'}" @click="detailTab='delivery'">მიწოდება და გარანტია</button></div>
        <div v-if="detailTab==='description'" class="description-grid">
          <article class="description-copy">
            <span class="eyebrow">პროდუქტის აღწერა</span>
            <h2>{{ selectedProduct.name }}</h2>
            <p>{{ selectedProduct.name }} განკუთვნილია ყოველდღიური სასოფლო-სამეურნეო და სამუშაო გამოყენებისთვის. მოდელი შერჩეულია ისე, რომ მომხმარებელმა მიიღოს გამძლე კორპუსი, მარტივი მართვა და ეკონომიური მუშაობა.</p>
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
            <ul class="description-specs"><li v-for="spec in selectedProduct.specs" :key="spec"><Check :size="16" /> {{ spec }}</li><li><Check :size="16" /> მარაგი: {{ selectedProduct.stock }} ცალი</li><li><Check :size="16" /> SKU: AG-{{ 3600 + selectedProduct.id }}</li></ul>
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
            ['შეიძლება განვადებით ყიდვა?', 'კი, შესაძლებელია BOG/TBC/Liberty პირობებით. ზუსტი თვიური თანხა ბანკის პირობაზე და არჩეულ ვადაზეა დამოკიდებული.'],
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
          <article v-for="item in cartItems" :key="item.id" class="cart-item"><div class="cart-product"><div class="cart-image"><img :src="item.image" :alt="item.name"></div><div><small>{{ item.category }}</small><h3>{{ item.name }}</h3><span class="stock"><Check :size="13" /> მარაგშია</span></div></div><div class="quantity"><button @click="item.qty=Math.max(1,item.qty-1)"><Minus :size="15" /></button><span>{{ item.qty }}</span><button @click="item.qty++"><Plus :size="15" /></button></div><strong>{{ (item.price*item.qty).toLocaleString() }} ₾</strong><button class="remove-item" @click="removeFromCart(item.id)"><Trash2 :size="18" /></button></article>
          <div v-if="!cartItems.length" class="cart-empty"><ShoppingBag :size="28" /><h3>კალათა ცარიელია</h3><button @click="openProductsPage('ყველა')">პროდუქციის ნახვა</button></div>
          <div v-else class="cart-note"><PackageCheck /><div><strong>უფასო მიწოდება თბილისში</strong><p>შენი შეკვეთა აკმაყოფილებს უფასო მიწოდების პირობას.</p></div></div>
          <div v-if="cartItems.length" class="cart-recommendations">
            <h3>ხშირად ამატებენ შეკვეთას</h3>
            <article v-for="product in cartRecommendations" :key="product.id">
              <img :src="product.image" :alt="product.name">
              <span><strong>{{ product.name }}</strong><small>{{ product.price.toLocaleString() }} ₾</small></span>
              <button @click="addToCart(product)">დამატება</button>
            </article>
          </div>
        </section>
        <aside class="order-summary"><h2>შეკვეთის შეჯამება</h2><div class="summary-line"><span>პროდუქტები</span><strong>{{ cartSubtotal.toLocaleString() }} ₾</strong></div><div class="summary-line"><span>მიწოდება</span><strong class="free">უფასო</strong></div><div class="summary-total"><span>სულ</span><strong>{{ cartSubtotal.toLocaleString() }} ₾</strong></div><div class="next-payment-note"><CreditCard :size="18" /><span><strong>გადახდის მეთოდს შემდეგ ნაბიჯზე აირჩევ</strong><small>განვადება, ნაწილ-ნაწილ ან ბარათით გადახდა</small></span></div><button class="checkout-button">შეკვეთის გაგრძელება <ArrowRight :size="19" /></button><p class="secure-note"><ShieldCheck :size="15" /> დაცული და უსაფრთხო გადახდა</p></aside>
      </div>
    </main>

    <main v-else-if="checkoutStep === 2" class="checkout-page wrap"><div class="checkout-page-head"><button class="back-link" @click="openCart"><ArrowLeft :size="18" /> კალათაში დაბრუნება</button><div class="checkout-steps"><span><b>1</b> კალათა</span><i></i><span class="active"><b>2</b> მონაცემები</span><i></i><span><b>3</b> გადახდა</span></div></div><div class="checkout-grid"><section class="checkout-form-card"><div class="form-heading"><b>01</b><div><h1>საკონტაქტო ინფორმაცია</h1><p>შეკვეთის დასადასტურებლად</p></div></div><div class="form-grid"><label><span>სახელი *</span><input value="გიორგი"></label><label><span>გვარი *</span><input value="ბერიძე"></label><label><span>ტელეფონი *</span><input value="+995 555 12 34 56"></label><label><span>ელფოსტა</span><input value="giorgi@example.com"></label></div><div class="form-heading second"><b>02</b><div><h1>მიწოდების მისამართი</h1><p>სად მოგაწოდოთ შეკვეთა?</p></div></div><div class="delivery-tabs"><button :class="{active:deliveryMethod==='courier'}" @click="deliveryMethod='courier'"><Truck :size="17" /> კურიერით</button><button :class="{active:deliveryMethod==='pickup'}" @click="deliveryMethod='pickup'"><MapPin :size="17" /> მაღაზიიდან გატანა</button></div><div v-if="deliveryMethod==='pickup'" class="branch-options"><label><input type="radio" name="branch" checked><span><strong>თბილისი</strong><small>წერეთლის გამზ. N147</small></span></label><label><input type="radio" name="branch"><span><strong>ოკამი</strong><small>მე-40 კილომეტრი</small></span></label><label><input type="radio" name="branch"><span><strong>ზესტაფონი</strong><small>რუსთაველის ქ. N60</small></span></label></div><div class="form-grid"><label><span>ქალაქი / სოფელი *</span><input value="თბილისი" placeholder="ქალაქი ან სოფელი"></label><label><span>მისამართი *</span><input value="ჭავჭავაძის გამზირი 12"></label><label class="full"><span>შენიშვნა</span><textarea placeholder="დამატებითი ინფორმაცია"></textarea></label></div><button class="checkout-button wide" @click="nextToPayment">გადახდაზე გადასვლა <ArrowRight :size="19" /></button></section><aside class="mini-summary"><h2>შენი შეკვეთა</h2><div v-for="item in cartItems" class="mini-item"><img :src="item.image"><span><strong>{{item.name}}</strong><small>{{item.qty}} × {{item.price.toLocaleString()}} ₾</small></span></div><div class="summary-total"><span>სულ</span><strong>{{cartSubtotal.toLocaleString()}} ₾</strong></div></aside></div></main>

    <main v-else-if="checkoutStep === 3" class="checkout-page wrap"><div v-if="!orderPlaced"><div class="checkout-page-head"><button class="back-link" @click="backToCheckoutDetails"><ArrowLeft :size="18" /> მონაცემებზე დაბრუნება</button><div class="checkout-steps"><span><b>1</b> კალათა</span><i></i><span><b>2</b> მონაცემები</span><i></i><span class="active"><b>3</b> გადახდა</span></div></div><div class="checkout-grid"><section class="checkout-form-card"><div class="form-heading"><b>03</b><div><h1>გადახდის მეთოდი</h1><p>აირჩიე სასურველი ბანკი</p></div></div><div class="payment-options checkout-payments"><label v-for="m in [{id:'bog-installment',logo:'BOG',cls:'bog',name:'BOG განვადება',sub:'3–48 თვე'},{id:'bog-parts',logo:'BOG',cls:'bog',name:'BOG ნაწილ-ნაწილ',sub:'4 თანაბარი გადახდა'},{id:'tbc',logo:'TBC',cls:'tbc',name:'TBC განვადება',sub:'მოქნილი პირობები'},{id:'liberty',logo:'L',cls:'liberty',name:'Liberty გადახდა',sub:'უსაფრთხო გადახდა'}]" :class="{selected:paymentMethod===m.id}"><input v-model="paymentMethod" type="radio" :value="m.id"><span class="bank-logo" :class="m.cls">{{m.logo}}</span><span><strong>{{m.name}}</strong><small>{{m.sub}}</small></span><i></i></label></div><div class="demo-notice"><ShieldCheck /><p><strong>დემო რეჟიმი</strong><br>რეალური საბანკო ოპერაცია არ შესრულდება.</p></div><button class="checkout-button wide" @click="placeOrder">შეკვეთის დადასტურება — {{cartSubtotal.toLocaleString()}} ₾</button></section><aside class="mini-summary"><h2>მიწოდება</h2><p>გიორგი ბერიძე<br>+995 555 12 34 56<br>თბილისი, ჭავჭავაძის გამზირი 12</p><div class="summary-total"><span>სულ</span><strong>{{cartSubtotal.toLocaleString()}} ₾</strong></div></aside></div></div><div v-else class="success-card"><span><Check /></span><small>შეკვეთა მიღებულია</small><h1>მადლობა შენაძენისთვის!</h1><p>შეკვეთის ნომერია <strong>#AT-2026-1048</strong></p><button class="primary-btn" @click="goHome">მთავარ გვერდზე დაბრუნება</button></div></main>

    <main v-else class="admin-shell">
      <aside class="admin-sidebar"><a class="logo" href="#" @click.prevent="goHome"><span class="logo-mark"><Leaf /></span><span>AGRO<span>ADMIN</span></span></a><nav><button v-for="item in [{id:'overview',label:'▦ მიმოხილვა'},{id:'products',label:'▣ პროდუქტები',count:508},{id:'orders',label:'▤ შეკვეთები',count:12},{id:'users',label:'◉ მომხმარებლები'},{id:'categories',label:'◇ კატეგორიები'},{id:'discounts',label:'％ ფასდაკლებები'},{id:'stock',label:'▧ მარაგები'},{id:'settings',label:'⚙ პარამეტრები'}]" :key="item.id" :class="{active:adminTab===item.id}" @click="adminTab=item.id"><span>{{ item.label }}</span><b v-if="item.count">{{ item.count }}</b></button></nav><button class="admin-exit" @click="goHome"><ArrowLeft :size="17" /> მაღაზიაში დაბრუნება</button></aside>
      <section class="admin-content"><header class="admin-header"><div><small>25 სექტემბერი, 2026</small><h1>{{ ({overview:'მიმოხილვა',products:'პროდუქტები',orders:'შეკვეთები',users:'მომხმარებლები',categories:'კატეგორიები',discounts:'ფასდაკლებები',stock:'მარაგები',settings:'პარამეტრები'})[adminTab] }}</h1></div><button><CircleUserRound /> Admin</button></header>
        <template v-if="adminTab==='overview'">
          <div class="stat-grid"><article><span>დღის გაყიდვები</span><strong>12,840 ₾</strong><small>↑ 18.4% წინა კვირასთან</small></article><article><span>ახალი შეკვეთები</span><strong>24</strong><small>12 საჭიროებს დამუშავებას</small></article><article><span>პროდუქტები</span><strong>508</strong><small>8 პროდუქტი მცირე მარაგით</small></article><article><span>მომხმარებლები</span><strong>2,847</strong><small>32 ახალი ამ თვეში</small></article></div>
          <div class="admin-grid"><section class="admin-panel"><div class="panel-head"><div><h2>ბოლო შეკვეთები</h2><p>დღევანდელი აქტივობა</p></div><button @click="adminTab='orders'">ყველას ნახვა</button></div><div class="order-table"><div class="table-head"><span>შეკვეთა</span><span>მომხმარებელი</span><span>თანხა</span><span>გადახდა</span><span>სტატუსი</span></div><div v-for="r in [['#1048','გიორგი ბერიძე','2,049 ₾','BOG განვადება','ახალი'],['#1047','ნინო მაისურაძე','780 ₾','ბარათი','გაგზავნილი'],['#1046','ლევან გელაშვილი','12,650 ₾','TBC განვადება','დამუშავება'],['#1045','მარიამ კიკნაძე','459 ₾','Liberty','დასრულებული']]" class="table-row"><strong>{{r[0]}}</strong><span>{{r[1]}}</span><strong>{{r[2]}}</strong><span>{{r[3]}}</span><em>{{r[4]}}</em></div></div></section><aside class="admin-panel low-stock"><div class="panel-head"><div><h2>მცირე მარაგი</h2><p>საჭიროებს შევსებას</p></div></div><div v-for="item in products.slice(0,4)"><img :src="item.image"><span><strong>{{item.name}}</strong><small>დარჩა {{Math.max(1,item.stock-5)}} ცალი</small></span><button>+</button></div></aside></div>
        </template>
        <section v-else-if="adminTab==='products'" class="admin-panel"><div class="panel-head"><div><h2>პროდუქტების მართვა</h2><p>დამატება, ფასი, ფოტო და მარაგი</p></div><button>+ პროდუქტი</button></div><div class="admin-product-list"><article v-for="item in products"><img :src="item.image"><span><strong>{{ item.name }}</strong><small>{{ item.category }} • {{ item.stock }} ცალი</small></span><b>{{ item.price.toLocaleString() }} ₾</b><button>რედაქტირება</button></article></div></section>
        <section v-else-if="adminTab==='orders'" class="admin-panel"><div class="panel-head"><div><h2>შეკვეთები</h2><p>სტატუსები და გადახდები</p></div><button>ექსპორტი</button></div><div class="order-table admin-wide"><div class="table-head"><span>შეკვეთა</span><span>მომხმარებელი</span><span>თანხა</span><span>გადახდა</span><span>სტატუსი</span></div><div v-for="r in [['#1048','გიორგი ბერიძე','2,049 ₾','BOG განვადება','ახალი'],['#1047','ნინო მაისურაძე','780 ₾','ბარათი','გაგზავნილი'],['#1046','ლევან გელაშვილი','12,650 ₾','TBC განვადება','დამუშავება'],['#1045','მარიამ კიკნაძე','459 ₾','Liberty','დასრულებული'],['#1044','დავით ნოზაძე','349 ₾','ნაღდი','ახალი']]" class="table-row"><strong>{{r[0]}}</strong><span>{{r[1]}}</span><strong>{{r[2]}}</strong><span>{{r[3]}}</span><em>{{r[4]}}</em></div></div></section>
        <section v-else-if="adminTab==='users'" class="admin-panel"><div class="panel-head"><div><h2>მომხმარებლები</h2><p>აქტიური კლიენტები და შეკვეთები</p></div></div><div class="admin-cards"><article v-for="u in [['გიორგი ბერიძე',6],['ნინო მაისურაძე',3],['ლევან გელაშვილი',4],['მარიამ კიკნაძე',2]]"><strong>{{ u[0] }}</strong><span>+995 5XX XX XX XX</span><small>შეკვეთები: {{ u[1] }}</small></article></div></section>
        <section v-else-if="adminTab==='categories'" class="admin-panel"><div class="panel-head"><div><h2>კატეგორიები</h2><p>პროდუქტების დაჯგუფება</p></div><button>+ კატეგორია</button></div><div class="admin-cards"><article v-for="cat in ['ტრაქტორები','მოტობლოკები','გენერატორები','ბენზოხერხები','ტუმბოები','ბაღის ტექნიკა']"><strong>{{ cat }}</strong><span>{{ allProducts.filter(p => p.category === cat).length }} პროდუქტი</span><small>აქტიური</small></article></div></section>
        <section v-else-if="adminTab==='discounts'" class="admin-panel"><div class="panel-head"><div><h2>ფასდაკლებები</h2><p>აქციები და სპეციალური შეთავაზებები</p></div><button>+ აქცია</button></div><div class="admin-cards"><article v-for="item in products.filter(p => p.oldPrice)"><strong>{{ item.name }}</strong><span>{{ item.oldPrice - item.price }} ₾ ეკონომია</span><small>{{ item.badge }}</small></article></div></section>
        <section v-else-if="adminTab==='stock'" class="admin-panel"><div class="panel-head"><div><h2>მარაგები</h2><p>ფილიალების მიხედვით ნაშთები</p></div><button>განახლება</button></div><div class="admin-product-list"><article v-for="item in allProducts.slice(0,8)"><img :src="item.image"><span><strong>{{ item.name }}</strong><small>თბილისი: {{ item.stock }} • ოკამი: {{ Math.max(0,item.stock-3) }} • ზესტაფონი: {{ Math.max(0,item.stock-5) }}</small></span><b>{{ item.stock }} ცალი</b><button>შევსება</button></article></div></section>
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
              <button v-for="brand in ['ყველა', ...brands, 'სხვა']" :key="brand" :class="{active:selectedBrand===brand}" @click="selectedBrand=brand; visibleCount=12">{{ brand }}</button>
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
              <option value="rating">რეიტინგით</option>
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
            <p>ნახე ფასი, მარაგი, შეფასება და მთავარი მახასიათებლები ერთ ფანჯარაში.</p>
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
            <div class="compare-row"><b>ფასდაკლება</b><span v-for="product in compareProducts" :key="product.id">{{ product.oldPrice ? `${(product.oldPrice - product.price).toLocaleString()} ₾` : 'არ აქვს' }}</span></div>
            <div class="compare-row"><b>რეიტინგი</b><span v-for="product in compareProducts" :key="product.id">★ {{ product.rating }} ({{ product.reviews }})</span></div>
            <div class="compare-row"><b>მარაგი</b><span v-for="product in compareProducts" :key="product.id">{{ product.stock }} ცალი</span></div>
            <div class="compare-row compare-specs"><b>მახასიათებლები</b><ul v-for="product in compareProducts" :key="product.id"><li v-for="spec in product.specs" :key="spec">{{ spec }}</li></ul></div>
          </div>
          <div class="compare-actions">
            <button v-for="product in compareProducts" :key="product.id" @click="comparePage=false; openProductPage(product)">ნახე {{ product.name }}</button>
          </div>
        </section>
      </div>
    </transition>

    <footer v-if="!adminPage">
      <div class="wrap footer-grid"><div><a class="logo footer-logo" href="#" @click.prevent="goHome"><span class="logo-mark"><Leaf /></span><span>AGRO<span>TRADE</span></span></a><p>ხარისხიანი სასოფლო-სამეურნეო ტექნიკა და პროფესიონალური მომსახურება.</p></div><div><h4>ნავიგაცია</h4><a href="#" @click.prevent="openProductsPage('ყველა')">პროდუქცია</a><a href="#categories">კატეგორიები</a><a href="#" @click.prevent="scrollToSection('service-info')">სერვისი</a></div><div><h4>მისამართები</h4><a href="https://maps.app.goo.gl/PvjEHT4oDRApxGAeA" target="_blank" rel="noopener">თბილისი, წერეთლის გამზ. N147</a><a href="https://maps.app.goo.gl/3NDu29YprotRacKc9" target="_blank" rel="noopener">ოკამი, მე-40 კმ</a><a href="https://maps.app.goo.gl/BevpdezxUeabjfyn9" target="_blank" rel="noopener">ზესტაფონი, რუსთაველის ქ. N60</a></div><div><h4>კონტაქტი</h4><a href="tel:+995598850503">+995 598 850 503</a><span>ორშ–შაბ: 09:00–18:00</span></div></div><div class="wrap copyright">© 2026 Agro Trade. ყველა უფლება დაცულია. <span>დემო ვერსია</span></div>
    </footer>

    <transition name="toast"><div v-if="addedToast" class="toast"><span><Check /></span> პროდუქტი დაემატა კალათაში</div></transition>
    <transition name="lightbox"><div v-if="zoomOpen && selectedProduct" class="image-lightbox" @click.self="zoomOpen=false"><button class="lightbox-close" @click="zoomOpen=false"><X /></button><div class="lightbox-content"><img :src="selectedProduct.image" :alt="selectedProduct.name"><div><span>{{ selectedProduct.category }}</span><strong>{{ selectedProduct.name }}</strong><small>დააჭირე ფონს დასახურად</small></div></div></div></transition>
    <div v-if="mobileMenu" class="mobile-drawer"><div class="drawer-head"><span>მენიუ</span><button @click="mobileMenu=false"><X /></button></div><button class="mobile-catalog-toggle" @click="catalogOpen=!catalogOpen"><span>კატეგორიები</span><ChevronDown :class="{rotated:catalogOpen}" /></button><div v-if="catalogOpen" class="mobile-category-list"><button v-for="cat in ['ყველა','ტრაქტორები','მოტობლოკები','გენერატორები','ბენზოხერხები','ტუმბოები','ბაღის ტექნიკა','ინსტრუმენტები']" :key="cat" @click="chooseCategory(cat)">{{ cat }}</button></div><a href="#" @click.prevent="openProductsPage('ყველა')">პროდუქცია</a><a href="#" @click.prevent="openOffersPage">შეთავაზებები</a><a href="#" @click.prevent="scrollToSection('service-info')">სერვისი</a><a href="#contact" @click="mobileMenu=false">კონტაქტი</a></div>
    <div v-if="mobileMenu" class="overlay" @click="mobileMenu=false"></div>
  </div>
</template>


