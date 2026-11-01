<script setup>
import { computed, nextTick, onMounted, onUnmounted, ref } from 'vue'

const dark = ref(false)
const menuOpen = ref(false)
const searchOpen = ref(false)
const searchQuery = ref('')
const activeFilter = ref('All')
const selectedStory = ref(null)
const toast = ref('')
const email = ref('')
const emailState = ref('idle')
const headerScrolled = ref(false)
const scrollProgress = ref(0)
const activeSection = ref('top')
const cursorVisible = ref(false)
const cursorLabel = ref('Open')
const cursorX = ref(0)
const cursorY = ref(0)

let sectionObserver

const filters = ['All', 'Scent', 'Carry', 'Desk', 'Home']

const stories = [
  {
    id: 'scent-after-rain',
    title: 'A scent after rain',
    deck: 'Five fragrances that stay close to the skin, never the room.',
    category: 'Scent',
    number: '01',
    date: '28 OCT 2026',
    read: '7 min read',
    image: 'https://images.unsplash.com/photo-1541643600914-78b084683601?auto=format&fit=crop&w=1300&q=85',
    accent: 'olive',
    body: 'There is a particular kind of fragrance that feels like weather rather than perfume: mineral, transparent, and just a little green. We tested the quiet contenders that make sense for a close day in the city.',
    products: ['A mineral eau de parfum', 'A green tea cologne', 'A soft skin musk'],
  },
  {
    id: 'carry-less',
    title: 'Carry less. Carry better.',
    deck: 'The small everyday objects that made our bag feel lighter.',
    category: 'Carry',
    number: '02',
    date: '22 OCT 2026',
    read: '5 min read',
    image: 'https://images.unsplash.com/photo-1553062407-98eeb64c6a62?auto=format&fit=crop&w=1300&q=85',
    accent: 'orange',
    body: 'A better everyday carry is rarely about adding another gadget. It is about removing friction: one cable, one notebook, one object that is a pleasure to reach for.',
    products: ['A compact sling', 'A braided USB-C cable', 'A notebook that opens flat'],
  },
  {
    id: 'desk-light',
    title: 'The desk, in a softer light',
    deck: 'An edit of lamps and small rituals for the final hour of work.',
    category: 'Desk',
    number: '03',
    date: '15 OCT 2026',
    read: '6 min read',
    image: 'https://images.unsplash.com/photo-1494438639946-1ebd1d20bf85?auto=format&fit=crop&w=1300&q=85',
    accent: 'blue',
    body: 'Good light is infrastructure. This is a practical guide to making a working surface feel deliberate at night, without turning it into a showroom.',
    products: ['A warm task lamp', 'A felt desk mat', 'A quiet timer'],
  },
  {
    id: 'hotel-bathroom',
    title: 'The hotel bathroom test',
    deck: 'What we look for in a travel-sized grooming kit.',
    category: 'Home',
    number: '04',
    date: '09 OCT 2026',
    read: '8 min read',
    image: 'https://images.unsplash.com/photo-1556228720-195a672e8a03?auto=format&fit=crop&w=1300&q=85',
    accent: 'pink',
    body: 'A good travel kit earns space by being useful twice: once in the hotel, then again when the trip is over. We care about refillability, texture, and the little details that survive a rushed morning.',
    products: ['A refillable bottle set', 'A compact toiletry case', 'A fast-drying towel'],
  },
  {
    id: 'under-2000',
    title: 'Under ₹2,000, properly chosen',
    deck: 'Seven useful gifts that do not feel like last-minute gifts.',
    category: 'Carry',
    number: '05',
    date: '03 OCT 2026',
    read: '9 min read',
    image: 'https://images.unsplash.com/photo-1513519245088-0e12902e5a38?auto=format&fit=crop&w=1300&q=85',
    accent: 'yellow',
    body: 'The price ceiling is real; the compromise does not have to be. A shortlist of tactile, useful objects for people who would rather receive one good thing than three novelty things.',
    products: ['A linen-bound journal', 'A small bedside speaker', 'A well-made card case'],
  },
  {
    id: 'shelf-life',
    title: 'A shelf with some shelf-life',
    deck: 'Objects that improve a room without asking to be admired.',
    category: 'Home',
    number: '06',
    date: '27 SEP 2026',
    read: '6 min read',
    image: 'https://images.unsplash.com/photo-1616486338812-3dadae4b4ace?auto=format&fit=crop&w=1300&q=85',
    accent: 'green',
    body: 'The best home objects become background actors. We looked for pieces with a useful job, a good material story, and enough restraint to still feel right next year.',
    products: ['A stone catchall', 'A sculptural bookend', 'A low-maintenance plant'],
  },
  {
    id: 'morning-cup',
    title: 'The morning cup, reconsidered',
    deck: 'Three ways to make the first coffee of the day less automatic.',
    category: 'Home',
    number: '07',
    date: '19 SEP 2026',
    read: '5 min read',
    image: 'https://images.unsplash.com/photo-1495474472287-4d71bcdd2085?auto=format&fit=crop&w=1300&q=85',
    accent: 'orange',
    body: 'The best coffee objects do not demand a new personality. They make a familiar ritual easier, warmer, and slightly more yours.',
    products: ['A forgiving pour-over brewer', 'A double-wall ceramic cup', 'A compact kitchen scale'],
  },
  {
    id: 'weekend-uniform',
    title: 'A weekend uniform for the long way round',
    deck: 'The durable basics that make packing for two days almost boring.',
    category: 'Carry',
    number: '08',
    date: '12 SEP 2026',
    read: '7 min read',
    image: 'https://images.unsplash.com/photo-1523381210434-271e8be1f52b?auto=format&fit=crop&w=1300&q=85',
    accent: 'blue',
    body: 'A good weekend bag is a small system. These are the layers, containers, and materials that make it work without overplanning.',
    products: ['A weatherproof overshirt', 'A soft-sided weekender', 'A pair of everyday trainers'],
  },
  {
    id: 'quiet-tech',
    title: 'Quiet tech for loud days',
    deck: 'The small digital helpers that stay out of the way.',
    category: 'Desk',
    number: '09',
    date: '05 SEP 2026',
    read: '8 min read',
    image: 'https://images.unsplash.com/photo-1516321318423-f06f85e504b3?auto=format&fit=crop&w=1300&q=85',
    accent: 'green',
    body: 'Technology can be useful without becoming the centre of a room. We kept the shortlist to tools that disappear until you need them.',
    products: ['A distraction-light keyboard', 'A compact charging dock', 'A pair of comfortable headphones'],
  },
  {
    id: 'gift-without-gimmick',
    title: 'The gift without the gimmick',
    deck: 'A field guide to giving someone a better ordinary day.',
    category: 'Scent',
    number: '10',
    date: '29 AUG 2026',
    read: '6 min read',
    image: 'https://images.unsplash.com/photo-1602173574767-37ac01994b2a?auto=format&fit=crop&w=1300&q=85',
    accent: 'yellow',
    body: 'A good gift is a small act of attention. These are useful things with enough character to feel chosen, not simply purchased.',
    products: ['A subtle personal fragrance', 'A handmade soap set', 'A small object for a daily ritual'],
  },
]

