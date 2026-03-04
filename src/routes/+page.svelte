<script>
  import { onMount } from 'svelte';

  let activeCard = null;

  const cards = [
    {
      id: 'hayom',
      emoji: '🎯',
      category: 'פעילויות',
      title: 'אז מה עושים היום',
      desc: 'המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש.',
      status: 'live',
      theme: 'card-hayom',
      heroStyle: 'background: linear-gradient(135deg, #21A366 0%, #2ECC71 100%);',
      about: 'הורים לילדים בהולנד שמחפשים מה לעשות בסוף השבוע? בהולנד, שפע של אירועי תרבות ובידור מותאמים לילדים — אבל אין מקום אחד שמאגד אותם. הקבוצה הזאת היא הפתרון.',
      details: [
        { icon: '📅', title: 'כל סוף שבוע', text: 'פוסטים שבועיים עם אירועים לסוף השבוע הקרוב — כדי שתוכלו לתכנן מראש.' },
        { icon: '📍', title: 'מקומי ואמיתי', text: 'לא נמו, לא חיטהורן. אירועים ייחודיים שמתרחשים במקום מסוים ובתאריך ספציפי.' },
        { icon: '👨‍👩‍👧', title: 'לכל המשפחה', text: 'מותאם לילדים בכל הגילאים — ממוזיקה ועד ספורט, מאמנות ועד טבע.' },
      ],
      links: [
        { href: '/hayom', label: 'לאתר הפרויקט', cardLabel: 'לפרויקט', external: false },
        { href: 'https://www.facebook.com/groups/907652610513541', label: 'לקבוצת פייסבוק', cardLabel: 'פייסבוק', external: true },
      ]
    },
    {
      id: 'hitech',
      emoji: '💻',
      category: 'קהילה',
      title: 'הייטקיסטים בהולנד',
      desc: 'הקהילה הגדולה להייטקיסטים ישראלים בהולנד — מ-2019. עזרה במציאת עבודה, טיפים לרילוקיישן, ונטוורקינג.',
      status: 'live',
      theme: 'card-hitech',
      heroStyle: 'background: linear-gradient(135deg, #1B2A4A 0%, #2D4373 100%);',
      about: 'הקהילה הגדולה ביותר של ישראלים בתעשיית ההייטק ההולנדית. אלפי חברים — מהנדסים, מנהלי מוצר, מעצבים, ומייסדים — שמשתפים ידע, עוזרים זה לזה, ובונים ביחד.',
      details: [
        { icon: '🗓️', title: 'פעילה מ-2019', text: 'אחת הקהילות הישראליות הוותיקות והגדולות בהולנד.' },
        { icon: '💼', title: 'מציאת עבודה', text: 'פוסטי משרות, המלצות על חברות, וחיבורים לגיוס.' },
        { icon: '🌍', title: 'רילוקיישן', text: 'טיפים על ויזות, חוזי עבודה, דיור, ומיסים.' },
      ],
      links: [
        { href: 'https://tech.israelis.nl', label: 'לאתר הקהילה', cardLabel: 'לאתר', external: true },
        { href: 'https://www.facebook.com/groups/426953251245124', label: 'לקבוצת פייסבוק', cardLabel: 'פייסבוק', external: true },
      ]
    },
    {
      id: 'nca',
      emoji: '🏐',
      category: 'ספורט',
      title: 'NCA ליגת האמהות',
      desc: 'ליגת האמהות ההולנדית בכדורשת. ספורט, כיף, וקהילה לאמהות ישראליות בהולנד.',
      status: 'live',
      theme: 'card-nca',
      heroStyle: 'background: linear-gradient(135deg, #C0392B 0%, #E74C3C 100%);',
      about: 'ליגת כדורשת לאמהות ישראליות בהולנד. ספורט, תחרות, וחברות — כי אמהות צריכות זמן לעצמן גם.',
      details: [
        { icon: '🏆', title: 'ליגה תחרותית', text: 'עונות משחקים עם קבוצות מרחבי הולנד.' },
        { icon: '🤸', title: 'ללא ניסיון קודם', text: 'פתוח לכל הרמות — מתחילות ועד מנוסות.' },
        { icon: '❤️', title: 'קהילה חברתית', text: 'מעבר לספורט — חברויות ורגעים שנשארים.' },
      ],
      links: [
        { href: '/nca', label: 'לאתר הליגה', cardLabel: 'לליגה', external: false },
      ]
    },
    {
      id: 'papot',
      emoji: '👶',
      category: 'הורות',
      title: 'פאפות אמסטלפיין',
      desc: 'קהילת ההורים הישראלים באמסטלפיין והסביבה. טיפים, מפגשים, ותמיכה הדדית.',
      status: 'live',
      theme: 'card-papot',
      heroStyle: 'background: linear-gradient(135deg, #FF6B35 0%, #FF8C5A 100%);',
      about: 'קהילה של הורים ישראלים שגדלים ביחד באמסטלפיין. שאלות על בתי ספר, גנים, רופאים, ופעילויות — בסביבה מוכרת ובשפה האחת.',
      details: [
        { icon: '🏫', title: 'חינוך ובתי ספר', text: 'המלצות על גנים, בתי ספר, וחינוך בהולנד.' },
        { icon: '🎉', title: 'מפגשים', text: 'ברביקיו, חגים, ואירועים לילדים ביחד.' },
        { icon: '🤝', title: 'תמיכה הדדית', text: 'כי לגדל ילדים בחו"ל עושים ביחד.' },
      ],
      links: [
        { href: '/papot', label: 'לקהילה', cardLabel: 'לקהילה', external: false },
      ]
    },
    {
      id: 'saba',
      emoji: '👴',
      category: 'משפחה',
      title: 'סבא בא',
      desc: 'כשסבא וסבתא באים לביקור בהולנד — המדריך השלם להפוך את הביקור לחוויה בלתי נשכחת.',
      status: 'soon',
      theme: 'card-saba',
      heroStyle: 'background: white; color: #1a1a2e !important;',
      darkHero: true,
      about: 'מדריך מקיף לביקורי הורים מישראל. מה לעשות, לאן ללכת, ואיך לתכנן שבוע שיגרום לסבא וסבתא לרצות לחזור.',
      details: [
        { icon: '🗺️', title: 'מסלולים מוכנים', text: 'תכניות ביקור לפי משך השהייה — יומיים, שבוע, שבועיים.' },
        { icon: '🎠', title: 'עם הנכדים', text: 'פעילויות שמתאימות לסבים עם ילדים קטנים.' },
        { icon: '🍽️', title: 'אוכל ומסעדות', text: 'המלצות על מסעדות שמתאימות לביקורי משפחה.' },
      ],
      links: []
    },
    {
      id: 'holland',
      emoji: '🌷',
      category: 'תיירות',
      title: 'הולנד הקלאסית',
      desc: 'המקומות שכל ישראלי בהולנד חייב להכיר. טחנות רוח, גבינות, ותעלות — הגרסה האותנטית.',
      status: 'soon',
      theme: 'card-holland',
      heroStyle: 'background: #F5E6D3; color: #1a1a2e !important;',
      darkHero: true,
      about: 'מדריך לאטרקציות הקלאסיות של הולנד — לאלה שכבר גרים כאן ורוצים להכיר את המדינה לעומק, ולאורחים שבאים לביקור.',
      details: [
        { icon: '🏗️', title: 'זאנסה סכאנס', text: 'כפר טחנות הרוח ההיסטורי — 20 דקות מאמסטרדם.' },
        { icon: '🧀', title: 'אלכסמאר', text: 'שוק הגבינות המפורסם — ניסיון הולנדי אמיתי.' },
        { icon: '🌿', title: 'חיטהורן', text: 'הכפר ללא כבישים — תעלות, סירות, ושקט מוחלט.' },
      ],
      links: []
    },
    {
      id: 'hagada',
      emoji: '🗺️',
      category: 'תרבות',
      title: 'הגדה על המפה',
      desc: 'מפה אינטראקטיבית של מקומות יהודיים וישראליים בהולנד. היסטוריה, קהילה, וזיכרון.',
      status: 'live',
      theme: 'card-hagada',
      heroStyle: 'background: linear-gradient(135deg, #6C3483 0%, #8E44AD 100%);',
      about: 'מפה אינטראקטיבית שמאגדת בתי כנסת, אנדרטאות שואה, מסעדות כשרות, ומרכזים קהילתיים יהודיים וישראליים בכל הולנד. היסטוריה, הווה, וזיכרון — על מפה אחת.',
      details: [
        { icon: '🕍', title: 'בתי כנסת', text: 'בתי כנסת פעילים והיסטוריים ברחבי הולנד.' },
        { icon: '🕯️', title: 'אנדרטאות זיכרון', text: 'אתרי זיכרון לקהילה היהודית בהולנד.' },
        { icon: '🍽️', title: 'אוכל כשר', text: 'מסעדות ומאפיות כשרות בכל הארץ.' },
      ],
      links: [
        { href: '/hagada', label: 'למפה האינטראקטיבית', cardLabel: 'למפה', external: false },
      ]
    },
    {
      id: 'hatecheck',
      emoji: '🛡️',
      category: 'כלים',
      title: 'HateCheck',
      desc: 'כלי לזיהוי ומעקב אחרי תוכן שנאה ברשת. כי מגיע לנו מרחב מקוון בטוח.',
      status: 'live',
      theme: 'card-hatecheck',
      heroStyle: 'background: #1a1a2e;',
      about: 'כלי המאפשר לדווח, לתעד, ולעקוב אחרי תוכן אנטישמי ותוכן שנאה ברשתות החברתיות. כי מגיע לנו לדעת, ולפעול.',
      details: [
        { icon: '🔍', title: 'זיהוי', text: 'זיהוי תוכן אנטישמי וגזעני ברשתות החברתיות.' },
        { icon: '📊', title: 'מעקב', text: 'תיעוד ומעקב אחרי תוכן שנאה לאורך זמן.' },
        { icon: '📢', title: 'דיווח', text: 'כלים לדיווח לפלטפורמות ולרשויות.' },
      ],
      links: [
        { href: '/hatecheck', label: 'לכלי', cardLabel: 'לכלי', external: false },
      ]
    },
    {
      id: 'jobs',
      emoji: '🚀',
      category: 'קריירה',
      title: 'מוצאים עבודה בהולנד',
      desc: 'ייעוץ קריירה אפקטיבי לישראלים בהולנד. איך לחפש, איך להתבלט, ואיך לנחות את התפקיד הבא.',
      status: 'soon',
      theme: 'card-jobs',
      heroStyle: 'background: linear-gradient(135deg, #0F3460 0%, #16213E 100%);',
      about: 'ייעוץ קריירה מותאם לישראלים שמחפשים עבודה בהולנד. שוק העבודה ההולנדי שונה — נעזור לכם לנווט אותו נכון.',
      details: [
        { icon: '📄', title: 'CV הולנדי', text: 'CV בפורמט המקומי שעובד עם מגייסים הולנדיים.' },
        { icon: '🎯', title: 'הכנה לראיון', text: 'אסטרטגיות וכלים לראיונות בשוק ההולנדי.' },
        { icon: '🔗', title: 'LinkedIn', text: 'אופטימיזציה של הפרופיל למציאת הזדמנויות.' },
      ],
      links: []
    },
    {
      id: 'business',
      emoji: '🤝',
      category: 'עסקים',
      title: 'Israeli Business Network',
      desc: 'רשת עסקית לישראלים בהולנד. חיבורים, שותפויות, והזדמנויות — בין יזמים ובעלי עסקים בקהילה.',
      status: 'soon',
      theme: 'card-business',
      heroStyle: 'background: linear-gradient(135deg, #B7791F 0%, #D69E2E 100%);',
      about: 'רשת לחיבור בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד. שותפויות, לקוחות, וספקים — בתוך הקהילה.',
      details: [
        { icon: '🤝', title: 'חיבורים', text: 'בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד.' },
        { icon: '💡', title: 'שותפויות', text: 'מציאת שותפים עסקיים בתוך הקהילה הישראלית.' },
        { icon: '📈', title: 'הזדמנויות', text: 'לקוחות, ספקים, ומשקיעים בתוך הרשת.' },
      ],
      links: []
    },
  ];

  function openCard(card) {
    activeCard = card;
    document.body.style.overflow = 'hidden';
  }

  function closeCard() {
    activeCard = null;
    document.body.style.overflow = '';
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

    function handleKeydown(e) {
      if (e.key === 'Escape') closeCard();
    }
    window.addEventListener('keydown', handleKeydown);
    return () => window.removeEventListener('keydown', handleKeydown);
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
  {#each cards as card}
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card {card.theme}" on:click={() => openCard(card)}>
    <span class="card-status {card.status === 'live' ? 'status-live' : 'status-soon'}">{card.status === 'live' ? 'פעיל' : 'בקרוב'}</span>
    <span class="card-toggle">+</span>
    <span class="card-emoji">{card.emoji}</span>
    <div class="card-category">{card.category}</div>
    <div class="card-title">{card.title}</div>
    <div class="card-desc">{card.desc}</div>
    <div style="display:flex; gap:0.8rem; margin-top:1.2rem; flex-wrap:wrap;">
      {#each card.links as link}
      <a href={link.href} class="card-link" style="margin-top:0;"
         target={link.external ? '_blank' : '_self'}
         rel={link.external ? 'noopener' : ''}
         on:click|stopPropagation>
        {link.cardLabel} <span class="arrow">←</span>
      </a>
      {/each}
    </div>
  </div>
  {/each}
</div>

<!-- FULL-SCREEN MODAL -->
{#if activeCard}
<!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
<div class="modal-backdrop" on:click={closeCard}>
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="modal-card" on:click|stopPropagation>

    <div class="modal-hero" style={activeCard.heroStyle}>
      <button class="modal-close" class:dark={activeCard.darkHero} on:click={closeCard}>×</button>
      <span class="modal-status {activeCard.status === 'live' ? 'status-live' : 'status-soon'}" class:dark-badge={activeCard.darkHero}>
        {activeCard.status === 'live' ? 'פעיל' : 'בקרוב'}
      </span>
      <span class="modal-emoji">{activeCard.emoji}</span>
      <div class="modal-category" class:dark-text={activeCard.darkHero}>{activeCard.category}</div>
      <h2 class="modal-title" class:dark-text={activeCard.darkHero}>{activeCard.title}</h2>
      <p class="modal-desc" class:dark-text={activeCard.darkHero}>{activeCard.desc}</p>
    </div>

    <div class="modal-body">
      <p class="modal-about">{activeCard.about}</p>

      <div class="modal-details">
        {#each activeCard.details as detail}
        <div class="modal-detail-item">
          <span class="detail-icon">{detail.icon}</span>
          <div>
            <strong>{detail.title}</strong>
            <p>{detail.text}</p>
          </div>
        </div>
        {/each}
      </div>

      {#if activeCard.links.length > 0}
      <div class="modal-links">
        {#each activeCard.links as link, i}
        <a href={link.href} class="modal-link-btn" class:secondary={i > 0}
           target={link.external ? '_blank' : '_self'}
           rel={link.external ? 'noopener' : ''}>
          {link.label} <span>←</span>
        </a>
        {/each}
      </div>
      {:else}
      <p class="modal-coming-soon">🔜 בקרוב — עקבו אחרינו לעדכונים</p>
      {/if}
    </div>

  </div>
</div>
{/if}

<style>
  /* ===== MODAL ===== */
  .modal-backdrop {
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.65);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    z-index: 1000;
    display: flex; align-items: center; justify-content: center;
    padding: 1rem;
    animation: backdropIn 0.2s ease;
  }
  @keyframes backdropIn { from { opacity: 0; } to { opacity: 1; } }

  .modal-card {
    background: white;
    border-radius: 24px;
    max-width: 640px; width: 100%;
    max-height: 90vh;
    overflow-y: auto;
    position: relative;
    animation: modalUp 0.35s cubic-bezier(0.23,1,0.32,1);
    box-shadow: 0 40px 120px rgba(0,0,0,0.35);
  }
  @keyframes modalUp {
    from { transform: translateY(40px) scale(0.97); opacity: 0; }
    to   { transform: translateY(0) scale(1); opacity: 1; }
  }

  .modal-hero {
    padding: 3rem 2rem 2.5rem;
    border-radius: 24px 24px 0 0;
    color: white;
    position: relative;
  }

  .modal-close {
    position: absolute; top: 1rem; left: 1rem;
    width: 36px; height: 36px;
    border: none; border-radius: 50%;
    background: rgba(255,255,255,0.2);
    color: white; font-size: 1.4rem; line-height: 1;
    cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: background 0.2s, transform 0.2s;
    padding: 0;
  }
  .modal-close:hover { background: rgba(255,255,255,0.35); transform: scale(1.1); }
  .modal-close.dark { background: rgba(0,0,0,0.08); color: #1a1a2e; }
  .modal-close.dark:hover { background: rgba(0,0,0,0.15); }

  .modal-status {
    display: inline-block; font-size: 0.7rem; font-weight: 700;
    padding: 0.25em 0.75em; border-radius: 100px;
    text-transform: uppercase; letter-spacing: 0.05em;
    margin-bottom: 1.2rem;
    background: rgba(33,163,102,0.25); color: #2ECC71;
  }
  .modal-status.status-soon { background: rgba(255,255,255,0.15); color: rgba(255,255,255,0.7); }
  .modal-status.dark-badge { background: rgba(0,0,0,0.08); color: rgba(0,0,0,0.5); }

  .modal-emoji { font-size: 3.5rem; display: block; margin-bottom: 0.8rem; }

  .modal-category {
    font-size: 0.75rem; font-weight: 700;
    text-transform: uppercase; letter-spacing: 0.1em;
    opacity: 0.65; margin-bottom: 0.4rem;
    color: white;
  }
  .modal-title {
    font-family: 'Secular One', sans-serif;
    font-size: clamp(1.8rem, 4vw, 2.4rem);
    line-height: 1.2; margin-bottom: 0.8rem;
    color: white;
  }
  .modal-desc {
    font-size: 1rem; opacity: 0.85; line-height: 1.65;
    color: white;
  }
  .dark-text { color: #1a1a2e !important; }

  .modal-body { padding: 2rem 2rem 2.5rem; }

  .modal-about {
    font-size: 1rem; line-height: 1.75; color: #5a5a7a;
    margin-bottom: 1.8rem;
    padding-bottom: 1.8rem;
    border-bottom: 1px solid rgba(0,0,0,0.07);
  }

  .modal-details { display: flex; flex-direction: column; gap: 1.2rem; margin-bottom: 2rem; }
  .modal-detail-item { display: flex; align-items: flex-start; gap: 1rem; }
  .detail-icon { font-size: 1.6rem; flex-shrink: 0; }
  .modal-detail-item strong {
    font-size: 0.95rem; color: #1a1a2e;
    display: block; margin-bottom: 0.2rem;
  }
  .modal-detail-item p { font-size: 0.9rem; color: #5a5a7a; margin: 0; line-height: 1.55; }

  .modal-links { display: flex; gap: 0.8rem; flex-wrap: wrap; }
  .modal-link-btn {
    display: inline-flex; align-items: center; gap: 0.5rem;
    padding: 0.75rem 1.5rem; border-radius: 50px;
    font-weight: 700; font-size: 0.9rem;
    text-decoration: none;
    background: #1B2A4A; color: white;
    transition: transform 0.2s, box-shadow 0.2s;
    box-shadow: 0 4px 14px rgba(0,0,0,0.15);
  }
  .modal-link-btn.secondary {
    background: transparent; color: #5a5a7a;
    box-shadow: none; border: 1.5px solid rgba(0,0,0,0.15);
  }
  .modal-link-btn:hover { transform: translateY(-2px); box-shadow: 0 8px 22px rgba(0,0,0,0.2); }
  .modal-link-btn.secondary:hover { background: rgba(0,0,0,0.04); }

  .modal-coming-soon {
    font-size: 0.95rem; color: #5a5a7a;
    background: rgba(0,0,0,0.04);
    padding: 1rem 1.2rem; border-radius: 12px;
    margin: 0;
  }

  @media (max-width: 600px) {
    .modal-card { border-radius: 20px 20px 0 0; max-height: 95vh; }
    .modal-backdrop { align-items: flex-end; padding: 0; }
    .modal-hero { padding: 2.5rem 1.5rem 2rem; }
    .modal-body { padding: 1.5rem 1.5rem 2rem; }
  }
</style>
