<script>
  import '../app.css';
  import { lang } from '$lib/lang.js';

  const footerText = {
    he: 'נבנה באהבה מאמסטלפיין',
    nl: 'Met liefde gebouwd vanuit Amstelveen',
    en: 'Built with love from Amstelveen',
  };
</script>

<slot />

<footer class="footer">
  <div class="footer-brand">israelis<span class="dot">.</span>nl</div>
  <p>{footerText[$lang] || footerText.he} 🧡</p>
</footer>
