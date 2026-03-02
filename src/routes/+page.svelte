<script>
  import { onMount } from 'svelte';

  let formSuccess = false;

  onMount(() => {
    // Scroll-reveal
    const reveals = document.querySelectorAll('.reveal');
    const io = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) {
          e.target.classList.add('visible');
          io.unobserve(e.target);
        }
      });
    }, { threshold: 0.14 });
    reveals.forEach(el => io.observe(el));
  });

  function handleSubmit(e) {
    e.preventDefault();
    formSuccess = true;
  }
</script>

<svelte:head>
  <title>Israelis.nl — Israeli Community in the Netherlands</title>
  <meta name="description" content="The home for Israelis living in the Netherlands. Connect, share, and belong." />
</svelte:head>

<!-- ─── HERO ─────────────────────────────────── -->
<section class="hero">
  <div class="hero-bg">
    <svg viewBox="0 0 1440 900" preserveAspectRatio="xMidYMid slice" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <pattern id="sp" width="90" height="104" patternUnits="userSpaceOnUse">
          <polygon points="45,5 55,22 72,22 59,34 64,51 45,40 26,51 31,34 18,22 35,22"
                   fill="none" stroke="white" stroke-width="1.2"/>
        </pattern>
      </defs>
      <rect width="100%" height="100%" fill="url(#sp)"/>
    </svg>
    <div class="hero-gradient"></div>
    <div class="hero-orange-glow"></div>
  </div>

  <div class="hero-content">
    <p class="hero-eyebrow">Israeli Community in the Netherlands</p>
    <h1 class="hero-title">
      Israelis<span class="accent">.nl</span>
    </h1>
    <p class="hero-tagline">Where Israelis in the Netherlands connect, share, and belong.</p>
    <p class="hero-sub">ברוכים הבאים &nbsp;·&nbsp; Welkom &nbsp;·&nbsp; Welcome</p>
    <div class="hero-actions">
      <a href="#community" class="btn-primary">Explore Community</a>
      <a href="#about" class="btn-outline">Our Story</a>
    </div>
  </div>

  <div class="hero-stats">
    <div class="stat-item">
      <div class="stat-number">20<span>K+</span></div>
      <div class="stat-label">Israelis in NL</div>
    </div>
    <div class="stat-item">
      <div class="stat-number"><span>#</span>1</div>
      <div class="stat-label">Community Hub</div>
    </div>
    <div class="stat-item">
      <div class="stat-number">7</div>
      <div class="stat-label">Cities</div>
    </div>
  </div>

  <div class="hero-scroll">
    <div class="scroll-line"></div>
    Scroll
  </div>
</section>

<!-- ─── ABOUT ────────────────────────────────── -->
<section class="about" id="about">
  <div class="container">
    <div class="about-grid">
      <div>
        <p class="section-label reveal">Our Story</p>
        <h2 class="section-title reveal reveal-d1">A home away<br>from <em>home</em></h2>
        <p class="section-body reveal reveal-d2">
          Whether you've just landed in Amsterdam, been living in Rotterdam for years, or are planning your move — Israelis.nl is your community platform. We bring together tens of thousands of Israelis across the Netherlands.
        </p>
        <p class="section-body reveal reveal-d2">
          Find local events, get practical advice, share your experiences, and build real connections with people who understand where you come from.
        </p>
        <div class="about-indent reveal reveal-d3">
          <p>From BSN registration tips to Friday-night dinners, from Hebrew tutors to the best hummus in Amsterdam — it all lives here.</p>
        </div>
      </div>

      <div class="reveal reveal-d2">
        <div class="about-card">
          <div class="about-stripe">
            <div class="stripe"></div>
            <div class="stripe"></div>
            <div class="stripe o"></div>
            <div class="stripe o"></div>
            <div class="stripe"></div>
            <div class="stripe"></div>
          </div>
          <p class="about-quote">"Two flags, one community. Our roots are Israeli, our home is the Netherlands."</p>
          <p class="about-quote-author">— The Israelis.nl Team</p>
        </div>
        <div class="about-footnote">
          <p>🇮🇱 &nbsp;Founded by Israelis who know the expat experience firsthand &nbsp;🇳🇱</p>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ─── COMMUNITY ─────────────────────────────── -->
<section class="community" id="community">
  <div class="container">
    <div class="community-header">
      <p class="section-label reveal">What We Offer</p>
      <h2 class="section-title reveal reveal-d1">Built for our<br><em>community</em></h2>
    </div>
    <div class="cards-grid">
      <div class="card reveal reveal-d1">
        <span class="card-icon">📅</span>
        <h3 class="card-title">Events &amp; Meetups</h3>
        <p class="card-body">From Shabbat dinners in Amsterdam to Israeli film screenings in The Hague — discover events happening near you every week.</p>
        <a href="#" class="card-link">Browse Events</a>
      </div>
      <div class="card reveal reveal-d2">
        <span class="card-icon">💬</span>
        <h3 class="card-title">Community Forum</h3>
        <p class="card-body">Ask questions, share tips, find a flat, recommend a dentist, or just chat in Hebrew. The most active Israeli expat forum in the Netherlands.</p>
        <a href="#" class="card-link">Join the Forum</a>
      </div>
      <div class="card reveal reveal-d3">
        <span class="card-icon">🗂️</span>
        <h3 class="card-title">Resources &amp; Guides</h3>
        <p class="card-body">BSN registration, healthcare, schools, housing — practical guides you actually need when you move to the Netherlands.</p>
        <a href="#" class="card-link">View Resources</a>
      </div>
    </div>
  </div>
</section>

<!-- ─── NEWSLETTER ────────────────────────────── -->
<section class="newsletter" id="newsletter">
  <div class="newsletter-inner">
    <div>
      <p class="section-label" style="color: var(--orange-warm)">Stay Connected</p>
      <h2 class="newsletter-title">Join the<br><span>community</span>.</h2>
      <p class="newsletter-desc">Get the weekly digest: events, community news, and useful guides — in your inbox, in English, with the occasional Hebrew joke.</p>
    </div>
    <form on:submit={handleSubmit}>
      <div class="form-row">
        <div class="form-group">
          <label class="form-label">First Name</label>
          <input type="text" class="form-input" placeholder="Yael" />
        </div>
        <div class="form-group">
          <label class="form-label">Last Name</label>
          <input type="text" class="form-input" placeholder="Cohen" />
        </div>
      </div>
      <div class="form-group">
        <label class="form-label">Email Address</label>
        <input type="email" class="form-input" placeholder="you@example.com" required />
      </div>
      <div class="form-group">
        <label class="form-label">Your City</label>
        <input type="text" class="form-input" placeholder="Amsterdam, Rotterdam, Utrecht…" />
      </div>
      <button type="submit" class="form-submit">Subscribe to Newsletter →</button>
      {#if formSuccess}
        <div class="success-msg">✓ Todah! You're on the list. Welcome to the community 🇮🇱🇳🇱</div>
      {/if}
    </form>
  </div>
</section>