const visibleStories = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()
  return stories.filter((story) => {
    const matchesFilter = activeFilter.value === 'All' || story.category === activeFilter.value
    const matchesSearch = !query || `${story.title} ${story.deck} ${story.category}`.toLowerCase().includes(query)
    return matchesFilter && matchesSearch
  })
})

function toggleTheme() {
  dark.value = !dark.value
}

function openStory(story) {
  selectedStory.value = story
  document.body.classList.add('modal-open')
}

function closeStory() {
  selectedStory.value = null
  document.body.classList.remove('modal-open')
}

function scrollTo(id) {
  menuOpen.value = false
  nextTick(() => document.getElementById(id)?.scrollIntoView({ behavior: 'smooth' }))
}

function handleScroll() {
  const scrollable = document.documentElement.scrollHeight - window.innerHeight
  scrollProgress.value = scrollable > 0 ? window.scrollY / scrollable : 0
  headerScrolled.value = window.scrollY > 28
}

function moveCursor(event) {
  cursorX.value = event.clientX
  cursorY.value = event.clientY
}

function showCursor(label = 'Open') {
  cursorLabel.value = label
  cursorVisible.value = true
}

function hideCursor() {
  cursorVisible.value = false
}

function tiltCard(event) {
  const card = event.currentTarget
  const bounds = card.getBoundingClientRect()
  const x = (event.clientX - bounds.left) / bounds.width
  const y = (event.clientY - bounds.top) / bounds.height
  card.style.setProperty('--rx', `${(0.5 - y) * 3.5}deg`)
  card.style.setProperty('--ry', `${(x - 0.5) * 4.5}deg`)
}

