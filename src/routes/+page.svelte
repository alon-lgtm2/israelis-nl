<script>
  import { onMount, tick } from 'svelte';
  import { lang } from '$lib/lang.js';

  let activeCard = null;
  let formValues = {};
  let formStatus = {};
  let googleAutoFilled = {};

  function setLang(l) {
    lang.set(l);
    if (typeof document !== 'undefined') {
      document.documentElement.dir = l === 'he' ? 'rtl' : 'ltr';
      document.documentElement.lang = l;
    }
  }

  // UI strings
  const ui = {
    he: {
      tagline: 'ישראלים, בהולנד.',
      sectionTitle: 'הפרויקטים שלנו',
      sectionDesc: 'קהילה, תוכן, כלים ויוזמות - הכל נבנה מתוך אהבה לקהילה הישראלית בהולנד',
      discover: 'גלו עוד',
      formSuccess: 'תודה! ניצור קשר בקרוב.',
      formLoading: 'שולח פרטים...',
      formError: 'משהו השתבש. נסו שוב.',
      comingSoon: 'בקרוב - עקבו אחרינו לעדכונים',
      live: 'Live',
      soon: 'בקרוב',
      joinGroup: 'הצטרפות לקבוצה',
      fullName: 'שם מלא *',
      phone: 'מספר טלפון *',
      metaDesc: 'קהילה, תוכן, כלים ויוזמות לישראלים בהולנד',
    },
    nl: {
      tagline: "Israeli's in Nederland",
      sectionTitle: 'Onze projecten',
      sectionDesc: "Community, content, tools en initiatieven - alles gebouwd uit liefde voor de Israelische gemeenschap in Nederland",
      discover: 'Ontdek meer',
      formSuccess: 'Bedankt! We nemen snel contact op.',
      formLoading: 'Gegevens verzenden...',
      formError: 'Er ging iets mis. Probeer opnieuw.',
      comingSoon: 'Binnenkort - volg ons voor updates',
      live: 'Live',
      soon: 'Binnenkort',
      joinGroup: 'Word lid',
      fullName: 'Volledige naam *',
      phone: 'Telefoonnummer *',
      metaDesc: "Community, content, tools en initiatieven voor Israeli's in Nederland",
    },
    en: {
      tagline: 'Israelis in the Netherlands',
      sectionTitle: 'Our Projects',
      sectionDesc: 'Community, content, tools and initiatives - all built with love for the Israeli community in the Netherlands',
      discover: 'Discover more',
      formSuccess: 'Thank you! We will be in touch soon.',
      formLoading: 'Sending details...',
      formError: 'Something went wrong. Please try again.',
      comingSoon: 'Coming soon - follow us for updates',
      live: 'Live',
      soon: 'Coming soon',
      joinGroup: 'Join group',
      fullName: 'Full name *',
      phone: 'Phone number *',
      metaDesc: 'Community, content, tools and initiatives for Israelis in the Netherlands',
    }
  };

  function u(l, key) { return ui[l]?.[key] ?? ui.he[key]; }

  function t(l, card, field) {
    if (l === 'he') return card[field];
    return card.i18n?.[l]?.[field] ?? card[field];
  }

  function tDetail(l, card, idx) {
    if (l === 'he') return card.details[idx];
    const translated = card.i18n?.[l]?.details?.[idx];
    return translated ? { ...card.details[idx], ...translated } : card.details[idx];
  }

  function tLink(l, card, idx, field) {
    if (l === 'he') return card.links[idx]?.[field];
    return card.i18n?.[l]?.links?.[idx]?.[field] ?? card.links[idx]?.[field];
  }

  function arrow(l) { return l === 'he' ? '←' : '→'; }

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
              subject: `${card?.title || 'israelis.nl'} - נרשם חדש: ${payload.name}`,
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
        { href: 'https://hatecheck.nl/', label: 'לכלי', cardLabel: 'לכלי', external: true },
      ],
      i18n: {
        nl: {
          category: 'Bestrijding antisemitisme',
          title: 'HateCheck',
          desc: 'Een tool voor het detecteren en monitoren van online haatcontent. Want we verdienen een veilige online ruimte.',
          about: 'Een tool waarmee je antisemitische en haatdragende content op sociale media kunt melden, documenteren en monitoren. Want we verdienen het om te weten en te handelen.',
          details: [
            { title: 'Detectie', text: 'Detectie van antisemitische en racistische content op sociale media.' },
            { title: 'Monitoring', text: 'Documentatie en monitoring van haatcontent door de tijd heen.' },
            { title: 'Melding', text: 'Tools om te melden bij platforms en autoriteiten.' },
          ],
          links: [{ label: 'Naar de tool', cardLabel: 'Naar tool' }],
        },
        en: {
          category: 'Fighting Antisemitism',
          title: 'HateCheck',
          desc: 'A tool for detecting and monitoring online hate content. Because we deserve a safe online space.',
          about: 'A tool that enables reporting, documenting, and tracking antisemitic and hate content on social media. Because we deserve to know and to act.',
          details: [
            { title: 'Detection', text: 'Detection of antisemitic and racist content on social media.' },
            { title: 'Monitoring', text: 'Documentation and tracking of hate content over time.' },
            { title: 'Reporting', text: 'Tools for reporting to platforms and authorities.' },
          ],
          links: [{ label: 'Go to tool', cardLabel: 'Go to tool' }],
        }
      }
    },
    {
      id: 'hayom',
      emoji: '🎯',
      category: 'פעילויות',
      tag: 'פנאי',
      title: 'אז מה עושים היום',
      desc: 'המלצות לאירועים ופעילויות לילדים בהולנד - כל סוף שבוע מחדש.',
      status: 'live',
      theme: 'card-hayom',
      heroStyle: 'background: linear-gradient(135deg, #7B2FBE 0%, #C850C0 50%, #9B59B6 100%);',
      banner: '/hayom-banner.jpeg',
      about: 'הורים לילדים בהולנד שמחפשים מה לעשות בסוף השבוע? בהולנד, שפע של אירועי תרבות ובידור מותאמים לילדים - אבל אין מקום אחד שמאגד אותם. הקבוצה הזאת היא הפתרון.',
      details: [
        { icon: '📅', title: 'כל סוף שבוע', text: 'פוסטים שבועיים עם אירועים לסוף השבוע הקרוב - כדי שתוכלו לתכנן מראש.' },
        { icon: '📍', title: 'מקומי ואמיתי', text: 'לא נמו, לא חיטהורן. אירועים ייחודיים שמתרחשים במקום מסוים ובתאריך ספציפי.' },
        { icon: '👨‍👩‍👧', title: 'לכל המשפחה', text: 'מותאם לילדים בכל הגילאים - ממוזיקה ועד ספורט, מאמנות ועד טבע.' },
      ],
      links: [
        { href: 'https://fun.israelis.nl', label: 'קחו אותי לשם! ←', cardLabel: 'לפעילויות', external: true },
      ],
      i18n: {
        nl: {
          category: 'Activiteiten',
          tag: 'Vrije tijd',
          title: 'Wat doen we vandaag?',
          desc: 'Aanbevelingen voor evenementen en activiteiten voor kinderen in Nederland - elk weekend opnieuw.',
          about: 'Ouders van kinderen in Nederland die op zoek zijn naar weekendactiviteiten? Nederland biedt een overvloed aan culturele en entertainment evenementen voor kinderen - maar er is geen centrale plek die ze bundelt. Deze groep is de oplossing.',
          details: [
            { title: 'Elk weekend', text: 'Wekelijkse posts met evenementen voor het komende weekend - zodat je vooruit kunt plannen.' },
            { title: 'Lokaal en echt', text: 'Geen Nemo, geen Giethoorn. Unieke evenementen op een specifieke plek en datum.' },
            { title: 'Voor het hele gezin', text: 'Geschikt voor kinderen van alle leeftijden - van muziek tot sport, van kunst tot natuur.' },
          ],
          links: [{ label: 'Breng me daar! →', cardLabel: 'Activiteiten' }],
        },
        en: {
          category: 'Activities',
          tag: 'Leisure',
          title: 'So What Are We Doing Today?',
          desc: 'Recommendations for events and activities for kids in the Netherlands - every weekend.',
          about: 'Parents of children in the Netherlands looking for weekend activities? The Netherlands offers plenty of cultural and entertainment events for kids - but there is no single place that aggregates them. This group is the solution.',
          details: [
            { title: 'Every weekend', text: 'Weekly posts with events for the upcoming weekend - so you can plan ahead.' },
            { title: 'Local and real', text: 'Not Nemo, not Giethoorn. Unique events at a specific place and date.' },
            { title: 'For the whole family', text: 'Suitable for children of all ages - from music to sports, from art to nature.' },
          ],
          links: [{ label: 'Take me there! →', cardLabel: 'Activities' }],
        }
      }
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
      about: 'ייעוץ קריירה מותאם לישראלים שמחפשים עבודה בהולנד. שוק העבודה ההולנדי שונה - נעזור לכם לנווט אותו נכון.',
      details: [
        { icon: '📄', title: 'CV הולנדי', text: 'CV בפורמט המקומי שעובד עם מגייסים הולנדיים.' },
        { icon: '🎯', title: 'הכנה לראיון', text: 'אסטרטגיות וכלים לראיונות בשוק ההולנדי.' },
        { icon: '🔗', title: 'LinkedIn', text: 'אופטימיזציה של הפרופיל למציאת הזדמנויות.' },
      ],
      links: [
        { href: 'https://jobs.israelis.nl/', label: 'למציאת עבודה ←', cardLabel: 'לאתר', external: true },
      ],
      i18n: {
        nl: {
          category: 'Carriere',
          tag: 'Nieuw',
          title: 'Werk vinden in Nederland',
          desc: "Effectief carriere-advies voor Israeli's in Nederland. Hoe te zoeken, hoe op te vallen, en hoe je volgende baan te bemachtigen.",
          about: "Op maat gemaakt carriere-advies voor Israeli's die werk zoeken in Nederland. De Nederlandse arbeidsmarkt is anders - wij helpen je er goed doorheen te navigeren.",
          details: [
            { title: 'Nederlands CV', text: 'Een CV in het lokale formaat dat werkt bij Nederlandse recruiters.' },
            { title: 'Sollicitatietraining', text: 'Strategieen en tools voor sollicitatiegesprekken op de Nederlandse markt.' },
            { title: 'LinkedIn', text: 'Profieloptimalisatie om kansen te vinden.' },
          ],
          links: [{ label: 'Vind een baan →', cardLabel: 'Naar site' }],
        },
        en: {
          category: 'Careers',
          tag: 'New',
          title: 'Finding a Job in the Netherlands',
          desc: 'Effective career advice for Israelis in the Netherlands. How to search, how to stand out, and how to land your next role.',
          about: 'Career advice tailored for Israelis looking for work in the Netherlands. The Dutch job market is different - we will help you navigate it successfully.',
          details: [
            { title: 'Dutch CV', text: 'A CV in the local format that works with Dutch recruiters.' },
            { title: 'Interview prep', text: 'Strategies and tools for interviews in the Dutch market.' },
            { title: 'LinkedIn', text: 'Profile optimization for finding opportunities.' },
          ],
          links: [{ label: 'Find a job →', cardLabel: 'To site' }],
        }
      }
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
      about: 'הבית המקצועי של קהילת הטק והחדשנות הישראלית בהולנד. הקבוצה מהווה צומת מרכזי ליזמים, מנהלים, משקיעים ואנשי פיתוח - ומטרתה לייצר נטוורקינג איכותי, שיתוף ידע אסטרטגי, והזדמנויות צמיחה בתעשייה המקומית.',
      details: [
        { icon: '🧠', title: 'ידע ושוק', text: 'דיונים מקצועיים, תובנות ותחזיות, וניתוחי שוק עדכניים על האקוסיסטם ההולנדי.' },
        { icon: '📋', title: 'לוח משרות ממוקד', text: 'פוזיציות High-Tech ו-Corporate בהולנד ובאירופה - ישירות מחברות ומגייסים בתוך הקהילה.' },
        { icon: '🤝', title: 'חיבורים עסקיים', text: 'נטוורקינג, שיתופי פעולה, והיכרויות בין יזמים, מנהלים ומשקיעים שמייצרות הזדמנויות אמיתיות.' },
      ],
      note: 'הקהילה שומרת על שיח מקצועי ואיכותי. פרסום מסחרי וקידום עצמי מתאפשרים במסגרת שיתופי פעולה רשמיים בלבד - לפרטים, פנו לאדמינים.',
      links: [
        { href: 'https://www.facebook.com/groups/426953251245124', label: 'הצטרפו לקהילה ←', cardLabel: 'לקהילה', external: true },
      ],
      i18n: {
        nl: {
          category: 'Tech & Innovatie',
          tag: 'Professioneel netwerk',
          title: "Israeli's in de Tech in Nederland",
          desc: "Het toonaangevende professionele netwerk in Europa voor de Israelische tech- en innovatiegemeenschap in Nederland.",
          about: "Het professionele thuis van de Israelische tech- en innovatiegemeenschap in Nederland. De groep dient als centraal knooppunt voor ondernemers, managers, investeerders en ontwikkelaars - gericht op kwalitatief netwerken, strategische kennisdeling en groeikansen in de lokale industrie.",
          details: [
            { title: 'Kennis & Markt', text: 'Professionele discussies, inzichten en voorspellingen, en actuele marktanalyses over het Nederlandse ecosysteem.' },
            { title: 'Vacaturebord', text: 'High-Tech en Corporate posities in Nederland en Europa - rechtstreeks van bedrijven en recruiters binnen de community.' },
            { title: 'Zakelijke connecties', text: 'Netwerken, samenwerkingen en kennismakingen tussen ondernemers, managers en investeerders die echte kansen creeren.' },
          ],
          note: 'De community handhaaft een professioneel en kwalitatief gesprek. Commerciele advertenties en zelfpromotie zijn alleen mogelijk via officiele samenwerkingen - neem contact op met de admins voor details.',
          links: [{ label: 'Sluit je aan →', cardLabel: 'Naar community' }],
        },
        en: {
          category: 'Tech & Innovation',
          tag: 'Professional network',
          title: 'Israeli Tech in the Netherlands',
          desc: "Europe's leading professional network for the Israeli tech and innovation community in the Netherlands.",
          about: 'The professional home of the Israeli tech and innovation community in the Netherlands. The group serves as a central hub for entrepreneurs, managers, investors, and developers - aimed at quality networking, strategic knowledge sharing, and growth opportunities in the local industry.',
          details: [
            { title: 'Knowledge & Market', text: 'Professional discussions, insights and forecasts, and current market analyses of the Dutch ecosystem.' },
            { title: 'Job Board', text: 'High-Tech and Corporate positions in the Netherlands and Europe - directly from companies and recruiters within the community.' },
            { title: 'Business Connections', text: 'Networking, collaborations, and introductions between entrepreneurs, managers, and investors that create real opportunities.' },
          ],
          note: 'The community maintains a professional and quality discourse. Commercial advertising and self-promotion are only allowed through official collaborations - contact the admins for details.',
          links: [{ label: 'Join the community →', cardLabel: 'To community' }],
        }
      }
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
      about: 'ליגת כדורשת לאמהות ישראליות בהולנד. ספורט, תחרות, וחברות - כי אמהות צריכות זמן לעצמן גם.',
      details: [
        { icon: '🏆', title: 'ליגה תחרותית', text: 'עונות משחקים עם קבוצות מרחבי הולנד.' },
        { icon: '🤸', title: 'ללא ניסיון קודם', text: 'פתוח לכל הרמות - מתחילות ועד מנוסות.' },
        { icon: '❤️', title: 'קהילה חברתית', text: 'מעבר לספורט - חברויות ורגעים שנשארים.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'רוצה פרטים נוספים? שוקלת להצטרף? לחצי מטה ונהיה איתך בקשר בהקדם!',
      ctaLabel: 'לפרטים נוספים',
      links: [
        { href: 'https://catchball.nl/', label: 'לאתר', cardLabel: 'לאתר', external: true },
      ],
      i18n: {
        nl: {
          category: 'Sport',
          tag: 'Sport',
          title: 'NCA Moedercompetitie',
          desc: 'De Nederlandse moedercompetitie in catchball. Sport, plezier en community voor Israelische moeders in Nederland.',
          about: 'Een catchball-competitie voor Israelische moeders in Nederland. Sport, competitie en vriendschap - want moeders verdienen ook tijd voor zichzelf.',
          details: [
            { title: 'Competitie', text: 'Speelseizoenen met teams uit heel Nederland.' },
            { title: 'Geen ervaring nodig', text: 'Open voor alle niveaus - van beginners tot gevorderden.' },
            { title: 'Sociale community', text: 'Meer dan sport - vriendschappen en momenten die blijven.' },
          ],
          formLabel: 'Meer informatie? Overweeg je om mee te doen? Klik hieronder en we nemen snel contact met je op!',
          ctaLabel: 'Meer informatie',
          links: [{ label: 'Naar site', cardLabel: 'Naar site' }],
        },
        en: {
          category: 'Sports',
          tag: 'Sports',
          title: 'NCA Moms League',
          desc: 'The Dutch moms league in catchball. Sports, fun, and community for Israeli moms in the Netherlands.',
          about: 'A catchball league for Israeli moms in the Netherlands. Sports, competition, and friendship - because moms deserve time for themselves too.',
          details: [
            { title: 'Competitive league', text: 'Game seasons with teams from across the Netherlands.' },
            { title: 'No experience needed', text: 'Open to all levels - from beginners to experienced players.' },
            { title: 'Social community', text: 'Beyond sports - friendships and moments that last.' },
          ],
          formLabel: 'Want more info? Considering joining? Click below and we will get back to you soon!',
          ctaLabel: 'More info',
          links: [{ label: 'To website', cardLabel: 'To website' }],
        }
      }
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
      about: 'קהילה של הורים ישראלים שגדלים ביחד באמסטלפיין. שאלות על בתי ספר, גנים, רופאים, ופעילויות - בסביבה מוכרת ובשפה האחת.',
      details: [
        { icon: '🏫', title: 'חינוך ובתי ספר', text: 'המלצות על גנים, בתי ספר, וחינוך בהולנד.' },
        { icon: '🎉', title: 'מפגשים', text: 'ברביקיו, חגים, ואירועים לילדים ביחד.' },
        { icon: '🤝', title: 'תמיכה הדדית', text: 'כי לגדל ילדים בחו"ל עושים ביחד.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל להצטרפות',
      ctaLabel: 'להצטרפות',
      links: [],
      i18n: {
        nl: {
          category: 'Ouderschap',
          tag: 'Community',
          title: 'Papot Amstelveen',
          desc: 'De Israelische oudergemeenschap in Amstelveen en omgeving. Tips, bijeenkomsten en onderlinge steun.',
          about: 'Een community van Israelische ouders die samen opgroeien in Amstelveen. Vragen over scholen, kinderdagverblijven, artsen en activiteiten - in een vertrouwde omgeving.',
          details: [
            { title: 'Onderwijs & Scholen', text: 'Aanbevelingen voor kinderdagverblijven, scholen en onderwijs in Nederland.' },
            { title: 'Bijeenkomsten', text: "Barbecues, feestdagen en kinderactiviteiten samen." },
            { title: 'Onderlinge steun', text: 'Want kinderen opvoeden in het buitenland doe je samen.' },
          ],
          formLabel: 'Log in met Google om lid te worden',
          ctaLabel: 'Word lid',
        },
        en: {
          category: 'Parenting',
          tag: 'Community',
          title: 'Papot Amstelveen',
          desc: 'The Israeli parents community in Amstelveen and surroundings. Tips, meetups, and mutual support.',
          about: 'A community of Israeli parents growing together in Amstelveen. Questions about schools, daycares, doctors, and activities - in a familiar environment.',
          details: [
            { title: 'Education & Schools', text: 'Recommendations for daycares, schools, and education in the Netherlands.' },
            { title: 'Meetups', text: 'Barbecues, holidays, and kids activities together.' },
            { title: 'Mutual support', text: 'Because raising kids abroad is better together.' },
          ],
          formLabel: 'Sign in with Google to join',
          ctaLabel: 'Join',
        }
      }
    },
    {
      id: 'saba',
      emoji: '👴',
      category: 'משפחה',
      tag: 'קהילה',
      title: 'סבא בא',
      desc: 'ההורים נוחתים בשבוע הבא? קהילה לישראלים שמארחים הורים מבוגרים לביקור ממושך בהולנד - טיפים, מסלולים, ועצות מהשטח.',
      status: 'live',
      theme: 'card-saba',
      heroStyle: 'background: white; color: #1a1a2e !important;',
      darkHero: true,
      about: 'מקום לישראלים שמארחים הורים מבוגרים לשבועות ארוכים בהולנד. מה לעשות עם סבא כשגמרו את אמסטרדם? לאן לקחת סבתא שמתקשה ללכת? איך מסבירים לרופא אילנית מה כואב לה? כאן תמצאו אנשים שכבר עברו את זה - ויודעים.',
      details: [
        { icon: '🗺️', title: 'מסלולים לפי משך השהייה', text: 'תכניות ביקור לשבוע, שבועיים, ויותר - כולל אופציות לכאלה שמתקשים בהליכה ממושכת.' },
        { icon: '🏥', title: 'בריאות ובירוקרטיה', text: 'רופאים, תרופות, ביטוחי נסיעות - כל מה שצריך לדעת לפני ובמהלך הביקור.' },
        { icon: '🍽️', title: 'המלצות מהשטח', text: 'מסעדות, פעילויות, וטיפים שעובדים - ממשפחות שכבר עשו את זה.' },
      ],
      hasForm: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      useGoogleSignIn: true,
      ctaLabel: 'לפרטים נוספים',
      links: [],
      i18n: {
        nl: {
          category: 'Familie',
          tag: 'Community',
          title: 'Opa Komt',
          desc: "Landen je ouders volgende week? Een community voor Israeli's die bejaarde ouders ontvangen voor een lang bezoek in Nederland - tips, routes en advies uit de praktijk.",
          about: "Een plek voor Israeli's die bejaarde ouders wekenlang ontvangen in Nederland. Wat te doen met opa als Amsterdam 'op' is? Waar neem je oma mee als ze moeilijk loopt? Hoe leg je aan de huisarts uit wat er pijn doet? Hier vind je mensen die het al hebben meegemaakt - en het weten.",
          details: [
            { title: 'Routes per verblijfsduur', text: 'Bezoekplannen voor een week, twee weken en meer - inclusief opties voor wie moeite heeft met lang lopen.' },
            { title: 'Gezondheid & Bureaucratie', text: 'Artsen, medicijnen, reisverzekeringen - alles wat je moet weten voor en tijdens het bezoek.' },
            { title: 'Tips uit de praktijk', text: 'Restaurants, activiteiten en tips die werken - van families die het al hebben gedaan.' },
          ],
          formLabel: 'Log in met Google voor meer informatie',
          ctaLabel: 'Meer informatie',
        },
        en: {
          category: 'Family',
          tag: 'Community',
          title: 'Grandpa Is Coming',
          desc: 'Parents landing next week? A community for Israelis hosting elderly parents for extended visits in the Netherlands - tips, routes, and field advice.',
          about: "A place for Israelis hosting elderly parents for weeks in the Netherlands. What to do with grandpa when Amsterdam is done? Where to take grandma who has trouble walking? How to explain to the doctor what hurts? Here you'll find people who have been through it - and know.",
          details: [
            { title: 'Routes by stay duration', text: 'Visit plans for one week, two weeks, and more - including options for those with limited mobility.' },
            { title: 'Health & Bureaucracy', text: 'Doctors, medications, travel insurance - everything you need to know before and during the visit.' },
            { title: 'Field recommendations', text: 'Restaurants, activities, and tips that work - from families who have done it.' },
          ],
          formLabel: 'Sign in with Google for more info',
          ctaLabel: 'More info',
        }
      }
    },
    {
      id: 'holland',
      emoji: '🎻',
      category: 'מוזיקה',
      tag: 'WhatsApp',
      title: 'הולנד הקלאסית',
      desc: 'קבוצת וואטסאפ לחובבי מוזיקה קלאסית בהולנד - קונצרטים, המלצות, ושיח מוזיקלי.',
      status: 'live',
      theme: 'card-holland',
      heroStyle: 'background: #F5E6D3; color: #1a1a2e !important;',
      darkHero: true,
      about: 'קהילה ייעודית לחובבי מוזיקה קלאסית בהולנד. קונצרטים של Concertgebouw ופילהרמונית רוטרדם, המלצות על אלבומים, ושיח מוזיקלי - הכל בעברית.',
      details: [
        { icon: '🎼', title: 'קונצרטים', text: 'עדכונים על הופעות ותזמורות מובילות בהולנד - כולל Concertgebouw.' },
        { icon: '🎧', title: 'המלצות מוזיקה', text: 'שיתוף אהבות, תגליות, ויצירות שכדאי להכיר.' },
        { icon: '🎹', title: 'קהילה מוזיקלית', text: 'חיבור בין מוזיקאים, מורים, ואוהבי מוזיקה קלאסית.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל להצטרפות',
      ctaLabel: 'להצטרפות',
      links: [],
      i18n: {
        nl: {
          category: 'Muziek',
          tag: 'WhatsApp',
          title: 'Klassiek Nederland',
          desc: 'WhatsApp-groep voor liefhebbers van klassieke muziek in Nederland - concerten, aanbevelingen en muzikaal gesprek.',
          about: 'Een community speciaal voor liefhebbers van klassieke muziek in Nederland. Concertgebouw en Rotterdams Philharmonisch, albumaanbevelingen en muzikaal gesprek.',
          details: [
            { title: 'Concerten', text: 'Updates over optredens en toonaangevende orkesten in Nederland - inclusief het Concertgebouw.' },
            { title: 'Muziekaanbevelingen', text: 'Het delen van favorieten, ontdekkingen en werken die de moeite waard zijn.' },
            { title: 'Muzikale community', text: 'Verbinding tussen muzikanten, docenten en liefhebbers van klassieke muziek.' },
          ],
          formLabel: 'Log in met Google om lid te worden',
          ctaLabel: 'Word lid',
        },
        en: {
          category: 'Music',
          tag: 'WhatsApp',
          title: 'Classical Holland',
          desc: 'WhatsApp group for classical music lovers in the Netherlands - concerts, recommendations, and musical discussion.',
          about: 'A community dedicated to classical music lovers in the Netherlands. Concertgebouw and Rotterdam Philharmonic concerts, album recommendations, and musical discussion.',
          details: [
            { title: 'Concerts', text: 'Updates on performances and leading orchestras in the Netherlands - including Concertgebouw.' },
            { title: 'Music recommendations', text: 'Sharing favorites, discoveries, and works worth knowing.' },
            { title: 'Musical community', text: 'Connecting musicians, teachers, and classical music lovers.' },
          ],
          formLabel: 'Sign in with Google to join',
          ctaLabel: 'Join',
        }
      }
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
      about: 'בהשראת sedermap.com - מפה שמאגדת סדרי פסח ציבוריים, אירועי חג, וקהילות ישראליות וחבד בהולנד. בין אם אתם מחפשים סדר פתוח, רוצים לארגן אחד, או פשוט רוצים לדעת מה קורה ליד הבית - הכל כאן.',
      details: [
        { icon: '🕍', title: 'סדרי פסח ציבוריים', text: 'סדרי פסח פתוחים מקהילות, בתי כנסת, וחבד ברחבי הולנד.' },
        { icon: '📍', title: 'לפי מיקום', text: 'מצאו אירועי חג קרובים אליכם - באמסטרדם, רוטרדם, האג ובכל הארץ.' },
        { icon: '🍽️', title: 'הוסיפו את שלכם', text: 'מארגנים סדר פתוח? הוסיפו אותו למפה ובואו חברים.' },
      ],
      links: [
        { href: 'https://sedermap.com', label: 'למפה האינטראקטיבית ←', cardLabel: 'למפה', external: true },
      ],
      i18n: {
        nl: {
          category: 'Feestdagen',
          tag: 'Pesach',
          title: 'Haggadah op de Kaart',
          desc: 'Op zoek naar een seder in Nederland? Interactieve kaart van openbare Pesach-seders, gemeenschappen en feestdagevenementen door heel het land.',
          about: 'Geinspireerd door sedermap.com - een kaart die openbare Pesach-seders, feestdagevenementen en Israelische en Chabad-gemeenschappen in Nederland bundelt. Of je nu een open seder zoekt, er een wilt organiseren, of gewoon wilt weten wat er bij jou in de buurt gebeurt - het staat hier allemaal.',
          details: [
            { title: 'Openbare Pesach-seders', text: 'Open seders van gemeenschappen, synagogen en Chabad door heel Nederland.' },
            { title: 'Op locatie', text: 'Vind feestdagevenementen bij jou in de buurt - in Amsterdam, Rotterdam, Den Haag en door heel het land.' },
            { title: 'Voeg de jouwe toe', text: 'Organiseer je een open seder? Voeg het toe aan de kaart en nodig vrienden uit.' },
          ],
          links: [{ label: 'Naar de interactieve kaart →', cardLabel: 'Naar kaart' }],
        },
        en: {
          category: 'Holidays',
          tag: 'Passover',
          title: 'Haggadah on the Map',
          desc: 'Looking for a Passover seder in the Netherlands? Interactive map of public seders, communities, and holiday events across the country.',
          about: 'Inspired by sedermap.com - a map that aggregates public Passover seders, holiday events, and Israeli and Chabad communities in the Netherlands. Whether you are looking for an open seder, want to organize one, or just want to know what is happening near you - it is all here.',
          details: [
            { title: 'Public Passover seders', text: 'Open seders from communities, synagogues, and Chabad across the Netherlands.' },
            { title: 'By location', text: 'Find holiday events near you - in Amsterdam, Rotterdam, The Hague, and across the country.' },
            { title: 'Add yours', text: 'Organizing an open seder? Add it to the map and invite friends.' },
          ],
          links: [{ label: 'To the interactive map →', cardLabel: 'To map' }],
        }
      }
    },
    {
      id: 'nifgashim',
      emoji: '☕',
      category: 'הגיל השלישי',
      tag: 'קהילה',
      title: 'נפגשים בעברית',
      desc: 'קהילה לישראלים בגיל 60 ומעלה המתגוררים בהולנד - שיח בעברית, מפגשים חברתיים, ועזרה הדדית.',
      status: 'live',
      theme: 'card-nifgashim',
      heroStyle: 'background: linear-gradient(135deg, #16705F 0%, #1DAA8A 100%);',
      about: 'קהילה לישראלים בגיל 60 ומעלה שגרים בהולנד. מקום לשיח בעברית, מפגשים חברתיים, ועזרה הדדית - כי גיל שלישי בחו"ל הוא גם הרפתקה.',
      details: [
        { icon: '🗣️', title: 'שיח בעברית', text: 'שיחות, שאלות, והמלצות - בעברית, בין ישראלים.' },
        { icon: '☕', title: 'מפגשים חברתיים', text: 'קפה, הליכות, ואירועים לגיל השלישי ברחבי הולנד.' },
        { icon: '🤝', title: 'עזרה הדדית', text: 'שאלות על רפואה, בירוקרטיה, וחיי יומיום - ביחד יותר קל.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      ctaLabel: 'לפרטים נוספים',
      links: [],
      i18n: {
        nl: {
          category: 'Senioren',
          tag: 'Community',
          title: 'Ontmoeten in het Hebreeuws',
          desc: "Een community voor Israeli's van 60+ in Nederland - gesprekken in het Hebreeuws, sociale bijeenkomsten en onderlinge hulp.",
          about: "Een community voor Israeli's van 60 en ouder die in Nederland wonen. Een plek voor gesprekken in het Hebreeuws, sociale bijeenkomsten en onderlinge hulp - want het derde levensfase in het buitenland is ook een avontuur.",
          details: [
            { title: 'Gesprekken in het Hebreeuws', text: "Gesprekken, vragen en aanbevelingen - in het Hebreeuws, tussen Israeli's." },
            { title: 'Sociale bijeenkomsten', text: 'Koffie, wandelingen en evenementen voor senioren door heel Nederland.' },
            { title: 'Onderlinge hulp', text: 'Vragen over gezondheidszorg, bureaucratie en het dagelijks leven - samen is het makkelijker.' },
          ],
          formLabel: 'Log in met Google voor meer informatie',
          ctaLabel: 'Meer informatie',
        },
        en: {
          category: 'Seniors',
          tag: 'Community',
          title: 'Meeting in Hebrew',
          desc: 'A community for Israelis aged 60+ living in the Netherlands - Hebrew conversations, social meetups, and mutual help.',
          about: 'A community for Israelis aged 60 and over living in the Netherlands. A place for Hebrew conversations, social meetups, and mutual help - because the third age abroad is also an adventure.',
          details: [
            { title: 'Hebrew conversations', text: 'Chats, questions, and recommendations - in Hebrew, among Israelis.' },
            { title: 'Social meetups', text: 'Coffee, walks, and events for seniors across the Netherlands.' },
            { title: 'Mutual help', text: 'Questions about healthcare, bureaucracy, and daily life - together it is easier.' },
          ],
          formLabel: 'Sign in with Google for more info',
          ctaLabel: 'More info',
        }
      }
    },
    {
      id: 'business',
      emoji: '🤝',
      category: 'עסקים',
      tag: 'רשת',
      title: 'Israeli Business Network',
      desc: 'רשת עסקית לישראלים בהולנד. חיבורים, שותפויות, והזדמנויות - בין יזמים ובעלי עסקים בקהילה.',
      status: 'live',
      theme: 'card-business',
      heroStyle: 'background: linear-gradient(135deg, #B7791F 0%, #D69E2E 100%);',
      about: 'רשת לחיבור בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד. שותפויות, לקוחות, וספקים - בתוך הקהילה.',
      details: [
        { icon: '🤝', title: 'חיבורים', text: 'בין יזמים, פרילנסרים, ובעלי עסקים ישראלים בהולנד.' },
        { icon: '💡', title: 'שותפויות', text: 'מציאת שותפים עסקיים בתוך הקהילה הישראלית.' },
        { icon: '📈', title: 'הזדמנויות', text: 'לקוחות, ספקים, ומשקיעים בתוך הרשת.' },
      ],
      hasForm: true,
      useGoogleSignIn: true,
      formLabel: 'התחברו עם גוגל לפרטים נוספים',
      ctaLabel: 'לפרטים נוספים',
      links: [],
      i18n: {
        nl: {
          category: 'Zakelijk',
          tag: 'Netwerk',
          title: 'Israeli Business Network',
          desc: "Een zakelijk netwerk voor Israeli's in Nederland. Connecties, partnerschappen en kansen - tussen ondernemers en bedrijfseigenaren in de community.",
          about: "Een netwerk dat Israelische ondernemers, freelancers en bedrijfseigenaren in Nederland met elkaar verbindt. Partnerschappen, klanten en leveranciers - binnen de community.",
          details: [
            { title: 'Connecties', text: "Tussen Israelische ondernemers, freelancers en bedrijfseigenaren in Nederland." },
            { title: 'Partnerschappen', text: 'Het vinden van zakelijke partners binnen de Israelische gemeenschap.' },
            { title: 'Kansen', text: 'Klanten, leveranciers en investeerders binnen het netwerk.' },
          ],
          formLabel: 'Log in met Google voor meer informatie',
          ctaLabel: 'Meer informatie',
        },
        en: {
          category: 'Business',
          tag: 'Network',
          title: 'Israeli Business Network',
          desc: 'A business network for Israelis in the Netherlands. Connections, partnerships, and opportunities - among entrepreneurs and business owners in the community.',
          about: 'A network connecting Israeli entrepreneurs, freelancers, and business owners in the Netherlands. Partnerships, clients, and suppliers - within the community.',
          details: [
            { title: 'Connections', text: 'Among Israeli entrepreneurs, freelancers, and business owners in the Netherlands.' },
            { title: 'Partnerships', text: 'Finding business partners within the Israeli community.' },
            { title: 'Opportunities', text: 'Clients, suppliers, and investors within the network.' },
          ],
          formLabel: 'Sign in with Google for more info',
          ctaLabel: 'More info',
        }
      }
    },
    {
      id: 'elect',
      emoji: '🗳️',
      category: 'בחירות',
      tag: 'חדש',
      title: 'בחירות מוניציפליות 2026',
      desc: 'הבחירות המוניציפליות בהולנד מגיעות. כל מה שצריך לדעת כדי להצביע - בעברית.',
      status: 'live',
      theme: 'card-elect',
      heroStyle: 'background: linear-gradient(135deg, #154B8C 0%, #1E6EC2 100%);',
      about: 'הבחירות לרשויות המקומיות בהולנד הן ההזדמנות שלנו להשפיע. אם יש לכם אזרחות הולנדית - יש לכם קול. המדריך הזה מסביר הכל בעברית, צעד אחר צעד.',
      details: [
        { icon: '📋', title: 'מי יכול להצביע?', text: 'אזרחים הולנדים ותושבי EU הרשומים בעירייה - כולל ישראלים עם אזרחות כפולה.' },
        { icon: '📅', title: 'מתי?', text: 'בחירות מוניציפליות 2026. המדריך כולל את כל הצעדים לפני יום ההצבעה.' },
        { icon: '🗺️', title: 'איך מצביעים?', text: 'כרטיס הצבעה, מרכז הצבעה, ואיך ממלאים את הפתק - שלב אחר שלב.' },
      ],
      links: [
        { href: '/elect', label: 'למדריך הבחירות ←', cardLabel: 'למדריך', external: false },
      ],
      i18n: {
        nl: {
          category: 'Verkiezingen',
          tag: 'Nieuw',
          title: 'Gemeenteraadsverkiezingen 2026',
          desc: 'De gemeenteraadsverkiezingen in Nederland komen eraan. Alles wat je moet weten om te stemmen - in het Hebreeuws.',
          about: 'De gemeenteraadsverkiezingen zijn onze kans om invloed uit te oefenen. Als je de Nederlandse nationaliteit hebt - heb je een stem. Deze gids legt alles stap voor stap uit.',
          details: [
            { title: 'Wie mag stemmen?', text: 'Nederlandse staatsburgers en EU-ingezetenen die bij de gemeente staan ingeschreven - inclusief Israelis met dubbele nationaliteit.' },
            { title: 'Wanneer?', text: 'Gemeenteraadsverkiezingen 2026. De gids bevat alle stappen voor de verkiezingsdag.' },
            { title: 'Hoe stem je?', text: 'Stempas, stembureau en hoe je het stembiljet invult - stap voor stap.' },
          ],
          links: [{ label: 'Naar de verkiezingsgids →', cardLabel: 'Naar gids' }],
        },
        en: {
          category: 'Elections',
          tag: 'New',
          title: 'Municipal Elections 2026',
          desc: 'The Dutch municipal elections are coming. Everything you need to know to vote - in Hebrew.',
          about: 'The local government elections in the Netherlands are our opportunity to make an impact. If you have Dutch citizenship - you have a voice. This guide explains everything step by step.',
          details: [
            { title: 'Who can vote?', text: 'Dutch citizens and EU residents registered with the municipality - including Israelis with dual citizenship.' },
            { title: 'When?', text: 'Municipal elections 2026. The guide includes all steps before election day.' },
            { title: 'How to vote?', text: 'Voting card, polling station, and how to fill in the ballot - step by step.' },
          ],
          links: [{ label: 'To the elections guide →', cardLabel: 'To guide' }],
        }
      }
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
    const payload = { ...data, _subject: `${card.formLabel || 'בקשת הצטרפות'} - ${card.title}` };
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
  <title>israelis.nl - {u($lang, 'tagline')}</title>
  <meta name="description" content={u($lang, 'metaDesc')} />
</svelte:head>

<!-- LANGUAGE SWITCHER -->
<div class="lang-bar">
  <button class="lang-btn" class:active={$lang === 'he'} on:click={() => setLang('he')}>HE</button>
  <button class="lang-btn" class:active={$lang === 'nl'} on:click={() => setLang('nl')}>NL</button>
  <button class="lang-btn" class:active={$lang === 'en'} on:click={() => setLang('en')}>EN</button>
</div>

<!-- HERO -->
<section class="hero">
  <div class="float-elements">
    <div class="float-el"></div>
    <div class="float-el"></div>
    <div class="float-el"></div>
  </div>
  <div class="hero-content">
    <h1 class="hero-logo">israelis<span class="dot">.</span>nl</h1>
    <p class="hero-tagline">{u($lang, 'tagline')}</p>
  </div>
  <div class="hero-scroll-hint">
    <span>{u($lang, 'discover')}</span>
    <div class="scroll-arrow"></div>
  </div>
</section>

<!-- PROJECTS -->
<div class="section-intro">
  <h2>{u($lang, 'sectionTitle')}</h2>
  <p>{u($lang, 'sectionDesc')}</p>
</div>

<div class="projects">
  {#each cards as card}
  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-static-element-interactions -->
  <div class="project-card {card.theme}" on:click={() => openCard(card)}>
    <span class="card-status {card.status === 'live' ? 'status-live' : 'status-soon'}">
      {card.status === 'live' ? (t($lang, card, 'tag') || u($lang, 'live')) : (t($lang, card, 'tag') || u($lang, 'soon'))}
    </span>
    <span class="card-emoji">{card.emoji}</span>
    <div class="card-category">{t($lang, card, 'category')}</div>
    <div class="card-title">{t($lang, card, 'title')}</div>
    <div class="card-desc">{t($lang, card, 'desc')}</div>
    <div style="display:flex; gap:0.8rem; margin-top:1.2rem; flex-wrap:wrap;">
      {#each card.links as link, i}
      <a href={link.href} class="card-link" style="margin-top:0;"
         target="_blank"
         rel="noopener"
         on:click|stopPropagation>
        {tLink($lang, card, i, 'cardLabel')} <span class="arrow">{arrow($lang)}</span>
      </a>
      {/each}
      {#if card.hasForm}
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <button class="card-link card-cta-btn" style="margin-top:0;" on:click|stopPropagation={() => openCard(card)}>
        {t($lang, card, 'ctaLabel') || card.ctaLabel} <span class="arrow">{arrow($lang)}</span>
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
        <img src={activeCard.banner} alt={t($lang, activeCard, 'title')} class="modal-banner" />
      {:else}
        <div class="modal-hero-inner">
          <span class="modal-status {activeCard.status === 'live' ? 'status-live' : 'status-soon'}" class:dark-badge={activeCard.darkHero}>
            {activeCard.status === 'live' ? (t($lang, activeCard, 'tag') || u($lang, 'live')) : (t($lang, activeCard, 'tag') || u($lang, 'soon'))}
          </span>
          <span class="modal-emoji">{activeCard.emoji}</span>
          <div class="modal-category" class:dark-text={activeCard.darkHero}>{t($lang, activeCard, 'category')}</div>
          <h2 class="modal-title" class:dark-text={activeCard.darkHero}>{t($lang, activeCard, 'title')}</h2>
          <p class="modal-desc" class:dark-text={activeCard.darkHero}>{t($lang, activeCard, 'desc')}</p>
        </div>
      {/if}
    </div>

    <!-- RIGHT/BOTTOM: BODY PANEL -->
    <div class="modal-body">
      <p class="modal-about">{t($lang, activeCard, 'about')}</p>

      <div class="modal-details">
        {#each activeCard.details as detail, idx}
        <div class="modal-detail-item">
          <span class="detail-icon">{detail.icon}</span>
          <div>
            <strong>{tDetail($lang, activeCard, idx).title}</strong>
            <p>{tDetail($lang, activeCard, idx).text}</p>
          </div>
        </div>
        {/each}
      </div>

      {#if activeCard.note || (activeCard.i18n?.[$lang]?.note)}
      <p class="modal-note">⚠️ {t($lang, activeCard, 'note')}</p>
      {/if}

      {#if activeCard.hasForm}
        <div class="modal-form-section">
          {#if formStatus[activeCard.id] === 'success'}
            <div class="form-success">
              <span class="success-icon">✅</span>
              <p>{u($lang, 'formSuccess')}</p>
            </div>
          {:else if formStatus[activeCard.id] === 'loading'}
            <div class="form-success">
              <span class="success-icon">⏳</span>
              <p>{u($lang, 'formLoading')}</p>
            </div>
          {:else}
            <p class="form-label">{t($lang, activeCard, 'formLabel') || u($lang, 'joinGroup')}</p>
            {#if activeCard.useGoogleSignIn}
              <div id="google-signin-btn-{activeCard.id}" class="google-signin-container"></div>
            {/if}
            {#if !activeCard.useGoogleSignIn}
            <form class="join-form" on:submit|preventDefault={() => submitForm(activeCard)}>
              <input type="text" placeholder={u($lang, 'fullName')} bind:value={formValues[activeCard.id].name} required />
              {#if (activeCard.formFields || ['name', 'phone']).includes('phone')}
              <input type="tel" placeholder={u($lang, 'phone')} bind:value={formValues[activeCard.id].phone} required />
              {/if}
              <button type="submit" class="submit-btn">{u($lang, 'joinGroup')} {arrow($lang)}</button>
            </form>
            {/if}
            {#if formStatus[activeCard.id] === 'error'}
              <p class="form-error">{u($lang, 'formError')}</p>
            {/if}
          {/if}
        </div>
      {:else if activeCard.links.length > 0}
      <div class="modal-links">
        {#each activeCard.links as link, i}
        <a href={link.href} class="modal-link-btn" class:secondary={i > 0}
           target="_blank"
           rel="noopener">
          {tLink($lang, activeCard, i, 'label')}
        </a>
        {/each}
      </div>
      {:else}
      <p class="modal-coming-soon">🔜 {u($lang, 'comingSoon')}</p>
      {/if}
    </div>

  </div>
</div>
{/if}

<style>
  /* ===== LANGUAGE SWITCHER ===== */
  .lang-bar {
    position: fixed;
    top: 1rem;
    left: 1rem;
    z-index: 1100;
    display: flex;
    gap: 0.3rem;
    background: rgba(255,255,255,0.15);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    padding: 0.3rem;
    border-radius: 100px;
    box-shadow: 0 2px 12px rgba(0,0,0,0.15);
    border: 1px solid rgba(255,255,255,0.2);
  }
  .lang-btn {
    background: transparent;
    border: none;
    color: rgba(255,255,255,0.8);
    font-family: 'Heebo', sans-serif;
    font-size: 0.75rem;
    font-weight: 700;
    padding: 0.35rem 0.65rem;
    border-radius: 100px;
    cursor: pointer;
    transition: all 0.2s;
    letter-spacing: 0.05em;
  }
  .lang-btn:hover {
    background: rgba(255,255,255,0.15);
    color: white;
  }
  .lang-btn.active {
    background: var(--orange);
    color: white;
  }

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

  /* Close button - top corner of modal */
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
  :global([dir="ltr"]) .modal-close {
    right: auto;
    left: 1rem;
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
  /* Single CTA - full width */
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
  :global([dir="ltr"]) .modal-note {
    border-right: none;
    border-left: 3px solid rgba(0,0,0,0.15);
    border-radius: 8px 0 0 8px;
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
    :global([dir="ltr"]) .modal-close { right: auto; left: 0.75rem; }
  }
</style>
