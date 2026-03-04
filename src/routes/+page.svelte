<script>
  import { onMount } from 'svelte';

  let activeCard = null;

  function toggleCard(id) {
    activeCard = activeCard === id ? null : id;
  }

  onMount(() => {
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
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-hayom" class:expanded={activeCard === 'hayom'} on:click={() => toggleCard('hayom')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle">{activeCard === 'hayom' ? '×' : '+'}</span>
    <span class="card-emoji">🎯</span>
    <div class="card-category">פעילויות</div>
    <div class="card-title">אז מה עושים היום</div>
    <div class="card-desc">המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש. כי גוגל לא יספר לך מה קורה השבת ליד הבית.</div>
    <div style="display:flex; gap:1rem; margin-top:1.2rem; flex-wrap:wrap;">
      <a href="/hayom" class="card-link" style="margin-top:0;" on:click|stopPropagation>לפרויקט <span class="arrow">←</span></a>
      <a href="https://www.facebook.com/groups/907652610513541" class="card-link" style="margin-top:0; opacity:0.7;" target="_blank" rel="noopener" on:click|stopPropagation>פייסבוק <span class="arrow">←</span></a>
    </div>
    {#if activeCard === 'hayom'}
    <div class="card-more">
      <p>קבוצת פייסבוק עם המלצות שבועיות לאירועים מקומיים — ייחודיים, חד-פעמיים, ומותאמים לילדים. לא אטרקציות כלליות, אלא מה שקורה <em>השבת</em> ליד הבית.</p>
      <p>אם אתם מחפשים מה לעשות הסוף שבוע — זה המקום.</p>
    </div>
    {/if}
  </div>

  <!-- הייטקיסטים בהולנד -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-hitech" class:expanded={activeCard === 'hitech'} on:click={() => toggleCard('hitech')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle">{activeCard === 'hitech' ? '×' : '+'}</span>
    <span class="card-emoji">💻</span>
    <div class="card-category">קהילה</div>
    <div class="card-title">הייטקיסטים בהולנד</div>
    <div class="card-desc">הקהילה הגדולה להייטקיסטים ישראלים בהולנד — מ-2019. עזרה במציאת עבודה, טיפים לרילוקיישן, ונטוורקינג.</div>
    <div style="display:flex; gap:1rem; margin-top:1.2rem; flex-wrap:wrap;">
      <a href="https://tech.israelis.nl" class="card-link" style="margin-top:0;" on:click|stopPropagation>לאתר <span class="arrow">←</span></a>
      <a href="https://www.facebook.com/groups/426953251245124" class="card-link" style="margin-top:0; opacity:0.7;" target="_blank" rel="noopener" on:click|stopPropagation>פייסבוק <span class="arrow">←</span></a>
    </div>
    {#if activeCard === 'hitech'}
    <div class="card-more">
      <p>הקהילה הגדולה ביותר של הייטקיסטים ישראלים בהולנד, פעילה מאז 2019. אלפי חברים — מהנדסים, מנהלי מוצר, מעצבים, ומייסדים.</p>
      <p>מציאת עבודה, שאלות על ויזה וחוזים, המלצות על חברות, ונטוורקינג — הכל במקום אחד.</p>
    </div>
    {/if}
  </div>

  <!-- NCA ליגת האמהות -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-nca" class:expanded={activeCard === 'nca'} on:click={() => toggleCard('nca')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle">{activeCard === 'nca' ? '×' : '+'}</span>
    <span class="card-emoji">🏐</span>
    <div class="card-category">ספורט</div>
    <div class="card-title">NCA ליגת האמהות</div>
    <div class="card-desc">ליגת האמהות ההולנדית בכדורשת. ספורט, כיף, וקהילה לאמהות ישראליות בהולנד.</div>
    <a href="/nca" class="card-link" on:click|stopPropagation>לליגה <span class="arrow">←</span></a>
    {#if activeCard === 'nca'}
    <div class="card-more">
      <p>ליגת כדורשת לאמהות ישראליות בהולנד — עונות משחקים, אימונים, וקהילה חברתית חמה.</p>
      <p>לא צריך ניסיון קודם. רק רצון לזוז, להתחרות, ולהכיר אמהות מדהימות.</p>
    </div>
    {/if}
  </div>

  <!-- פאפות אמסטלפיין -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-papot" class:expanded={activeCard === 'papot'} on:click={() => toggleCard('papot')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle" style="color: var(--blue-deep);">{activeCard === 'papot' ? '×' : '+'}</span>
    <span class="card-emoji">👶</span>
    <div class="card-category">הורות</div>
    <div class="card-title">פאפות אמסטלפיין</div>
    <div class="card-desc">קהילת ההורים הישראלים באמסטלפיין והסביבה. טיפים, מפגשים, ותמיכה הדדית.</div>
    <a href="/papot" class="card-link" on:click|stopPropagation>לקהילה <span class="arrow">←</span></a>
    {#if activeCard === 'papot'}
    <div class="card-more" style="border-top-color: rgba(27,42,74,0.2); color: var(--blue-deep);">
      <p>קהילה של הורים ישראלים שגדלים ביחד באמסטלפיין. שאלות על בתי ספר, גנים, רופאים, ופעילויות לילדים.</p>
      <p>מפגשים, ברביקיו, וחגים ביחד — כי לגדל ילדים בחו"ל עושים ביחד.</p>
    </div>
    {/if}
  </div>

  <!-- סבא בא -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-saba" class:expanded={activeCard === 'saba'} on:click={() => toggleCard('saba')}>
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-toggle" style="color: var(--text-dark); background: rgba(0,0,0,0.06);">{activeCard === 'saba' ? '×' : '+'}</span>
    <span class="card-emoji">👴</span>
    <div class="card-category">משפחה</div>
    <div class="card-title">סבא בא</div>
    <div class="card-desc">כשסבא וסבתא באים לביקור בהולנד — המדריך השלם להפוך את הביקור לחוויה בלתי נשכחת.</div>
    <a href="/saba" class="card-link" on:click|stopPropagation>בקרוב <span class="arrow">←</span></a>
    {#if activeCard === 'saba'}
    <div class="card-more" style="border-top-color: rgba(0,0,0,0.08); color: var(--text-light);">
      <p>מדריך מקיף לביקורי הורים מישראל — מה לעשות, לאן ללכת, ואיך לתכנן שבוע מושלם שיגרום לסבא וסבתא לרצות לחזור.</p>
      <p>בקרוב — נשמח לשמוע מה אתם רוצים לראות!</p>
    </div>
    {/if}
  </div>

  <!-- הולנד הקלאסית -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-holland" class:expanded={activeCard === 'holland'} on:click={() => toggleCard('holland')}>
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-toggle" style="color: var(--text-dark); background: rgba(0,0,0,0.06);">{activeCard === 'holland' ? '×' : '+'}</span>
    <span class="card-emoji">🌷</span>
    <div class="card-category">תיירות</div>
    <div class="card-title">הולנד הקלאסית</div>
    <div class="card-desc">המקומות שכל ישראלי בהולנד חייב להכיר. טחנות רוח, גבינות, ותעלות — הגרסה האותנטית.</div>
    <a href="/holland" class="card-link" on:click|stopPropagation>בקרוב <span class="arrow">←</span></a>
    {#if activeCard === 'holland'}
    <div class="card-more" style="border-top-color: rgba(0,0,0,0.08); color: var(--text-light);">
      <p>מדריך האורחים הקלאסי — אלכסמאר, זאנסה סכאנס, חיטהורן, כפר המלוחים בפולנדום. מסלולים מוכנים לסופי שבוע.</p>
      <p>בקרוב.</p>
    </div>
    {/if}
  </div>

  <!-- הגדה על המפה -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-hagada" class:expanded={activeCard === 'hagada'} on:click={() => toggleCard('hagada')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle">{activeCard === 'hagada' ? '×' : '+'}</span>
    <span class="card-emoji">🗺️</span>
    <div class="card-category">תרבות</div>
    <div class="card-title">הגדה על המפה</div>
    <div class="card-desc">מפה אינטראקטיבית של מקומות יהודיים וישראליים בהולנד. היסטוריה, קהילה, וזיכרון.</div>
    <a href="/hagada" class="card-link" on:click|stopPropagation>למפה <span class="arrow">←</span></a>
    {#if activeCard === 'hagada'}
    <div class="card-more">
      <p>מפה אינטראקטיבית שמאגדת בתי כנסת, אנדרטאות שואה, מסעדות כשרות, ומרכזים קהילתיים יהודיים וישראליים בכל הולנד.</p>
      <p>היסטוריה, הווה, וקהילה — על מפה אחת.</p>
    </div>
    {/if}
  </div>

  <!-- HateCheck -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-hatecheck" class:expanded={activeCard === 'hatecheck'} on:click={() => toggleCard('hatecheck')}>
    <span class="card-status status-live">פעיל</span>
    <span class="card-toggle">{activeCard === 'hatecheck' ? '×' : '+'}</span>
    <span class="card-emoji">🛡️</span>
    <div class="card-category">כלים</div>
    <div class="card-title">HateCheck</div>
    <div class="card-desc">כלי לזיהוי ומעקב אחרי תוכן שנאה ברשת. כי מגיע לנו מרחב מקוון בטוח.</div>
    <a href="/hatecheck" class="card-link" on:click|stopPropagation>לכלי <span class="arrow">←</span></a>
    {#if activeCard === 'hatecheck'}
    <div class="card-more">
      <p>כלי המאפשר לדווח, לתעד, ולעקוב אחרי תוכן אנטישמי ותוכן שנאה ברשתות החברתיות.</p>
      <p>כי מגיע לנו לדעת, ולפעול.</p>
    </div>
    {/if}
  </div>

  <!-- מוצאים עבודה בהולנד -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-jobs" class:expanded={activeCard === 'jobs'} on:click={() => toggleCard('jobs')}>
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-toggle">{activeCard === 'jobs' ? '×' : '+'}</span>
    <span class="card-emoji">🚀</span>
    <div class="card-category">קריירה</div>
    <div class="card-title">מוצאים עבודה בהולנד</div>
    <div class="card-desc">ייעוץ קריירה אפקטיבי לישראלים בהולנד. איך לחפש, איך להתבלט, ואיך לנחות את התפקיד הבא.</div>
    <a href="/jobs" class="card-link" on:click|stopPropagation>בקרוב <span class="arrow">←</span></a>
    {#if activeCard === 'jobs'}
    <div class="card-more">
      <p>CV בפורמט ההולנדי, הכנה לראיונות, אופטימיזציה של LinkedIn, וניווט בשוק העבודה המקומי.</p>
      <p>ייעוץ אישי ומשאבים מותאמים לישראלים שמחפשים את הצעד הבא בקריירה שלהם בהולנד.</p>
    </div>
    {/if}
  </div>

  <!-- רשת עסקים -->
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card card-business" class:expanded={activeCard === 'business'} on:click={() => toggleCard('business')}>
    <span class="card-status status-soon">בקרוב</span>
    <span class="card-toggle" style="color: white; background: rgba(255,255,255,0.2);">{activeCard === 'business' ? '×' : '+'}</span>
    <span class="card-emoji">🤝</span>
    <div class="card-category">עסקים</div>
    <div class="card-title">Israeli Business Network</div>
    <div class="card-desc">רשת עסקית לישראלים בהולנד. חיבורים, שותפויות, והזדמנויות — בין יזמים ובעלי עסקים בקהילה.</div>
    <a href="/business" class="card-link" on:click|stopPropagation>בקרוב <span class="arrow">←</span></a>
    {#if activeCard === 'business'}
    <div class="card-more" style="border-top-color: rgba(255,255,255,0.2); color: rgba(255,255,255,0.9);">
      <p>רשת לחיבור בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד. שותפויות, לקוחות, וספקים — בתוך הקהילה.</p>
      <p>בקרוב — אם אתם מעוניינים להיות חלק מהרשת הראשונה, השאירו פרטים.</p>
    </div>
    {/if}
  </div>

</div>
