<svelte:head>
  <title>אז מה עושים היום — israelis.nl</title>
  <meta name="description" content="המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש" />
</svelte:head>

<section class="hayom-hero">
  <div class="hayom-hero-inner">
    <a href="/" class="back-link"><span class="back-arrow">→</span> israelis.nl</a>
    <div class="hayom-banner">
      <img src="/hayom-banner.jpeg" alt="אז מה עושים היום" />
    </div>
    <h1>אז מה עושים היום</h1>
    <p class="hayom-tagline">המלצות לאירועים ופעילויות לילדים בהולנד — כל סוף שבוע מחדש</p>
    <a href="https://www.facebook.com/groups/907652610513541" class="fb-cta" target="_blank" rel="noopener">
      <span class="fb-icon">f</span>
      הצטרפו לקבוצה בפייסבוק
    </a>
  </div>
</section>

<section class="hayom-content">
  <div class="hayom-lead">
    <p class="lead-text">הורים לילדים בהולנד? מחפשים המלצות מגניבות לאירועים ממוקדים (בעיקר בסופ"שים וחופשים)? הגעתם למקום הנכון!</p>
  </div>

  <div class="hayom-story">
    <h2>הרקע</h2>
    <p>
      בעיה שלמרבה ההפתעה טרם נפתרה על-ידי גוגל / פייסבוק / בינה מלאכותית — נוגעת לנגישות למידע אודות אירועים מקומיים.
      <strong>לדעת בקלות מה מתקיים מתי והיכן?</strong>
    </p>
    <p>
      בהולנד, שפע של אירועי תרבות, הווי בידור ובילוי, מותאמים לילדים מדי יום ובוודאי שבחופשות ובסופי שבוע — ואין (למיטב ידיעתי) מקום אחד שמאגד וסוקר אותם. הקבוצה הזאת היא הפתרון.
    </p>
    <p>
      כמי שמתגורר בהולנד זה מספר שנים, לקחתי על עצמי את המשימה לאגד ולרכז אירועים מעניינים ואיכותיים בסביבה מדי סוף שבוע. בתחילה זה היה רק עבור משפחה וחברים קרובים (חפירות אינסופיות בוואטסאפ), ולאחרונה — ולאור ביקוש גובר — החלטתי לפתוח את הקבוצה ולשתף בהמלצות גם במעגלים מורחבים.
    </p>
  </div>

  <div class="hayom-notes">
    <h2>הערות לסדר</h2>
    <ol>
      <li>אינני לוקח אחריות על המידע המסוקר — ועל השלכות כאלה או אחרות של האירועים המשותפים ו/או על ביטולם. אנא בדקו בקישורים וקחו החלטות מושכלות.</li>
      <li>מדובר באירועים מקומיים ולא באטרקציות כלליות. זאת אומרת, שלא אמליץ כאן על מוזיאון נמו או על חיטהורן (המלצות שכאלה יש בלי סוף ברחבי הרשת), אלא על אירועים ייחודיים וחד-פעמיים המתקיימים במקום מסויים ובתאריך ספציפי.</li>
    </ol>
    <p class="closing">אשמח מאד לכל פיסת משוב, בקשות ועצות לשיפור. <strong>צאו ותהנו!</strong></p>
  </div>

  <div class="hayom-cta-bottom">
    <a href="https://www.facebook.com/groups/907652610513541" class="fb-cta" target="_blank" rel="noopener">
      <span class="fb-icon">f</span>
      לקבוצת הפייסבוק
    </a>
  </div>
</section>

<style>
  .hayom-hero {
    background: linear-gradient(135deg, #21A366 0%, #2ECC71 100%);
    color: white;
    padding: 3rem 2rem 4rem;
    text-align: center;
  }

  .hayom-hero-inner {
    max-width: 800px;
    margin: 0 auto;
  }

  .back-link {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    color: rgba(255,255,255,0.7);
    text-decoration: none;
    font-size: 0.9rem;
    margin-bottom: 2rem;
    transition: color 0.2s;
  }
  .back-link:hover { color: white; }
  .back-arrow { font-size: 1.1em; }

  .hayom-banner {
    border-radius: 16px;
    overflow: hidden;
    margin: 0 auto 2rem;
    max-width: 600px;
    box-shadow: 0 20px 60px rgba(0,0,0,0.2);
  }
  .hayom-banner img {
    width: 100%;
    height: auto;
    display: block;
  }

  h1 {
    font-family: 'Secular One', sans-serif;
    font-size: clamp(2rem, 5vw, 3.5rem);
    margin-bottom: 0.5rem;
  }

  .hayom-tagline {
    font-size: clamp(1rem, 2.5vw, 1.3rem);
    opacity: 0.85;
    margin-bottom: 2rem;
    line-height: 1.5;
  }

  .fb-cta {
    display: inline-flex;
    align-items: center;
    gap: 0.6rem;
    background: #1877F2;
    color: white;
    text-decoration: none;
    padding: 0.9rem 2rem;
    border-radius: 50px;
    font-weight: 700;
    font-size: 1rem;
    transition: transform 0.2s, box-shadow 0.2s;
    box-shadow: 0 4px 20px rgba(24,119,242,0.4);
  }
  .fb-cta:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 30px rgba(24,119,242,0.5);
  }

  .fb-icon {
    font-family: serif;
    font-size: 1.2em;
    font-weight: 900;
  }

  .hayom-content {
    max-width: 720px;
    margin: 0 auto;
    padding: 3rem 2rem 5rem;
  }

  .lead-text {
    font-size: 1.2rem;
    line-height: 1.7;
    color: #21A366;
    font-weight: 600;
    background: rgba(33,163,102,0.08);
    border-right: 4px solid #21A366;
    padding: 1.2rem 1.5rem;
    border-radius: 0 12px 12px 0;
    margin-bottom: 2.5rem;
  }

  .hayom-story, .hayom-notes {
    margin-bottom: 2.5rem;
  }

  h2 {
    font-family: 'Secular One', sans-serif;
    font-size: 1.5rem;
    color: #1B2A4A;
    margin-bottom: 1rem;
  }

  p {
    font-size: 1rem;
    line-height: 1.8;
    color: #5a5a7a;
    margin-bottom: 1rem;
  }
  p strong { color: #1a1a2e; }

  ol {
    padding-right: 1.5rem;
    color: #5a5a7a;
    line-height: 1.8;
  }
  ol li {
    margin-bottom: 0.8rem;
    font-size: 1rem;
  }

  .closing {
    margin-top: 1.5rem;
    font-size: 1.05rem;
  }

  .hayom-cta-bottom {
    text-align: center;
    padding-top: 1rem;
  }

  @media (max-width: 600px) {
    .hayom-hero { padding: 2rem 1.2rem 3rem; }
    .hayom-content { padding: 2rem 1.2rem 4rem; }
  }
</style>