function resetTilt(event) {
  const card = event.currentTarget
  card.style.setProperty('--rx', '0deg')
  card.style.setProperty('--ry', '0deg')
  hideCursor()
}

function showToast(message) {
  toast.value = message
  window.setTimeout(() => { toast.value = '' }, 3300)
}

function subscribe() {
  if (!email.value || !email.value.includes('@')) {
    emailState.value = 'error'
    return
  }
  emailState.value = 'success'
  email.value = ''
}

function onKeydown(event) {
  if (event.key === 'Escape') {
    closeStory()
    searchOpen.value = false
    menuOpen.value = false
  }
}

onMounted(() => {
  window.addEventListener('keydown', onKeydown)
  window.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('pointermove', moveCursor, { passive: true })
  handleScroll()

  sectionObserver = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) activeSection.value = entry.target.id
    })
  }, { rootMargin: '-36% 0px -54% 0px', threshold: 0 })

  document.querySelectorAll('[data-section]').forEach((section) => sectionObserver.observe(section))
})

onUnmounted(() => {
  window.removeEventListener('keydown', onKeydown)
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('pointermove', moveCursor)
  sectionObserver?.disconnect()
})
</script>

<template>
  <div class="site-shell" :class="{ 'is-dark': dark }">
    <div class="scroll-progress" aria-hidden="true"><span :style="{ transform: `scaleX(${scrollProgress})` }"></span></div>

    <div class="announcement">
      <span>THE OBJECT EDIT / ISSUE 01</span>
      <span class="announcement-center">A quieter way to choose</span>
      <button class="announcement-link" @click="scrollTo('newsletter')">Join the list <span>↗</span></button>
    </div>

    <header class="site-header" :class="{ 'is-scrolled': headerScrolled }">
      <button class="wordmark" aria-label="Back to top" @click="scrollTo('top')">
        <span class="wordmark-mark">O</span>
        <span class="wordmark-text">THE OBJECT<br />EDIT</span>
      </button>

      <nav class="desktop-nav" aria-label="Primary navigation">
        <button @click="scrollTo('stories')">Stories</button>
        <button @click="scrollTo('method')">Method</button>
        <button @click="scrollTo('about')">About</button>
      </nav>

      <div class="header-actions">
        <button class="icon-button" aria-label="Toggle colour theme" @click="toggleTheme">
          <span>{{ dark ? '☼' : '◐' }}</span>
        </button>
        <button class="icon-button" aria-label="Search stories" @click="searchOpen = !searchOpen">⌕</button>
        <button class="menu-button" :aria-expanded="menuOpen" @click="menuOpen = !menuOpen">
          <span class="menu-lines"><i></i><i></i></span>
          <span>Menu</span>
        </button>
      </div>
    </header>

    <Transition name="menu-reveal">
      <div v-if="menuOpen" class="menu-layer" @click.self="menuOpen = false">
        <div class="mobile-menu">
          <div class="menu-label">Navigate / 00</div>
          <button @click="scrollTo('stories')">Stories <span>01</span></button>
          <button @click="scrollTo('method')">Method <span>02</span></button>
          <button @click="scrollTo('about')">About <span>03</span></button>
          <button @click="scrollTo('newsletter')">Join the list <span>↗</span></button>
        </div>
      </div>
    </Transition>

    <Transition name="search-reveal">
      <div v-if="searchOpen" class="search-drawer">
        <div class="search-inner">
          <label for="story-search">Search the edit</label>
          <input id="story-search" v-model="searchQuery" autofocus placeholder="Try “desk”, “scent”, or “gifts”" />
          <button aria-label="Close search" @click="searchOpen = false">×</button>
        </div>
      </div>
    </Transition>

    <aside class="section-rail" aria-label="Page progress">
      <button v-for="section in ['top', 'stories', 'method', 'about', 'newsletter']" :key="section" :class="{ active: activeSection === section }" @click="scrollTo(section)">
        <span></span><small>{{ section === 'top' ? 'Start' : section }}</small>
      </button>
    </aside>

    <div class="cursor-orb" :class="{ visible: cursorVisible }" :style="{ left: `${cursorX}px`, top: `${cursorY}px` }"><span>{{ cursorLabel }}</span></div>

    <main id="top">
      <section class="hero section-pad" data-section="top">
        <div class="hero-kicker reveal-up"><span class="status-dot"></span> Independent buying notes / Est. 2026</div>
        <div class="hero-heading-wrap">
          <h1 class="hero-title reveal-up delay-1">Good things,<br /><em>properly considered.</em></h1>
          <div class="hero-index reveal-up delay-2"><span>Scroll to explore</span><b>↓</b></div>
        </div>
        <div class="hero-lower">
          <p class="hero-intro reveal-up delay-2">Objects for living, working, travelling and giving. We test the details, name the trade-offs, and keep the shortlist short.</p>
          <div class="hero-stamp reveal-up delay-3"><span>NO<br />NOISE</span><b>✳</b><span>JUST<br />SIGNAL</span></div>
        </div>
      </section>

      <div class="ticker" aria-hidden="true">
        <div class="ticker-track">
          <span>FIELD NOTES <b>✳</b> BUY LESS, NOTICE MORE <b>✳</b> OBJECTS WITH A POINT OF VIEW <b>✳</b> </span>
          <span>FIELD NOTES <b>✳</b> BUY LESS, NOTICE MORE <b>✳</b> OBJECTS WITH A POINT OF VIEW <b>✳</b> </span>
        </div>
      </div>

      <section class="feature-section section-pad" aria-label="Featured story">
        <div class="section-meta"><span>Featured / 001</span><span>New from the edit</span></div>
        <article class="feature-card tilt-card" @click="openStory(stories[0])" @mousemove="tiltCard" @mouseenter="showCursor('Read')" @mouseleave="resetTilt">
          <div class="feature-image image-wrap">
            <img :src="stories[0].image" :alt="stories[0].title" />
            <div class="image-caption"><span>Field note 001</span><span>Open story ↗</span></div>
          </div>
          <div class="feature-copy">
            <div class="eyebrow">{{ stories[0].category }} <span>— {{ stories[0].date }}</span></div>
            <h2>{{ stories[0].title }}</h2>
            <p>{{ stories[0].deck }}</p>
            <button class="line-link">Read the notes <span>↗</span></button>
          </div>
        </article>
      </section>

      <section id="stories" class="stories-section section-pad" data-section="stories">
        <div class="section-heading">
          <div>
            <div class="eyebrow">The latest notes</div>
            <h2>Less browsing.<br /><em>More finding.</em></h2>
          </div>
          <p>Original guides and comparisons for people who prefer a reason with their recommendation.</p>
        </div>

        <div class="filter-row" aria-label="Filter stories">
          <button v-for="filter in filters" :key="filter" :class="{ active: activeFilter === filter }" @click="activeFilter = filter">{{ filter }}</button>
          <span class="result-count">{{ visibleStories.length.toString().padStart(2, '0') }} notes</span>
        </div>

        <TransitionGroup v-if="visibleStories.length" name="story-list" tag="div" class="story-grid">
          <article v-for="story in visibleStories" :key="story.id" class="story-card tilt-card" @click="openStory(story)" @mousemove="tiltCard" @mouseenter="showCursor('Open')" @mouseleave="resetTilt">
            <div class="story-image image-wrap" :class="`accent-${story.accent}`">
              <img :src="story.image" :alt="story.title" loading="lazy" />
              <span class="story-number">{{ story.number }}</span>
              <span class="story-arrow">↗</span>
            </div>
            <div class="story-info">
              <div class="story-topline"><span>{{ story.category }}</span><span>{{ story.date }}</span></div>
              <h3>{{ story.title }}</h3>
              <p>{{ story.deck }}</p>
              <span class="read-time">{{ story.read }}</span>
            </div>
          </article>
        </TransitionGroup>
        <div v-else class="empty-state">No notes found. Try a different word or category.</div>
      </section>

      <section id="method" class="method-section" data-section="method">
        <div class="method-intro section-pad">
          <div class="eyebrow">Our method / 002</div>
          <h2>Useful is<br /><em>beautiful.</em></h2>
          <p>Every recommendation starts with a job to do. We look at how it feels, how it holds up, and whether it earns the space it takes.</p>
        </div>
        <div class="method-steps">
          <div class="method-step"><span>01</span><h3>Define the job</h3><p>We start with the actual problem, not a product category.</p></div>
          <div class="method-step"><span>02</span><h3>Live with it</h3><p>Details only reveal themselves after the first impression.</p></div>
          <div class="method-step"><span>03</span><h3>Name the trade-off</h3><p>Nothing is perfect. We tell you what you gain and what you give up.</p></div>
          <div class="method-step"><span>04</span><h3>Keep it short</h3><p>A small shortlist is a service. More choice is not always more useful.</p></div>
        </div>
      </section>

      <section id="about" class="manifesto section-pad" data-section="about">
        <div class="manifesto-orbit"><span>THE OBJECT EDIT</span><span>THE OBJECT EDIT</span><span>THE OBJECT EDIT</span></div>
        <div class="manifesto-copy">
          <div class="eyebrow">A note from the editor</div>
          <h2>Choose with<br /><em>intention.</em></h2>
          <p>We live in a world of infinite tabs and very little certainty. The Object Edit is a small, independent corner for the considered purchase: products with a point of view, reviewed in plain language.</p>
          <button class="line-link" @click="showToast('The long version is coming in Issue 02.')">Read the long version <span>↗</span></button>
        </div>
      </section>

      <section id="newsletter" class="newsletter section-pad" data-section="newsletter">
        <div class="newsletter-top"><span>03 / The dispatch</span><span>Once a month, never noisy</span></div>
        <div class="newsletter-content">
          <h2>A good thing<br /><em>in your inbox.</em></h2>
          <div class="newsletter-form-wrap">
            <p>One considered shortlist, a useful guide, and a few objects worth noticing.</p>
            <form @submit.prevent="subscribe">
              <label class="sr-only" for="email">Your email address</label>
              <input id="email" v-model="email" type="email" placeholder="Your email address" :class="{ invalid: emailState === 'error' }" />
              <button type="submit">Join <span>↗</span></button>
            </form>
            <small v-if="emailState === 'error'" class="form-message error">Please add a valid email.</small>
            <small v-if="emailState === 'success'" class="form-message">You’re on the list. Issue 01 is on its way.</small>
          </div>
        </div>
      </section>
    </main>

    <footer class="site-footer section-pad">
      <div class="footer-top">
        <div class="footer-brand"><span class="wordmark-mark">O</span><span>THE OBJECT EDIT</span></div>
        <p>Considered things for everyday life.</p>
        <div class="footer-links"><a href="#stories">Instagram</a><a href="#stories">Pinterest</a><a href="mailto:hello@theobjectedit.example">Email</a></div>
      </div>
      <div class="footer-bottom">
        <span>© 2026 The Object Edit</span>
        <button @click="showToast('Privacy page placeholder — connect your policy before launch.')">Privacy</button>
        <button @click="showToast('Affiliate links are not active in this preview.')">Disclosure</button>
        <span>Made with care / {{ dark ? 'Night' : 'Day' }} mode</span>
      </div>
      <div class="affiliate-note">Affiliate note: this preview contains no active affiliate links. Before publishing Amazon links, replace this note with the exact disclosure required by the Associates agreement and place clear link-level disclosures beside each monetised link.</div>
    </footer>

    <Transition name="modal-reveal">
      <div v-if="selectedStory" class="story-modal" role="dialog" aria-modal="true" :aria-label="selectedStory.title" @click.self="closeStory">
        <button class="modal-close" aria-label="Close story" @click="closeStory">×</button>
        <div class="modal-panel">
          <div class="modal-image"><img :src="selectedStory.image" :alt="selectedStory.title" /></div>
          <div class="modal-copy">
            <div class="story-topline"><span>{{ selectedStory.category }} / {{ selectedStory.number }}</span><span>{{ selectedStory.date }}</span></div>
            <h2>{{ selectedStory.title }}</h2>
            <p class="modal-deck">{{ selectedStory.deck }}</p>
            <p>{{ selectedStory.body }}</p>
            <div class="modal-rule"></div>
            <div class="shortlist-title">The shortlist</div>
            <ul><li v-for="product in selectedStory.products" :key="product"><span>↳</span>{{ product }}</li></ul>
            <button class="modal-cta" @click="showToast('Product links will be activated after affiliate approval.')">View the shortlist <span>↗</span></button>
            <small class="modal-disclosure">Editorial note: recommendations are independent. Any future paid links will be labelled clearly.</small>
          </div>
        </div>
      </div>
    </Transition>

    <transition name="toast"><div v-if="toast" class="toast">{{ toast }}</div></transition>
  </div>
</template>
