<script>
  import { onMount } from 'svelte';

  onMount(() => {
    // Scroll-reveal for project cards
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const idx = Array.from(document.querySelectorAll('.project-card')).indexOf(entry.target);
          entry.target.style.animationDelay = `${idx * 0.1}s`;
          entry.target.classList.add('visible');
          observer.unobserve(entry.target);
        }
      });
    }, { threshold: 0.15 });

    document.querySelectorAll('.project-card').forEach(card => observer.observe(card));

    // Smooth scroll on hint click
    document.querySelector('.hero-scroll-hint')?.addEventListener('click', () => {
      document.querySelector('.section-intro')?.scrollIntoView({ behavior: 'smooth' });
    });
  });
</script>

<svelte:head>
  <title>israelis.nl — הבית של הישראלים בהולנד</title>
  <meta name="description" content="קהילה, תוכן, כלים ויוזמות לישראלים בהולנד" />
</svelte:head>

<!-- HERO -->
<section class="hero">
  <div class="float-elements">
    <div class="float-el"></div>
    <div class="float-el"></div>
    <div class="float-el"></div>
  </div>
  <div class="hero-content">
    <h1 class="hero-logo">israelis<span class="dot">.</span>nl</h1>
    <p class="hero-tagline">הבית של הישראלים בהולנד</p>
  </div>
  <div class="hero-scroll-hint">
    <span>גלו עוד</span>
    <div class="scroll-arrow"></div>
  </div>
</section>

<!-- PROJECTS -->
<div class="section-intro">
  <h2>הפרויקטים שלנו</h2>
  <p>קהילה, תוכן, כלים ויוזמות — הכל נבנה מתוך אהבה לקהילה הישראלית בהולנד</p>
</div>

<div class="projects">

  <!-- אז מה עושים היום -->
  <div class="project-card card-hayom">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">🎯</span>
    <div class="card-category">פעילויות</div>
    <div class="card-title">אז מה עושים היום</div>
    <div class="card-desc">המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש. כי גוגל לא יספר לך מה קורה השבת ליד הבית.</div>
    <div style="display:flex; gap:1rem; margin-top:1.2rem; flex-wrap:wrap;">
      <a href="/hayom" class="card-link" style="margin-top:0;">לפרויקט <span class="arrow">←</span></a>
      <a href="https://www.facebook.com/groups/907652610513541" class="card-link" style="margin-top:0; opacity:0.7;" target="_blank" rel="noopener">פייסבוק <span class="arrow">←</span></a>
    </div>
  </div>

  <!-- הייטקיסטים בהולנד -->
  <div class="project-card card-hitech">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">💻</span>
    <div class="card-category">קהילה</div>
    <div class="card-title">הייטקיסטים בהולנד</div>
    <div class="card-desc">הקהילה הגדולה להייטקיסטים ישראלים בהולנד — מ-2019. עזרה במציאת עבודה, טיפים לרילוקיישן, ונטוורקינג.</div>
    <div style="display:flex; gap:1rem; margin-top:1.2rem; flex-wrap:wrap;">
      <a href="https://tech.israelis.nl" class="card-link" style="margin-top:0;">לאתר <span class="arrow">←</span></a>
      <a href="https://www.facebook.com/groups/426953251245124" class="card-link" style="margin-top:0; opacity:0.7;" target="_blank" rel="noopener">פייסבוק <span class="arrow">←</span></a>
    </div>
  </div>

  <!-- NCA ליגת האמהות -->
  <div class="project-card card-nca">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">🏐</span>
    <div class="card-category">ספורט</div>
    <div class="card-title">NCA ליגת האמהות</div>
    <div class="card-desc">ליגת האמהות ההולנדית בכדורשת. ספורט, כיף, וקהילה לאמהות ישראליות בהולנד.</div>
    <a href="/nca" class="card-link">לליגה <span class="arrow">←</span></a>
  </div>

  <!-- פאפות אמסטלפיין -->
  <div class="project-card card-papot">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">👶</span>
    <div class="card-category">הורות</div>
    <div class="card-title">פאפות אמסטלפיין</div>
    <div class="card-desc">קהילת ההורים הישראלים באמסטלפיין והסביבה. טיפים, מפגשים, ותמיכה הדדית.</div>
    <a href="/papot" class="card-link">לקהילה <span class="arrow">←</span></a>
  </div>

  <!-- סבא בא -->
  <div class="project-card card-saba">
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-emoji">👴</span>
    <div class="card-category">משפחה</div>
    <div class="card-title">סבא בא</div>
    <div class="card-desc">כשסבא וסבתא באים לביקור בהולנד — המדריך השלם להפוך את הביקור לחוויה בלתי נשכחת.</div>
    <a href="/saba" class="card-link">בקרוב <span class="arrow">←</span></a>
  </div>

  <!-- הולנד הקלאסית -->
  <div class="project-card card-holland">
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-emoji">🌷</span>
    <div class="card-category">תיירות</div>
    <div class="card-title">הולנד הקלאסית</div>
    <div class="card-desc">המקומות שכל ישראלי בהולנד חייב להכיר. טחנות רוח, גבינות, ותעלות — הגרסה האותנטית.</div>
    <a href="/holland" class="card-link">בקרוב <span class="arrow">←</span></a>
  </div>

  <!-- הגדה על המפה -->
  <div class="project-card card-hagada">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">🗺️</span>
    <div class="card-category">תרבות</div>
    <div class="card-title">הגדה על המפה</div>
    <div class="card-desc">מפה אינטראקטיבית של מקומות יהודיים וישראליים בהולנד. היסטוריה, קהילה, וזיכרון.</div>
    <a href="/hagada" class="card-link">למפה <span class="arrow">←</span></a>
  </div>

  <!-- HateCheck -->
  <div class="project-card card-hatecheck">
    <span class="card-status status-live">פעיל</span>
    <span class="card-emoji">🛡️</span>
    <div class="card-category">כלים</div>
    <div class="card-title">HateCheck</div>
    <div class="card-desc">כלי לזיהוי ומעקב אחרי תוכן שנאה ברשת. כי מגיע לנו מרחב מקוון בטוח.</div>
    <a href="/hatecheck" class="card-link">לכלי <span class="arrow">←</span></a>
  </div>

  <!-- מוצאים עבודה בהולנד -->
  <div class="project-card card-jobs">
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-emoji">🚀</span>
    <div class="card-category">קריירה</div>
    <div class="card-title">מוצאים עבודה בהולנד</div>
    <div class="card-desc">ייעוץ קריירה אפקטיבי לישראלים בהולנד. איך לחפש, איך להתבלט, ואיך לנחות את התפקיד הבא.</div>
    <a href="/jobs" class="card-link">בקרוב <span class="arrow">←</span></a>
  </div>

  <!-- רשת עסקים -->
  <div class="project-card card-business">
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-emoji">🤝</span>
    <div class="card-category">עסקים</div>
    <div class="card-title">Israeli Business Network</div>
    <div class="card-desc">רשת עסקית לישראלים בהולנד. חיבורים, שותפויות, והזדמנויות — בין יזמים ובעלי עסקים בקהילה.</div>
    <a href="/business" class="card-link">בקרוב <span class="arrow">←</span></a>
  </div>

</div>
