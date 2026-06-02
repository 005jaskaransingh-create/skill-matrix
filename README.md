/* ==========================================================================
   SKILL MATRIX - INTERACTIVE PLATFORM MOTOR (app.js)
   Engineered for seamless animations, micro-interactions, and visual wow-factor.
   ========================================================================== */

document.addEventListener('DOMContentLoaded', () => {
  
  // 1. MOBILE NAVBAR MENU TOGGLE
  const menuToggle = document.getElementById('menu-toggle');
  const navLinks = document.getElementById('nav-links');
  
  if (menuToggle && navLinks) {
    menuToggle.addEventListener('click', () => {
      menuToggle.classList.toggle('active');
      navLinks.classList.toggle('active');
    });

    // Close mobile menu on clicking any navigation link
    document.querySelectorAll('.nav-link').forEach(link => {
      link.addEventListener('click', () => {
        menuToggle.classList.remove('active');
        navLinks.classList.remove('active');
      });
    });
  }

  // Navbar sticky scroll shadow
  const navbar = document.getElementById('navbar');
  window.addEventListener('scroll', () => {
    if (window.scrollY > 40) {
      navbar.classList.add('scrolled');
    } else {
      navbar.classList.remove('scrolled');
    }
  });


  // 2. CINEMATIC CURSOR FOLLOW SPOTLIGHT
  const spotlight = document.getElementById('spotlight');
  document.addEventListener('mousemove', (e) => {
    const x = e.clientX;
    const y = e.clientY;
    document.documentElement.style.setProperty('--mouse-x', `${x}px`);
    document.documentElement.style.setProperty('--mouse-y', `${y}px`);
  });


  // 3. REACTIVE PARTICLES GRID CANVAS ENGINE
  const canvas = document.getElementById('particle-canvas');
  const ctx = canvas.getContext('2d');
  
  let particlesArray = [];
  const maxDistance = 100;
  let mouse = { x: null, y: null, radius: 150 };

  // Track mouse coordinates on window
  window.addEventListener('mousemove', (e) => {
    mouse.x = e.clientX;
    mouse.y = e.clientY;
  });
  window.addEventListener('mouseout', () => {
    mouse.x = null;
    mouse.y = null;
  });

  // Resize canvas dynamically
  function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    initParticles();
  }
  window.addEventListener('resize', resizeCanvas);

  class Particle {
    constructor(x, y, vx, vy, size, color) {
      this.x = x;
      this.y = y;
      this.vx = vx;
      this.vy = vy;
      this.size = size;
      this.color = color;
    }
    draw() {
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fillStyle = this.color;
      ctx.fill();
    }
    update() {
      // Bounce off borders
      if (this.x > canvas.width || this.x < 0) this.vx = -this.vx;
      if (this.y > canvas.height || this.y < 0) this.vy = -this.vy;

      this.x += this.vx;
      this.y += this.vy;

      // Mouse interactivity (slight attraction/repulsion)
      if (mouse.x != null && mouse.y != null) {
        let dx = this.x - mouse.x;
        let dy = this.y - mouse.y;
        let distance = Math.hypot(dx, dy);
        if (distance < mouse.radius) {
          let force = (mouse.radius - distance) / mouse.radius;
          this.x += (dx / distance) * force * 1.5;
          this.y += (dy / distance) * force * 1.5;
        }
      }
      this.draw();
    }
  }

  function initParticles() {
    particlesArray = [];
    // Number of particles dependent on canvas width
    let numberOfParticles = Math.floor((canvas.width * canvas.height) / 18000);
    numberOfParticles = Math.min(numberOfParticles, 85); // cap for peak execution performance

    const colors = [
      'rgba(0, 242, 254, 0.15)', // Cyan
      'rgba(0, 82, 255, 0.12)',  // Electric Blue
      'rgba(124, 58, 237, 0.12)'  // Violet
    ];

    for (let i = 0; i < numberOfParticles; i++) {
      let size = Math.random() * 2 + 1.5;
      let x = Math.random() * canvas.width;
      let y = Math.random() * canvas.height;
      let vx = (Math.random() - 0.5) * 0.4;
      let vy = (Math.random() - 0.5) * 0.4;
      let color = colors[Math.floor(Math.random() * colors.length)];
      particlesArray.push(new Particle(x, y, vx, vy, size, color));
    }
  }

  // Draw lines connecting nearby particles
  function connectParticles() {
    for (let a = 0; a < particlesArray.length; a++) {
      for (let b = a; b < particlesArray.length; b++) {
        let dx = particlesArray[a].x - particlesArray[b].x;
        let dy = particlesArray[a].y - particlesArray[b].y;
        let distance = Math.hypot(dx, dy);

        if (distance < maxDistance) {
          let opacity = (1 - (distance / maxDistance)) * 0.12;
          ctx.strokeStyle = `rgba(0, 242, 254, ${opacity})`;
          ctx.lineWidth = 0.8;
          ctx.beginPath();
          ctx.moveTo(particlesArray[a].x, particlesArray[a].y);
          ctx.lineTo(particlesArray[b].x, particlesArray[b].y);
          ctx.stroke();
        }
      }
    }
  }

  function animateParticles() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    for (let i = 0; i < particlesArray.length; i++) {
      particlesArray[i].update();
    }
    connectParticles();
    requestAnimationFrame(animateParticles);
  }

  // Initial trigger for canvas
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  initParticles();
  animateParticles();


  // 4. PROGRAM CARDS - LOCAL CURSOR GLOW FOLLOW
  const glowCards = document.querySelectorAll('[data-glow]');
  glowCards.forEach(card => {
    card.addEventListener('mousemove', (e) => {
      const rect = card.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      card.style.setProperty('--glow-x', `${x}px`);
      card.style.setProperty('--glow-y', `${y}px`);
    });
  });


  // 5. INTERSECTION OBSERVER FOR FADING REVEALS & STAT RUNNERS
  const revealElements = document.querySelectorAll('.scroll-reveal');
  const observerOptions = {
    root: null,
    threshold: 0.12,
    rootMargin: '0px'
  };

  const revealObserver = new IntersectionObserver((entries, observer) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('active');
        
        // Trigger specific sub-elements animation once parent section is visible
        if (entry.target.id === 'why-us') {
          const bar = document.getElementById('salary-progress-bar-fill');
          if (bar) bar.style.width = '100%';
        }
        
        observer.unobserve(entry.target);
      }
    });
  }, observerOptions);

  revealElements.forEach(el => revealObserver.observe(el));

  // Numeric Stats Counters Animation on Hero
  const statsNumElements = document.querySelectorAll('.stat-num');
  
  function countUp(el) {
    const target = parseInt(el.getAttribute('data-val'));
    const duration = 1800; // ms
    let start = 0;
    const increment = target / (duration / 16); // ~60fps
    
    const counter = setInterval(() => {
      start += increment;
      if (start >= target) {
        clearInterval(counter);
        if (target === 50000) {
          el.textContent = '50K+';
        } else if (target === 120) {
          el.textContent = '120+';
        } else {
          el.textContent = '95%';
        }
      } else {
        if (target === 50000) {
          el.textContent = Math.floor(start / 1000) + 'K+';
        } else {
          el.textContent = Math.floor(start) + (target === 95 ? '%' : '+');
        }
      }
    }, 16);
  }

  // Trigger counters after a small delay on page load
  setTimeout(() => {
    statsNumElements.forEach(num => countUp(num));
  }, 400);


  // 6. SYLLABUS EXPANSION DRAWERS
  const syllabusToggles = document.querySelectorAll('.btn-syllabus-toggle');
  syllabusToggles.forEach(toggle => {
    toggle.addEventListener('click', (e) => {
      e.stopPropagation();
      const targetId = toggle.getAttribute('data-target');
      const drawer = document.getElementById(targetId);
      
      toggle.classList.toggle('active');
      
      if (drawer.style.height === '' || drawer.style.height === '0px') {
        // Expand
        const contentHeight = drawer.querySelector('.syllabus-content').scrollHeight + 20;
        drawer.style.height = `${contentHeight}px`;
      } else {
        // Collapse
        drawer.style.height = '0px';
      }
    });
  });


  // 7. INTERACTIVE CERTIFICATE GENERATOR
  const certInput = document.getElementById('cert-name-input');
  const certDisplay = document.getElementById('cert-display-name');

  if (certInput && certDisplay) {
    certInput.addEventListener('input', (e) => {
      const name = e.target.value.trim().toUpperCase();
      if (name === '') {
        certDisplay.textContent = 'FUTURE GRADUATE';
      } else {
        certDisplay.textContent = name;
      }
    });
  }


  // 8. INTERACTIVE LEARNING HUB PLAYGROUND (TABS & SIMULATOR)
  const tabs = document.querySelectorAll('.play-tab');
  const contents = document.querySelectorAll('.play-content');
  
  tabs.forEach(tab => {
    tab.addEventListener('click', () => {
      tabs.forEach(t => t.classList.remove('active'));
      contents.forEach(c => c.classList.remove('active'));
      
      tab.classList.add('active');
      const targetId = tab.getAttribute('data-tab');
      document.getElementById(targetId).classList.add('active');

      // If switching to the Analytics Tab, render the Canvas Graph
      if (targetId === 'tab-radar') {
        setTimeout(drawAnalyticsChart, 100);
      }
    });
  });

  // STREAMING CODE SIMULATOR (AI Assistant Playground)
  const solutionCodes = {
    agent: `// Creating Autonomous Newsletter Summarizer Agent
import { Agent, Crew } from 'crewai';
import { PineconeClient } from 'pinecone-client';

const vectorDB = new PineconeClient({
  apiKey: "sm_live_9a382b"
});

const summarizerAgent = new Agent({
  role: 'Newsletter Compiler',
  goal: 'Read incoming AI drafts & create crisp summaries',
  backstory: 'A technical summarizer trained on Linear & Apple AI releases',
  verbose: true,
  tools: [vectorDB.searchTool()]
});

export async function runSummarizerPipeline(incomingFeed) {
  console.log("Analyzing text nodes...");
  const vectorMatches = await vectorDB.query({ vector: incomingFeed });
  return await summarizerAgent.execute({ context: vectorMatches });
}`,
    scraping: `// Build Dynamic Scraper with LLM Synthesizer
const puppeteer = require('puppeteer-extra');
const { OpenAI } = require('openai');

const openai = new OpenAI();

async function scrapeAndExtract(targetURL) {
  const browser = await puppeteer.launch({ headless: true });
  const page = await browser.newPage();
  
  console.log("Accessing " + targetURL);
  await page.goto(targetURL, { waitUntil: 'domcontentloaded' });
  
  const rawText = await page.evaluate(() => document.body.innerText);
  await browser.close();
  
  const synthesis = await openai.chat.completions.create({
    model: "gpt-4o-mini",
    messages: [
      { role: "system", content: "Extract digital tools and salary indicators." },
      { role: "user", content: rawText }
    ]
  });
  
  return synthesis.choices[0].message.content;
}`,
    db: `// Pinecone Vector Database Integration & RAG Pipeline
import { Pinecone } from '@pinecone-database/pinecone';

const pinecone = new Pinecone({ apiKey: process.env.PINECONE_API_KEY });
const index = pinecone.Index('skill-matrix-embeddings');

export async function embedAndUpsert(documentId, textPayload) {
  console.log("Generating Pinecone vector embedding hashes...");
  const embedding = await generateTextEmbedding(textPayload);
  
  await index.upsert([{
    id: documentId,
    values: embedding,
    metadata: { source: 'RAG_System', time: Date.now() }
  }]);
  
  console.log("✔ Vectors safely synchronized in Pinecone DB cluster.");
}`
  };

  const promptBtns = document.querySelectorAll('.assistant-prompt-btn');
  const codeArea = document.getElementById('assistant-streaming-code');
  const typingStatus = document.getElementById('typing-status-text');
  
  let typingTimer = null;

  function streamCode(key) {
    if (typingTimer) clearInterval(typingTimer);
    
    const text = solutionCodes[key];
    codeArea.textContent = '';
    typingStatus.textContent = 'Orchestrating AI response...';
    
    let index = 0;
    const speed = 10; // Char writing speed
    
    typingTimer = setInterval(() => {
      if (index < text.length) {
        codeArea.textContent += text.charAt(index);
        index++;
        // Keep scrolling to bottom of code screen
        codeArea.parentElement.scrollTop = codeArea.parentElement.scrollHeight;
      } else {
        clearInterval(typingTimer);
        typingStatus.textContent = 'Solution compiled successfully ✓';
      }
    }, speed);
  }

  // Connect simulation buttons
  promptBtns.forEach(btn => {
    btn.addEventListener('click', () => {
      promptBtns.forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      const typeKey = btn.getAttribute('data-prompt');
      streamCode(typeKey);
    });
  });

  // Start with default simulator response on load
  streamCode('agent');

  // Hero section terminal code writer animation
  const heroTerm = document.getElementById('hero-terminal-text');
  if (heroTerm) {
    const heroCode = `// Running neural matrix sync...
// Loading dataset: AI_High_Income_Skills_2026.csv
// Running LangChain multi-agent orchestration...
// Output: System Online. Ready to upgrade your future.`;
    
    heroTerm.textContent = '';
    let index = 0;
    const hTimer = setInterval(() => {
      if (index < heroCode.length) {
        heroTerm.textContent += heroCode.charAt(index);
        index++;
      } else {
        clearInterval(hTimer);
      }
    }, 15);
  }


  // 9. HIGH END CANVAS LINE GRAPH WRITER (Analytics Tab)
  function drawAnalyticsChart() {
    const chartCanvas = document.getElementById('analytics-canvas-chart');
    if (!chartCanvas) return;
    
    const c = chartCanvas.getContext('2d');
    const width = chartCanvas.width;
    const height = chartCanvas.height;

    // Clear previous drawing
    c.clearRect(0, 0, width, height);

    // Draw background grid lines
    c.strokeStyle = 'rgba(255,255,255,0.05)';
    c.lineWidth = 1;
    
    const rows = 4;
    const cols = 6;
    
    for (let i = 0; i <= rows; i++) {
      let y = 30 + (i * (height - 60) / rows);
      c.beginPath();
      c.moveTo(40, y);
      c.lineTo(width - 20, y);
      c.stroke();
    }
    for (let j = 0; j < cols; j++) {
      let x = 40 + (j * (width - 60) / (cols - 1));
      c.beginPath();
      c.moveTo(x, 30);
      c.lineTo(x, height - 30);
      c.stroke();
    }

    // Chart curve coordinates (6 weeks)
    // Points represent percentages from bottom
    const dataPoints = [0.15, 0.32, 0.48, 0.65, 0.82, 0.95];
    const points = [];
    
    for (let k = 0; k < dataPoints.length; k++) {
      let x = 40 + (k * (width - 60) / (dataPoints.length - 1));
      let y = height - 30 - (dataPoints[k] * (height - 60));
      points.push({ x, y });
    }

    // Draw glowing line chart path (bezier smooth curved fitting)
    c.strokeStyle = '#00F2FE';
    c.lineWidth = 3.5;
    c.shadowColor = 'rgba(0, 242, 254, 0.6)';
    c.shadowBlur = 12;
    
    c.beginPath();
    c.moveTo(points[0].x, points[0].y);
    for (let m = 0; m < points.length - 1; m++) {
      let xc = (points[m].x + points[m + 1].x) / 2;
      let yc = (points[m].y + points[m + 1].y) / 2;
      c.quadraticCurveTo(points[m].x, points[m].y, xc, yc);
    }
    c.lineTo(points[points.length - 1].x, points[points.length - 1].y);
    c.stroke();
    
    // Reset shadows for dots
    c.shadowBlur = 0;

    // Draw bottom fill gradient
    const gradient = c.createLinearGradient(0, 30, 0, height - 30);
    gradient.addColorStop(0, 'rgba(0, 242, 254, 0.25)');
    gradient.addColorStop(1, 'rgba(124, 58, 237, 0.0)');
    
    c.fillStyle = gradient;
    c.beginPath();
    c.moveTo(points[0].x, height - 30);
    c.lineTo(points[0].x, points[0].y);
    for (let n = 0; n < points.length - 1; n++) {
      let xc = (points[n].x + points[n + 1].x) / 2;
      let yc = (points[n].y + points[n + 1].y) / 2;
      c.quadraticCurveTo(points[n].x, points[n].y, xc, yc);
    }
    c.lineTo(points[points.length - 1].x, points[points.length - 1].y);
    c.lineTo(points[points.length - 1].x, height - 30);
    c.closePath();
    c.fill();

    // Draw circular indicators with labels
    c.fillStyle = '#fff';
    c.strokeStyle = '#7C3AED';
    c.lineWidth = 2;
    
    for (let p = 0; p < points.length; p++) {
      c.beginPath();
      c.arc(points[p].x, points[p].y, 5, 0, Math.PI * 2);
      c.fill();
      c.stroke();
      
      // Draw small text label for week speed
      c.fillStyle = 'rgba(255,255,255,0.7)';
      c.font = '9px var(--font-heading)';
      c.fillText(`Wk ${p + 1}`, points[p].x - 10, height - 12);
    }
  }


  // 10. SUCCESS TESTIMONIALS SLIDER CAROUSEL
  const slides = document.querySelectorAll('.carousel-slide');
  const dots = document.querySelectorAll('.carousel-dot');
  const prevBtn = document.getElementById('carousel-prev');
  const nextBtn = document.getElementById('carousel-next');
  
  let currentSlide = 0;
  let slideTimer = null;

  function showSlide(index) {
    if (index >= slides.length) currentSlide = 0;
    else if (index < 0) currentSlide = slides.length - 1;
    else currentSlide = index;

    slides.forEach(slide => slide.classList.remove('active'));
    dots.forEach(dot => dot.classList.remove('active'));

    slides[currentSlide].classList.add('active');
    dots[currentSlide].classList.add('active');
  }

  function startAutoSlide() {
    if (slideTimer) clearInterval(slideTimer);
    slideTimer = setInterval(() => {
      showSlide(currentSlide + 1);
    }, 6000);
  }

  if (prevBtn && nextBtn) {
    prevBtn.addEventListener('click', () => {
      showSlide(currentSlide - 1);
      startAutoSlide();
    });
    nextBtn.addEventListener('click', () => {
      showSlide(currentSlide + 1);
      startAutoSlide();
    });

    dots.forEach((dot, index) => {
      dot.addEventListener('click', () => {
        showSlide(index);
        startAutoSlide();
      });
    });

    startAutoSlide();
  }


  // 11. PRICING MONTHLY/YEARLY DYNAMIC TOGGLE
  const pricingBtn = document.getElementById('pricing-toggle-btn');
  const starterPrice = document.getElementById('price-starter');
  const proPrice = document.getElementById('price-pro');
  const elitePrice = document.getElementById('price-elite');
  const starterPeriod = document.getElementById('period-starter');
  const proPeriod = document.getElementById('period-pro');
  const elitePeriod = document.getElementById('period-elite');
  const labelMonthly = document.getElementById('label-monthly');
  const labelYearly = document.getElementById('label-yearly');

  if (pricingBtn) {
    pricingBtn.addEventListener('click', () => {
      pricingBtn.classList.toggle('active');
      const isYearly = pricingBtn.classList.contains('active');

      if (isYearly) {
        labelYearly.classList.add('text-cyan');
        labelMonthly.classList.remove('text-cyan');
        
        animatePriceChange(starterPrice, starterPrice.getAttribute('data-yearly'));
        animatePriceChange(proPrice, proPrice.getAttribute('data-yearly'));
        animatePriceChange(elitePrice, elitePrice.getAttribute('data-yearly'));

        starterPeriod.textContent = '/mo, billed annually';
        proPeriod.textContent = '/mo, billed annually';
        elitePeriod.textContent = '/mo, billed annually';
      } else {
        labelMonthly.classList.add('text-cyan');
        labelYearly.classList.remove('text-cyan');

        animatePriceChange(starterPrice, starterPrice.getAttribute('data-monthly'));
        animatePriceChange(proPrice, proPrice.getAttribute('data-monthly'));
        animatePriceChange(elitePrice, elitePrice.getAttribute('data-monthly'));

        starterPeriod.textContent = '/mo';
        proPeriod.textContent = '/mo';
        elitePeriod.textContent = '/mo';
      }
    });
  }

  function animatePriceChange(el, newVal) {
    el.style.opacity = 0;
    el.style.transform = 'translateY(-10px)';
    
    setTimeout(() => {
      el.textContent = newVal;
      el.style.opacity = 1;
      el.style.transform = 'translateY(0)';
    }, 200);
  }


  // 12. FAQ ACCORDION SLIDE-AWAY INTERACTION
  const faqTriggers = document.querySelectorAll('.faq-trigger');
  
  faqTriggers.forEach(trigger => {
    trigger.addEventListener('click', () => {
      const parent = trigger.parentElement;
      const content = parent.querySelector('.faq-content');
      const isActive = parent.classList.contains('active');
      
      // Close all other FAQ elements first for premium singular feel
      document.querySelectorAll('.faq-item').forEach(item => {
        item.classList.remove('active');
        item.querySelector('.faq-content').style.maxHeight = '0px';
        item.querySelector('.faq-trigger').setAttribute('aria-expanded', 'false');
        item.querySelector('.faq-content').setAttribute('aria-hidden', 'true');
      });

      if (!isActive) {
        parent.classList.add('active');
        content.style.maxHeight = content.scrollHeight + 'px';
        trigger.setAttribute('aria-expanded', 'true');
        content.setAttribute('aria-hidden', 'false');
      }
    });
  });


  // 13. DYNAMIC MODAL ENGINE (DEMO & LEGALS)
  const modalContainer = document.getElementById('modal-container');
  const modalClose = document.getElementById('modal-close-btn');
  const modalSections = document.querySelectorAll('.modal-body-section');

  function openModal(sectionId) {
    if (modalContainer) {
      modalSections.forEach(sec => sec.classList.remove('active'));
      const activeSection = document.getElementById(`modal-${sectionId}-body`);
      
      if (activeSection) {
        activeSection.classList.add('active');
        modalContainer.style.display = 'flex';
        document.body.style.overflow = 'hidden'; // stop backing scrolling
      }
    }
  }

  function closeModal() {
    if (modalContainer) {
      modalContainer.style.display = 'none';
      document.body.style.overflow = 'auto';
    }
  }

  // Hook watch demo buttons
  const watchDemoBtn = document.getElementById('demo-btn');
  if (watchDemoBtn) {
    watchDemoBtn.addEventListener('click', () => openModal('video'));
  }

  // Hook footer legal buttons
  document.querySelectorAll('.btn-modal-trigger').forEach(btn => {
    btn.addEventListener('click', (e) => {
      e.preventDefault();
      const type = btn.getAttribute('data-modal');
      openModal(type);
    });
  });

  if (modalClose) {
    modalClose.addEventListener('click', closeModal);
  }

  // Close modal clicking dark overlay backdrop
  if (modalContainer) {
    modalContainer.addEventListener('click', (e) => {
      if (e.target === modalContainer) {
        closeModal();
      }
    });
  }


  // 14. DYNAMIC EMAIL INTRO ANCHOR ON FINAL CTA
  const ctaBtn = document.getElementById('cta-email-btn');
  const ctaInput = document.getElementById('cta-email-input');

  if (ctaBtn && ctaInput) {
    ctaBtn.addEventListener('click', () => {
      const email = ctaInput.value.trim();
      if (email === '' || !email.includes('@')) {
        alert('Please enter a valid cryptographic email address.');
      } else {
        ctaBtn.innerHTML = 'Booking Call...';
        ctaBtn.style.pointerEvents = 'none';
        
        setTimeout(() => {
          alert(`Success! We have queued a technical roadmap invite to: ${email}. Look out for the Skill Matrix scheduling link.`);
          ctaInput.value = '';
          ctaBtn.innerHTML = 'Book Free Consultation';
          ctaBtn.style.pointerEvents = 'auto';
        }, 1500);
      }
    });
  }

});
