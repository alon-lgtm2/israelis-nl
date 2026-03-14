<script>
  import { onMount, tick } from 'svelte';

  let activeCard = null;
  let formValues = {};
  let formStatus = {};
  let googleAutoFilled = {};

  const GOOGLE_CLIENT_ID = '225623142977-oqtmjp8lr7kt3n2b53ftl37801391021.apps.googleusercontent.com';
  const WEB3FORMS_KEY = '4fbcbbf1-0fe6-4f59-986d-618f42b0df80';

  function loadGsi() {
    return new Promise((resolve) => {
      if (window.google?.accounts?.id) { resolve(); return; }
      const script = document.createElement('script');
      script.src = 'https://accounts.google.com/gsi/client';
      script.async = true; script.defer = true;
      script.onload = resolve;
      document.head.appendChild(script);
    });
  }

  async function initGoogleSignIn(cardId) {
    await loadGsi();
    window.google.accounts.id.initialize({
      client_id: GOOGLE_CLIENT_ID,
      callback: async (response) => {
        try {
          const base64 = response.credential.split('.')[1].replace(/-/g, '+').replace(/_/g, '/');
          const payload = JSON.parse(atob(base64));
          const card = cards.find(c => c.id === cardId);
          formStatus = { ...formStatus, [cardId]: 'loading' };
          const res = await fetch('https://api.web3forms.com/submit', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
            body: JSON.stringify({
              access_key: WEB3FORMS_KEY,
              subject: `${card?.title || 'israelis.nl'} — נרשם חדש: ${payload.name}`,
              name: payload.name || '',
              email: payload.email || '',
              given_name: payload.given_name || '',
              family_name: payload.family_name || '',
              email_verified: payload.email_verified ? 'כן' : 'לא',
              google_id: payload.sub || '',
              profile_picture: payload.picture || '',
              locale: payload.locale || '',
              source: card?.title || 'israelis.nl',
            })
          });
          formStatus = { ...formStatus, [cardId]: res.ok ? 'success' : 'error' };
        } catch (e) {
          console.error('Google sign-in error', e);
          formStatus = { ...formStatus, [cardId]: 'error' };
        }
      },
    });
    const container = document.getElementById(`google-signin-btn-${cardId}`);
    if (container) {
      window.google.accounts.id.renderButton(container, {
        theme: 'outline', size: 'large',
        text: 'signin_with', shape: 'pill',
        width: container.offsetWidth || 280,
      });
    }
  }

  const cards = [
    {
      id: 'elect',
      emoji: '🗳️',
      category: 'בחירות',
      tag: 'חדש',
      title: 'בחירות מוניציפליות 2026',
      desc: 'הבחירות המוניציפליות בהולנד מגיעות. כל מה שצריך לדעת כדי להצביע — בעברית.',
      status: 'live',
      theme: 'card-elect',
      heroStyle: 'background: linear-gradient(135deg, #154B8C 0%, #1E6EC2 100%);',
      about: 'הבחירות לרשויות המקומיות בהולנד הן ההזדמנות שלנו להשפיע. אם יש לכם אזרחות הולנדית — יש לכם קול. המדריך הזה מסביר הכל בעברית, צעד אחר צעד.',
      details: [
        { icon: '📋', title: 'מי יכול להצביע?', text: 'אזרחים הולנדים ותושבי EU הרשומים בעירייה — כולל ישראלים עם אזרחות כפולה.' },
        { icon: '📅', title: 'מתי?', text: 'בחירות מוניציפליות 2026. המדריך כולל את כל הצעדים לפני יום ההצבעה.' },
        { icon: '🗺️', title: 'איך מצביעים?', text: 'כרטיס הצבעה, מרכז הצבעה, ואיך ממלאים את הפתק — שלב אחר שלב.' },
      ],
      links: [
        { href: '/elect', label: 'למדריך הבחירות ←', cardLabel: 'למדריך', external: false },
      ]
    },
    {
      id: 'hayom',
      emoji: '🎯',
      category: 'פעילויות',
      tag: 'פנאי',
      title: 'אז מה עושים היום',
      desc: 'המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש.',
      status: 'live',
      theme: 'card-hayom',
      heroStyle: 'background: linear-gradient(135deg, #7B2FBE 0%, #C850C0 50%, #9B59B6 100%);',
      banner: '/hayom-banner.jpeg',
      about: 'הורים לילדים בהולנד שמחפשים מה לעשות בסוף השבוע? בהולנד, שפע של אירועי תרבות ובידור מותאמים לילדים — אבל אין מקום אחד שמאגד אותם. הקבוצה הזאת היא הפתרון.',
      details: [
        { icon: '📅', title: 'כל סוף שבוע', text: 'פוסטים שבועיים עם אירועים לסוף השבוע הקרוב — כדי שתוכלו לתכנן מראש.' },
        { icon: '📍', title: 'מקומי ואמיתי', text: 'לא נמו, לא חיטהורן. אירועים ייחודיים שמתרחשים במקום מסוים ובתאריך ספציפי.' },
        { icon: '👨‍👩‍👧', title: 'לכל המשפחה', text: 'מותאם לילדים בכל הגילאים — ממוזיקה ועד ספורט, מאמנות ועד טבע.' },
      ],
      links: [
        { href: 'https://www.facebook.com/groups/907652610513541', label: 'קחו אותי לשם! ←', cardLabel: 'לפעילויות', external: true },
      ]
    },
    {
      id: 'jobs',
      emoji: '🚀',
      category: 'קריירה',
      tag: 'חדש',
      title: 'מוצאים עבודה בהולנד',
      desc: 'ייעוץ קריירה אפקטיבי לישראלים בהולנד. איך לחפש, איך להתבלט, ואיך לנחות את התפקיד הבא.',
      status: 'live',
      theme: 'card-jobs',
      heroStyle: 'background: linear-gradient(135deg, #0F3460 0%, #16213E 100%);',
      about: 'ייעוץ קריירה מותאם לישראלים שמחפשים עבודה בהולנד. שוק העבודה ההולנדי שונה — נעזור לכם לנווט אותו נכון.',
      details: [
        { icon: '📄', title: 'CV הולנדי', text: 'CV בפורמט המקומי שעובד עם מגייסים הולנדיים.' },
        { icon: '🎯', title: 'הכנה לראיון', text: 'אסטרטגיות וכלים לראיונות בשוק ההולנדי.' },
        { icon: '🔗', title: 'LinkedIn', text: 'אופטימיזציה של הפרופיל למציאת הזדמנויות.' },
      ],
      links: [
        { href: 'https://jobs.israelis.nl/', label: 'למציאת עבודה ←', cardLabel: 'לאתר', external: true },
      ]
    },
    {
      id: 'hitech',
      emoji: '💻',
      category: 'טק וחדשנות',
      tag: 'קהילה מקצועית',
      title: 'הייטקיסטים בהולנד',
      desc: 'הנטוורק המקצועי המוביל באירופה לקהילת הטק והחדשנות הישראלית בהולנד.',
      status: 'live',
      theme: 'card-hitech',
      heroStyle: 'background: #0a0a0a;',
      banner: '/hitech-banner.jpeg',
      about: 'הבית המקצועי של קהילת הטק והחדשנות הישראלית בהולנד. הקבוצה מהווה צומת מרכזי ליזמים, מנהלים, משקיעים ואנשי פיתוח — ומטרתה לייצר נטוורקינג איכותי, שיתוף ידע אסטרטגי, והזדמנויות צמיחה בתעשייה המקומית.',
      details: [
        { icon: '🧠', title: 'ידע ושוק', text: 'דיונים מקצועיים, תובנות ותחזיות, וניתוחי שוק עדכניים על האקוסיסטם ההולנדי.' },
        { icon: '📋', title: 'לוח משרות ממוקד', text: 'פוזיציות High-Tech ו-Corporate בהולנד ובאירופה — ישירות מחברות ומגייסים בתוך הקהילה.' },
        { icon: '🤝', title: 'חיבורים עסקיים', text: 'נטוורקינג, שיתופי פעולה, והיכרויות בין יזמים, מנהלים ומשקיעים שמייצרות הזדמנויות אמיתיות.' },
      ],
      note: 'הקהילה שומרת על שיח מקצועי ואיכותי. פרסום מסחרי וקידום עצמי מתאפשרים במסגרת שיתופי פעולה רשמיים בלבד — לפרטים, פנו לאדמינים.',
      links: [
        { href: 'https://www.facebook.com/groups/426953251245124', label: 'הצטרפו לקהילה ←', cardLabel: 'לקהילה', external: true },
      ]
    },
    {
      id: 'nca',
      emoji: '🏐',
      category: 'ספורט',
      tag: 'ספורט',
      title: 'NCA ליגת האמהות',
      desc: 'ליגת האמהות ההולנדית בכדורשת. ספורט, כיף, וקהילה לאמהות ישראליות בהולנד.',
      status: 'soon',
      theme: 'card-nca',
      heroStyle: 'background: linear-gradient(135deg, #C0392B 0%, #E74C3C 100%);',
      about: 'ליגת כדורשת לאמהות ישראליות בהולנד. ספורט, תחרות, וחברות — כי אמהות צריכות זמן לעצמן גם.',
      details: [
        { icon: '🏆', title: 'ליגה תחרותית', text: 'עונות משחקים עם קבוצות מרחבי הולנד.' },
        { icon: '🤸', title: 'ללא ניסיון קודם', text: 'פתוח לכל הרמות — מתחילות ועד מנוסות.' },
        { icon: '❤️', title: 'קהילה חברתית', text: 'מעבר לספורט — חברויות ורגעים שנשארים.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'רוצה פרטים נוספים? שוקלת להצטרף? לחצי מטה ונהיה איתך בקשר בהקדם!',
      ctaLabel: 'לפרטים נוספים',
      links: []
    },
    {
      id: 'papot',
      emoji: '🍺',
      category: 'הורות',
      tag: 'קהילה',
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
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל להצטרפות',
      ctaLabel: 'להצטרפות',
      links: []
    },
    {
      id: 'saba',
      emoji: '👴',
      category: 'משפחה',
      tag: 'קהילה',
      title: 'סבא בא',
      desc: 'ההורים נוחתים בשבוע הבא? קהילה לישראלים שמארחים הורים מבוגרים לביקור ממושך בהולנד — טיפים, מסלולים, ועצות מהשטח.',
      status: 'live',
      theme: 'card-saba',
      heroStyle: 'background: white; color: #1a1a2e !important;',
      darkHero: true,
      about: 'מקום לישראלים שמארחים הורים מבוגרים לשבועות ארוכים בהולנד. מה לעשות עם סבא כשגמרו את אמסטרדם? לאן לקחת סבתא שמתקשה ללכת? איך מסבירים לרופא אילנית מה כואב לה? כאן תמצאו אנשים שכבר עברו את זה — ויודעים.',
      details: [
        { icon: '🗺️', title: 'מסלולים לפי משך השהייה', text: 'תכניות ביקור לשבוע, שבועיים, ויותר — כולל אופציות לכאלה שמתקשים בהליכה ממושכת.' },
        { icon: '🏥', title: 'בריאות ובירוקרטיה', text: 'רופאים, תרופות, ביטוחי נסיעות — כל מה שצריך לדעת לפני ובמהלך הביקור.' },
        { icon: '🍽️', title: 'המלצות מהשטח', text: 'מסעדות, פעילויות, וטיפים שעובדים — ממשפחות שכבר עשו את זה.' },
      ],
      hasForm: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      useGoogleSignIn: true,
      ctaLabel: 'לפרטים נוספים',
      links: []
    },
    {
      id: 'holland',
      emoji: '🎻',
      category: 'מוזיקה',
      tag: 'WhatsApp',
      title: 'הולנד הקלאסית',
      desc: 'קבוצת וואטסאפ לחובבי מוזיקה קלאסית בהולנד — קונצרטים, המלצות, ושיח מוזיקלי.',
      status: 'live',
      theme: 'card-holland',
      heroStyle: 'background: #F5E6D3; color: #1a1a2e !important;',
      darkHero: true,
      about: 'קהילה ייעודית לחובבי מוזיקה קלאסית בהולנד. קונצרטים של Concertgebouw ופילהרמונית רוטרדם, המלצות על אלבומים, ושיח מוזיקלי — הכל בעברית.',
      details: [
        { icon: '🎼', title: 'קונצרטים', text: 'עדכונים על הופעות ותזמורות מובילות בהולנד — כולל Concertgebouw.' },
        { icon: '🎧', title: 'המלצות מוזיקה', text: 'שיתוף אהבות, תגליות, ויצירות שכדאי להכיר.' },
        { icon: '🎹', title: 'קהילה מוזיקלית', text: 'חיבור בין מוזיקאים, מורים, ואוהבי מוזיקה קלאסית.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל להצטרפות',
      ctaLabel: 'להצטרפות',
      links: []
    },
    {
      id: 'hagada',
      emoji: '🍷',
      category: 'חגים',
      tag: 'פסח',
      title: 'הגדה על המפה',
      desc: 'מחפשים סדר פסח בהולנד? מפה אינטראקטיבית של סדרי פסח ציבוריים, קהילות, ואירועי חג ברחבי הארץ.',
      status: 'live',
      theme: 'card-hagada',
      heroStyle: 'background: linear-gradient(135deg, #6C3483 0%, #8E44AD 100%);',
      about: 'בהשראת sedermap.com — מפה שמאגדת סדרי פסח ציבוריים, אירועי חג, וקהילות ישראליות וחבד בהולנד. בין אם אתם מחפשים סדר פתוח, רוצים לארגן אחד, או פשוט רוצים לדעת מה קורה ליד הבית — הכל כאן.',
      details: [
        { icon: '🕍', title: 'סדרי פסח ציבוריים', text: 'סדרי פסח פתוחים מקהילות, בתי כנסת, וחבד ברחבי הולנד.' },
        { icon: '📍', title: 'לפי מיקום', text: 'מצאו אירועי חג קרובים אליכם — באמסטרדם, רוטרדם, האג ובכל הארץ.' },
        { icon: '🍽️', title: 'הוסיפו את שלכם', text: 'מארגנים סדר פתוח? הוסיפו אותו למפה ובואו חברים.' },
      ],
      links: [
        { href: 'https://sedermap.com', label: 'למפה האינטראקטיבית ←', cardLabel: 'למפה', external: true },
      ]
    },
    {
      id: 'hatecheck',
      emoji: '🛡️',
      category: 'מאבק באנטישמיות',
      tag: 'AI',
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
      id: 'nifgashim',
      emoji: '☕',
      category: 'הגיל השלישי',
      tag: 'קהילה',
      title: 'נפגשים בעברית',
      desc: 'קהילה לישראלים בגיל 60 ומעלה המתגוררים בהולנד — שיח בעברית, מפגשים חברתיים, ועזרה הדדית.',
      status: 'live',
      theme: 'card-nifgashim',
      heroStyle: 'background: linear-gradient(135deg, #16705F 0%, #1DAA8A 100%);',
      about: 'קהילה לישראלים בגיל 60 ומעלה שגרים בהולנד. מקום לשיח בעברית, מפגשים חברתיים, ועזרה הדדית — כי גיל שלישי בחו"ל הוא גם הרפתקה.',
      details: [
        { icon: '🗣️', title: 'שיח בעברית', text: 'שיחות, שאלות, והמלצות — בעברית, בין ישראלים.' },
        { icon: '☕', title: 'מפגשים חברתיים', text: 'קפה, הליכות, ואירועים לגיל השלישי ברחבי הולנד.' },
        { icon: '🤝', title: 'עזרה הדדית', text: 'שאלות על רפואה, בירוקרטיה, וחיי יומיום — ביחד יותר קל.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      ctaLabel: 'לפרטים נוספים',
      links: []
    },
    {
      id: 'business',
      emoji: '🤝',
      category: 'עסקים',
      tag: 'רשת',
      title: 'Israeli Business Network',
      desc: 'רשת עסקית לישראלים בהולנד. חיבורים, שותפויות, והזדמנויות — בין יזמים ובעלי עסקים בקהילה.',
      status: 'live',
      theme: 'card-business',
      heroStyle: 'background: linear-gradient(135deg, #B7791F 0%, #D69E2E 100%);',
      about: 'רשת לחיבור בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד. שותפויות, לקוחות, וספקים — בתוך הקהילה.',
      details: [
        { icon: '🤝', title: 'חיבורים', text: 'בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד.' },
        { icon: '💡', title: 'שותפויות', text: 'מציאת שותפים עסקיים בתוך הקהילה הישראלית.' },
        { icon: '📈', title: 'הזדמנויות', text: 'לקוחות, ספקים, ומשקיעים בתוך הרשת.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      ctaLabel: 'לפרטים נוספים',
      links: []
    },
  ];

  function openCard(card) {
    activeCard = card;
    if (card.hasForm && !formValues[card.id]) {
      const fields = card.formFields || ['name', 'phone'];
      const init = {};
      fields.forEach(f => (init[f] = ''));
      formValues[card.id] = init;
    }
    document.body.style.overflow = 'hidden';
    if (card.useGoogleSignIn) {
      tick().then(() => initGoogleSignIn(card.id));
    }
  }

  function closeCard() {
    activeCard = null;
    document.body.style.overflow = '';
  }

  async function submitForm(card) {
    const data = formValues[card.id];
    const fields = card.formFields || ['name', 'phone'];
    if (fields.some(f => !data?.[f])) return;
    formStatus = { ...formStatus, [card.id]: 'loading' };
    const payload = { ...data, _subject: `${card.formLabel || 'בקשת הצטרפות'} — ${card.title}` };
    try {
      const res = await fetch(`https://formspree.io/f/${card.formId}`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
        body: JSON.stringify(payload)
      });
      formStatus = { ...formStatus, [card.id]: res.ok ? 'success' : 'error' };
    } catch {
      formStatus = { ...formStatus, [card.id]: 'error' };
    }
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
  <title>israelis.nl — ישראלים: בהולנד</title>
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
    <p class="hero-tagline">ישראלים: בהולנד</p>
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
    <span class="card-status {card.status === 'live' ? 'status-live' : 'status-soon'}">
      {card.status === 'live' ? (card.tag || 'Live') : (card.tag || 'בקרוב')}
    </span>
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
      {#if card.ctaLabel && card.hasForm}
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <button class="card-link card-cta-btn" style="margin-top:0;" on:click|stopPropagation={() => openCard(card)}>
        {card.ctaLabel} <span class="arrow">←</span>
      </button>
      {/if}
    </div>
  </div>
  {/each}
</div>

<!-- FULL-SCREEN MODAL -->
{#if activeCard}
<!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
<div class="modal-backdrop" on:click={closeCard}>
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="modal-card" class:banner-layout={!!activeCard.banner} on:click|stopPropagation>

    <!-- CLOSE BUTTON -->
    <button class="modal-close" on:click={closeCard}>×</button>

    <!-- LEFT/TOP: HERO PANEL -->
    <div class="modal-hero" style={activeCard.heroStyle}>
      {#if activeCard.banner}
        <img src={activeCard.banner} alt={activeCard.title} class="modal-banner" />
      {:else}
        <div class="modal-hero-inner">
          <span class="modal-status {activeCard.status === 'live' ? 'status-live' : 'status-soon'}" class:dark-badge={activeCard.darkHero}>
            {activeCard.status === 'live' ? (activeCard.tag || 'Live') : (activeCard.tag || 'בקרוב')}
          </span>
          <span class="modal-emoji">{activeCard.emoji}</span>
          <div class="modal-category" class:dark-text={activeCard.darkHero}>{activeCard.category}</div>
          <h2 class="modal-title" class:dark-text={activeCard.darkHero}>{activeCard.title}</h2>
          <p class="modal-desc" class:dark-text={activeCard.darkHero}>{activeCard.desc}</p>
        </div>
      {/if}
    </div>

    <!-- RIGHT/BOTTOM: BODY PANEL -->
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

      {#if activeCard.note}
      <p class="modal-note">⚠️ {activeCard.note}</p>
      {/if}

      {#if activeCard.hasForm}
        <div class="modal-form-section">
          {#if formStatus[activeCard.id] === 'success'}
            <div class="form-success">
              <span class="success-icon">✅</span>
              <p>תודה! ניצור קשר בקרוב.</p>
            </div>
          {:else if formStatus[activeCard.id] === 'loading'}
            <div class="form-success">
              <span class="success-icon">⏳</span>
              <p>שולח פרטים...</p>
            </div>
          {:else}
            <p class="form-label">{activeCard.formLabel || 'הצטרפות לקבוצה'}</p>
            {#if activeCard.useGoogleSignIn}
              <div id="google-signin-btn-{activeCard.id}" class="google-signin-container"></div>
            {/if}
            {#if !activeCard.useGoogleSignIn}
            <form class="join-form" on:submit|preventDefault={() => submitForm(activeCard)}>
              <input type="text" placeholder="שם מלא *" bind:value={formValues[activeCard.id].name} required />
              {#if (activeCard.formFields || ['name', 'phone']).includes('phone')}
              <input type="tel" placeholder="מספר טלפון *" bind:value={formValues[activeCard.id].phone} required />
              {/if}
              <button type="submit" class="submit-btn">הצטרפות לקבוצה ←</button>
            </form>
            {/if}
            {#if formStatus[activeCard.id] === 'error'}
              <p class="form-error">משהו השתבש. נסו שוב.</p>
            {/if}
          {/if}
        </div>
      {:else if activeCard.links.length > 0}
      <div class="modal-links">
        {#each activeCard.links as link, i}
        <a href={link.href} class="modal-link-btn" class:secondary={i > 0}
           target={link.external ? '_blank' : '_self'}
           rel={link.external ? 'noopener' : ''}>
          {link.label}
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
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    z-index: 1000;
    display: flex; align-items: center; justify-content: center;
    padding: 1.5rem;
    animation: backdropIn 0.2s ease;
  }
  @keyframes backdropIn { from { opacity: 0; } to { opacity: 1; } }

  /* Default: two-column layout (no banner) */
  .modal-card {
    display: flex;
    flex-direction: row;
    background: white;
    border-radius: 24px;
    max-width: 840px;
    width: 100%;
    max-height: 82vh;
    overflow: hidden;
    position: relative;
    animation: modalUp 0.35s cubic-bezier(0.23,1,0.32,1);
    box-shadow: 0 40px 120px rgba(0,0,0,0.4);
  }

  /* Banner layout: stacked (banner on top, content below) */
  .modal-card.banner-layout {
    flex-direction: column;
    max-width: 620px;
  }
  .modal-card.banner-layout .modal-hero {
    width: 100%;
    max-height: 210px;
    flex-shrink: 0;
  }
  .modal-card.banner-layout .modal-banner {
    height: 210px;
    object-fit: cover;
  }
  @keyframes modalUp {
    from { transform: translateY(40px) scale(0.97); opacity: 0; }
    to   { transform: translateY(0) scale(1); opacity: 1; }
  }

  /* Close button — top corner of modal */
  .modal-close {
    position: absolute;
    top: 1rem;
    right: 1rem;
    z-index: 100;
    width: 36px; height: 36px;
    border: none; border-radius: 50%;
    background: rgba(0,0,0,0.35);
    backdrop-filter: blur(4px);
    color: white; font-size: 1.4rem; line-height: 1;
    cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: background 0.2s, transform 0.2s;
    padding: 0;
  }
  .modal-close:hover { background: rgba(0,0,0,0.55); transform: scale(1.1); }

  /* Hero panel (right side in RTL) */
  .modal-hero {
    width: 300px;
    flex-shrink: 0;
    position: relative;
    overflow: hidden;
  }

  .modal-banner {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  /* Hero content for non-banner cards */
  .modal-hero-inner {
    height: 100%;
    padding: 3rem 1.8rem 2rem;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    gap: 0.3rem;
    color: white;
  }

  .modal-status {
    display: inline-block; font-size: 0.7rem; font-weight: 700;
    padding: 0.25em 0.75em; border-radius: 100px;
    text-transform: uppercase; letter-spacing: 0.05em;
    margin-bottom: 0.8rem;
    background: rgba(33,163,102,0.25); color: #2ECC71;
    align-self: flex-start;
  }
  .modal-status.status-soon { background: rgba(255,255,255,0.15); color: rgba(255,255,255,0.7); }
  .modal-status.dark-badge { background: rgba(0,0,0,0.08); color: rgba(0,0,0,0.5); }

  .modal-emoji { font-size: 3rem; display: block; margin-bottom: 0.4rem; }

  .modal-category {
    font-size: 0.72rem; font-weight: 700;
    text-transform: uppercase; letter-spacing: 0.1em;
    opacity: 0.6; margin-bottom: 0.3rem;
    color: white;
  }
  .modal-title {
    font-family: 'Secular One', sans-serif;
    font-size: clamp(1.5rem, 3vw, 2rem);
    line-height: 1.2; margin-bottom: 0.5rem;
    color: white;
  }
  .modal-desc {
    font-size: 0.9rem; opacity: 0.8; line-height: 1.55;
    color: white;
  }
  .dark-text { color: #1a1a2e !important; }

  /* Body panel (left side in RTL) */
  .modal-body {
    flex: 1;
    overflow-y: auto;
    padding: 2rem 2rem 2rem;
    display: flex;
    flex-direction: column;
    min-width: 0;
    background: white;
  }

  .modal-about {
    font-size: 0.95rem; line-height: 1.7; color: #5a5a7a;
    margin-bottom: 1.4rem;
    padding-bottom: 1.4rem;
    border-bottom: 1px solid rgba(0,0,0,0.07);
  }

  .modal-details { display: flex; flex-direction: column; gap: 0.9rem; margin-bottom: 1.4rem; }
  .modal-detail-item { display: flex; align-items: flex-start; gap: 0.8rem; }
  .detail-icon { font-size: 1.4rem; flex-shrink: 0; }
  .modal-detail-item strong {
    font-size: 0.9rem; color: #1a1a2e;
    display: block; margin-bottom: 0.15rem;
  }
  .modal-detail-item p { font-size: 0.85rem; color: #5a5a7a; margin: 0; line-height: 1.5; }

  .modal-links { display: flex; gap: 0.8rem; flex-wrap: wrap; margin-top: auto; }
  .modal-link-btn {
    display: inline-flex; align-items: center; justify-content: center;
    padding: 0.85rem 1.8rem; border-radius: 50px;
    font-weight: 700; font-size: 0.95rem;
    text-decoration: none;
    background: #1B2A4A; color: white;
    transition: transform 0.2s, box-shadow 0.2s;
    box-shadow: 0 4px 14px rgba(0,0,0,0.15);
  }
  /* Single CTA — full width */
  .modal-links:has(.modal-link-btn:only-child) { display: block; }
  .modal-links:has(.modal-link-btn:only-child) .modal-link-btn {
    width: 100%; font-size: 1rem; padding: 0.95rem 2rem;
  }
  .modal-link-btn.secondary {
    background: transparent; color: #5a5a7a;
    box-shadow: none; border: 1.5px solid rgba(0,0,0,0.15);
  }
  .modal-link-btn:hover { transform: translateY(-2px); box-shadow: 0 8px 22px rgba(0,0,0,0.2); }
  .modal-link-btn.secondary:hover { background: rgba(0,0,0,0.04); }

  .modal-note {
    font-size: 0.82rem; color: #5a5a7a; line-height: 1.6;
    background: rgba(0,0,0,0.04); border-right: 3px solid rgba(0,0,0,0.15);
    padding: 0.7rem 0.9rem; border-radius: 0 8px 8px 0;
    margin-bottom: 1.2rem;
  }

  .modal-coming-soon {
    font-size: 0.9rem; color: #5a5a7a;
    background: rgba(0,0,0,0.04);
    padding: 0.9rem 1.1rem; border-radius: 12px;
    margin-top: auto;
  }

  /* ===== JOIN FORM ===== */
  .modal-form-section { margin-top: auto; }

  .form-label {
    font-size: 0.8rem; font-weight: 700;
    text-transform: uppercase; letter-spacing: 0.08em;
    color: #5a5a7a; margin-bottom: 0.75rem;
  }

  .join-form {
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
  }

  .join-form input {
    width: 100%;
    padding: 0.8rem 1rem;
    border: 1.5px solid rgba(0,0,0,0.12);
    border-radius: 12px;
    font-family: 'Heebo', sans-serif;
    font-size: 0.95rem;
    color: #1a1a2e;
    background: #fafafa;
    direction: rtl;
    text-align: right;
    transition: border-color 0.2s, background 0.2s;
    outline: none;
  }
  .join-form input:focus {
    border-color: #1B2A4A;
    background: white;
  }
  .join-form input::placeholder { color: #aaa; }

  .submit-btn {
    width: 100%;
    padding: 0.95rem;
    background: #1B2A4A;
    color: white;
    border: none;
    border-radius: 50px;
    font-family: 'Heebo', sans-serif;
    font-size: 1rem;
    font-weight: 700;
    cursor: pointer;
    transition: transform 0.2s, background 0.2s, box-shadow 0.2s;
    box-shadow: 0 4px 14px rgba(0,0,0,0.15);
    margin-top: 0.2rem;
  }
  .submit-btn:hover:not(:disabled) {
    background: #253d6a;
    transform: translateY(-1px);
    box-shadow: 0 8px 22px rgba(0,0,0,0.2);
  }
  .submit-btn:disabled { opacity: 0.6; cursor: not-allowed; }

  .form-success {
    text-align: center;
    padding: 1.5rem;
    background: rgba(33,163,102,0.08);
    border-radius: 16px;
    border: 1px solid rgba(33,163,102,0.2);
  }
  .form-success .success-icon { font-size: 2rem; display: block; margin-bottom: 0.5rem; }
  .form-success p { color: #1a1a2e; font-size: 0.95rem; line-height: 1.6; margin: 0; }

  .form-error {
    font-size: 0.82rem;
    color: #e53e3e;
    text-align: center;
    margin-top: 0.2rem;
  }

  .card-cta-btn {
    background: none; border: none; cursor: pointer;
    font-family: inherit; padding: 0; color: inherit;
  }

  .google-signin-container {
    display: flex; justify-content: center;
    margin-bottom: 0.5rem;
    min-height: 44px;
  }

  .form-divider {
    display: flex; align-items: center; gap: 0.75rem;
    margin: 0.4rem 0 0.6rem; color: #aaa; font-size: 0.8rem;
  }
  .form-divider::before,
  .form-divider::after {
    content: ''; flex: 1; height: 1px; background: rgba(0,0,0,0.1);
  }

  /* Mobile: everything stacked */
  @media (max-width: 640px) {
    .modal-backdrop { align-items: flex-end; padding: 0; }
    .modal-card {
      flex-direction: column;
      border-radius: 20px 20px 0 0;
      max-width: 100%;
      max-height: 95vh;
    }
    .modal-card.banner-layout { max-width: 100%; }
    .modal-hero {
      width: 100%;
      min-height: 160px;
      max-height: 200px;
    }
    .modal-card.banner-layout .modal-hero { max-height: 180px; }
    .modal-card.banner-layout .modal-banner { height: 180px; }
    .modal-banner { height: 100%; object-fit: cover; }
    .modal-hero-inner { padding: 2rem 1.5rem 1.5rem; }
    .modal-body { padding: 1.5rem 1.5rem 2rem; }
    .modal-close { top: 0.75rem; right: 0.75rem; }
  }
</style>
