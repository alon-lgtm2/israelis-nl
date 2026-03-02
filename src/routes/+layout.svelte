<script>
  import '../app.css';
  import { onMount } from 'svelte';

  onMount(() => {
    const navbar = document.getElementById('navbar');
    const handleScroll = () => {
      navbar?.classList.toggle('scrolled', window.scrollY > 50);
    };
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  });
</script>

<!-- NAV -->
<nav id="navbar">
  <a href="/" class="nav-logo">Israelis<span>.nl</span></a>
  <ul class="nav-links">
    <li><a href="/#about">About</a></li>
    <li><a href="/#community">Community</a></li>
    <li><a href="/#newsletter">Newsletter</a></li>
    <li><a href="/#newsletter" class="nav-cta">Join Us</a></li>
  </ul>
  <button class="nav-toggle" aria-label="Open menu">
    <span></span><span></span><span></span>
  </button>
</nav>

<!-- PAGE CONTENT -->
<slot />

<!-- FOOTER -->
<footer>
  <div class="footer-grid">
    <div class="footer-brand">
      <a href="/" class="nav-logo">Israelis<span>.nl</span></a>
      <p>Your Israeli community platform in the Netherlands. Connecting Israelis from Amsterdam to Maastricht.</p>
    </div>
    <div>
      <p class="footer-col-title">Community</p>
      <ul class="footer-links">
        <li><a href="/events">Events</a></li>
        <li><a href="/forum">Forum</a></li>
        <li><a href="/resources">Resources</a></li>
        <li><a href="/#newsletter">Newsletter</a></li>
      </ul>
    </div>
    <div>
      <p class="footer-col-title">Cities</p>
      <ul class="footer-links">
        <li><a href="/amsterdam">Amsterdam</a></li>
        <li><a href="/rotterdam">Rotterdam</a></li>
        <li><a href="/den-haag">The Hague</a></li>
        <li><a href="/utrecht">Utrecht</a></li>
      </ul>
    </div>
    <div>
      <p class="footer-col-title">Follow Us</p>
      <ul class="footer-links">
        <li><a href="https://instagram.com" target="_blank" rel="noopener">Instagram</a></li>
        <li><a href="https://facebook.com" target="_blank" rel="noopener">Facebook</a></li>
        <li><a href="https://whatsapp.com" target="_blank" rel="noopener">WhatsApp Groups</a></li>
        <li><a href="https://linkedin.com" target="_blank" rel="noopener">LinkedIn</a></li>
      </ul>
    </div>
  </div>
  <div class="footer-bottom">
    <p class="footer-copy">© 2024 Israelis.nl &nbsp;·&nbsp; All rights reserved</p>
    <div class="footer-flags">🇮🇱&nbsp;🇳🇱</div>
  </div>
</footer>
