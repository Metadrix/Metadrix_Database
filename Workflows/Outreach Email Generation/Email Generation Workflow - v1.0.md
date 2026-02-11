[📁 Explore](obsidian://open?vault=Metadrix_Database&file=📁%20Explore) > [Workflows](obsidian://open?vault=Metadrix_Database&file=Workflows) > [Email Generation](obsidian://open?vault=Metadrix_Database&file=Email%20Generation) > Email Generation Workflow - v1.0
# #double #grey
## Pure AI-Based Workflow 
## **Recommended Workflow Architecture**

```
┌─────────────────┐
│ Upload .md files│
│   (Client +     │
│  Competitors)   │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────┐
│  STEP 1: AI Document Parser     │
│  Tool: Claude/ChatGPT/Perplexity│
│  Action: Extract structured data│
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  STEP 2: AI Web Research Agent  │
│  Tool: Perplexity AI Pro/       │
│        ChatGPT Web Browse        │
│  Action: Fetch company intel    │
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  STEP 3: AI Email Generator     │
│  Tool: Claude 3.5 Sonnet         │
│  Action: Create personalized    │
│         email with comparisons   │
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  STEP 4: Email Formatter        │
│  Tool: Notion AI/Gamma/Canva    │
│  Action: Add images, format     │
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  STEP 5: Copy to Email Client   │
│  Gmail/Outlook with preview     │
└─────────────────────────────────┘
```

***

## **Tool Stack Recommendations**

| Step                       | Tool                                                | Why This?                                          | Cost                       |
| -------------------------- | --------------------------------------------------- | -------------------------------------------------- | -------------------------- |
| **MD Parsing**             | **Claude Sonnet**                                   | Best at extracting structured data from .md        | Free tier (5 msgs/hr)      |
| **Web Research**           | **Perplexity AI Pro Deep Research** (perplexity.ai) | Real-time web search + AI analysis                 | $20/mo OR use free ChatGPT |
| **Email Generation**       | **Claude Sonnet**                                   | Superior business writing + following instructions | Free tier                  |
| **Visual Assembly**        | **Gamma.app** OR **Notion AI**                      | AI-powered document formatting + images            | Free tier                  |
| **Alternative All-in-One** | **Relay.app** (AI workflows)                        | Combines all steps, no-code                        | $20/mo                     |

***

## **STEP-BY-STEP IMPLEMENTATION**

### **STEP 1: AI Document Parser (5 mins/client)**

1. **Tool:** Claude.ai 
2. **Action:** Upload your .md files and use this prompt:
3. **Inputs:** 
	1. Client company name
	2. Client company .md files
	3. Competitor company names
	4. Competitor company .md files

```
I'm uploading 2 markdown files:
1. CLIENT file: [Company Name] - my sales target
2. COMPETITOR file: [Company Name] - their competitor

TASK: Extract and structure this data in JSON format:

{
  "client": {
    "name": "",
    "website": "",
    "primary_email": "",
    "social_links": {"instagram": "", "linkedin": "", "facebook": ""},
    "seo_scores": {"seoptimer": "", "seositecheckup": "", "semrush": ""},
    "pain_points": [
      "List TOP 5 items marked as 'Poor' or 'Missing' under ## Pain Points section",
      "Translate each to business language like: 'Missing animations' → 'Your site lacks engaging interactive experiences that retain visitors'"
    ],
    "strengths": ["List any 'Good' items for balance"]
  },
  "competitor": {
    "name": "",
    "website": "",
    "seo_scores": {},
    "strengths": ["List their 'Good' items from Pain Points section"]
  }
}

RULES:
- Focus on checkboxes marked [x] next to Poor/Missing
- Keep pain points business-focused, NOT technical
- Prioritize UI/UX, animations, CTA, marketing issues
```

**Upload:** Your client.md + competitor1.md + competitor2.md

**Output:** Clean JSON you'll use in next steps.

***

### **STEP 2: AI Web Research Agent (3 mins/client)**

1. **Tool:** Perplexity AI Pro
2. **Action:** Use this prompt with the client name/website from Step 1:
3. **Inputs:** 
	1. Client name
	2. Company website link

```
You are a research assistant helping me personalize a sales email for a company.

Research the company below and RETURN ONLY VALID MINIFIED JSON.  
NO explanations, NO markdown, NO extra text.

COMPANY:
- Name: [CLIENT_NAME]
- Website: [CLIENT_WEBSITE]
- Main Competitor (for context, if known): [COMPETITOR_NAME or ""]

Your tasks:
1. Search the web for recent, reliable information about this company.
2. Summarize key points in a compact, structured JSON object.

JSON SCHEMA (STRICT):

{
  "company_name": "string",
  "website": "string",
  "recent_news": [
    {
      "title": "string",
      "summary": "1–2 sentence summary, plain language, no fluff",
      "date": "YYYY-MM-DD or null if unknown",
      "source": "domain only, e.g. 'economictimes.indiatimes.com'",
      "url": "string"
    }
  ],
  "flagship_projects": [
    {
      "name": "string",
      "location": "string or null",
      "type": "e.g. 'luxury residential', 'commercial', 'mixed-use'",
      "key_points": [
        "short bullet-like sentence about scale/positioning",
        "another short point if useful"
      ]
    }
  ],
  "market_position": {
    "positioning_summary": "2–3 short sentences on how the brand is perceived (premium/mass, niche, trusted, etc.)",
    "differentiators": [
      "short point about what makes them stand out",
      "another, if available"
    ],
    "risks_or_weaknesses": [
      "short point about any visible weakness / criticism / gap, if found",
      "another, if available"
    ]
  },
  "social_media": {
    "instagram": {
      "url": "string or null",
      "activity_level": "active | semi-active | dormant | not_found",
      "typical_content": "1–2 short sentences",
      "engagement_quality": "1–2 short sentences (e.g. 'good comments, low likes')"
    },
    "linkedin": {
      "url": "string or null",
      "activity_level": "active | semi-active | dormant | not_found",
      "typical_content": "1–2 short sentences",
      "engagement_quality": "1–2 short sentences"
    },
    "facebook": {
      "url": "string or null",
      "activity_level": "active | semi-active | dormant | not_found",
      "typical_content": "1–2 short sentences",
      "engagement_quality": "1–2 short sentences"
    },
    "youtube": {
      "url": "string or null",
      "activity_level": "active | semi-active | dormant | not_found",
      "typical_content": "1–2 short sentences",
      "engagement_quality": "1–2 short sentences"
    }
  },
  "industry_trends": {
    "segment": "e.g. 'luxury real estate Gurgaon', 'IT services', etc.",
    "key_trends": [
      "short bullet describing 1 important digital/marketing trend relevant to this company",
      "another trend",
      "max 4 items"
    ]
  },
  "comparison_to_competitor": {
    "competitor_name": "string or null",
    "summary": "2–3 short sentences comparing brand perception / digital presence",
    "notes": [
      "any concise, relevant comparison insight if available",
      "optional second point"
    ]
  }
}

RULES:
- Keep all text concise and factual.
- If a field is unknown, use null or an empty array [].
- Do NOT invent specific numbers (revenue, employee count, etc.) if not clearly stated by sources.
- Do NOT include any text outside the JSON.
- Return JSON as ONE SINGLE OBJECT (no comments, no trailing commas).

```


***

### **STEP 3: Master AI Email Generator (5 mins/client)**

**Tool:** Claude 

**Action:** Paste the JSON from Step 1 and Step 2 into this mega-prompt:

```
You are a senior sales consultant for an IT services company founded by BITS Pilani students. The company creates stunning, modern, high-conversion websites and apps with top-tier animations, UI/UX, and interactive experiences. Each founder has 2+ years of individual freelancing experience, and together you now offer agency-level quality at affordable prices in a market full of overpriced, suboptimal solutions.

Your goal: generate **short, sharp, visually structured emails** that feel personal, insight-driven, and tailored—not like mass campaigns.

---

## INPUT DATA (PROVIDED BY USER)

Attached files:

`[CLIENT DATA JSON]` - 

`[COMPETITOR DATA JSON]` - 

`[WEB RESEARCH INTEL JSON]` -

I will provide:

1. **CLIENT DATA (JSON)** – parsed from a markdown file:
    
    - Name, website, contact(s)
        
    - SEO scores
        
    - Social links
        
    - Top pain points (items marked Poor/Missing, translated into business language)
        
    - Key strengths
        
2. **COMPETITOR DATA (JSON)** – from a competitor markdown file:
    
    - Name, website
        
    - SEO scores
        
    - Key strengths (especially in website/UX/marketing)
        
3. **WEB RESEARCH INTEL (JSON)** – from web search:
    
    - Recent news/projects for the client
        
    - Market position and sentiment
        
    - Social media presence and engagement quality
        
    - Relevant industry/digital trends for their sector
        

I will paste them as:

CLIENT DATA (JSON):

json

`[CLIENT DATA JSON]`

COMPETITOR DATA (JSON):

json

`[COMPETITOR DATA JSON]`

WEB RESEARCH INTEL:

json

`[WEB RESEARCH INTEL JSON]`
    

---

## OVERALL CONSTRAINTS

- Max **600 words** total.
    
- **Markdown output only**, ready to paste:
    
    - Use `##` headings, **bold**, bullet points, and `---` separators.
        
- Emojis: **2–5 total**, primarily in headings (not sprayed through body text).
    
- Tone:
    
    - **Professional, conversational, analytical**, and respectful.
        
    - Feels like a **1:1 advisory email**, not a mass marketing blast.
        
- Assume **no technical knowledge**:
    
    - Explain funnels, signals, UI/UX impacts in clear, simple, business language.
        
- Focus on **clarity and compression**:
    
    - No fluff, no over-explaining.
        
    - Bullets should be **tight and to the point**.
        

---

## WHAT TO GENERATE

## 1. SUBJECT LINE OPTIONS

Generate **3 subject lines** that:

- Are specific to this client (use their company name or project name where natural).
    
- Focus on **business value / gaps / opportunity** (lost leads, digital gap, premium positioning).
    
- Do **not** sound spammy or clickbait.
    

Format exactly like:

- Subject 1: …
    
- Subject 2: …
    
- Subject 3: …
    

Example style (do NOT copy literally):

- “How [CLIENT COMPANY] Can Turn Its Launch Success Into 24/7 Digital Lead Generation”
    
- “[Client Name] vs [Competitor Name]: Premium Projects, But a Digital Experience That Lags Behind”
    

Then generate the **email body only** (no “EMAIL BODY” label).

---

## 2. INTRO / HOOK (COMPACT, HIGH-IMPACT)

Start the email as:

text

`Dear [First Name],`

Then write **2 compact paragraphs**, together around **140–180 words** total:

- **Paragraph 1**:
    
    - Start with **concrete, specific praise** based on WEB RESEARCH INTEL:
        
        - Recent flagship project(s), launch numbers, locations, scale, positioning (luxury, premium, etc.).
            
    - Acknowledge their **momentum / ambition** (pipeline, upcoming cities, targets).
        
    - Smoothly shift to the **digital challenge**: their website / digital funnel does not yet match the premium offline experience.
        
- **Paragraph 2**:
    
    - Briefly reference that you’ve **reviewed their site / SEO & UX** (like a quick audit done proactively).
        
    - Mention **1–2 hard facts**:
        
        - Their SEO grade vs competitor’s.
            
        - Obvious gaps (missing animations, weak CTAs, static feel, etc.).
            
    - Make the **business impact explicit**:
        
        - High-intent prospects leaking out.
            
        - Competitors capturing attention and enquiries that should be theirs.
            
    - End with a calm, confident line that this gap is **very fixable**.
        

Immediately after these paragraphs, insert:

text

`[IMAGE PLACEHOLDER: Simple SEO & Visibility Snapshot – Client vs Competitor] ---`

The intro should be **tight, readable, and clearly set up the “problem”** without sounding dramatic or salesy.

---

## 3. PROBLEM ANALYSIS (HEADINGS + BULLETS, NO FLUFF)

Create a parent heading:

text

`## **What's Holding [Client Name]'s Digital Presence Back**`

Then create **3 thematic sub-headings** (with emojis) such as:

text

`## **✨ First Impression & Trust Signals** ## **🏆 Conversion Path & Lead Capture** ## **🧭 Navigation, Branding & Consistency**`

Under **each sub-heading**, write **3–5 bullet points** with these rules:

- Each bullet is **1–2 sentences**, max **2–3 lines** visually.
    
- No long paragraphs; this section must be **instantly skimmable**.
    
- Each bullet should:
    
    - Call out **one concrete issue** from the client’s pain points (UI/UX, animations, CTAs, testimonials, forms, mobile nav, etc.).
        
    - Optionally, compare to **competitor by name** when relevant (e.g., “[Competitor Name] uses…”).
        
    - Always tie to a **business impact** (lost trust, confusion, lower enquiries, bounce, etc.).
        

Example style (adapt to actual data, do not copy):

text

`## **✨ First Impression & Trust Signals** - Your homepage feels more static than immersive—there are no subtle animations or interactive elements that immediately communicate the premium nature of your projects. - [Competitor Name] uses smooth animations, cohesive visuals, and storytelling that instantly feel more “presidential”, even before a visitor reads any copy. - There are few visible testimonials or case studies, so visitors arriving from search or ads must “guess” your track record for multi-crore decisions. ## **🏆 Conversion Path & Lead Capture** - Most key pages lack a clear “next step” like “Schedule a Private Tour”, “Download Brochure”, or “Chat on WhatsApp”, so high-intent visitors simply leave. - [Competitor Name] places clean, visible CTAs and forms where visitors naturally pause, gently nudging them into a conversation. - Contact forms and details are buried or missing from high-traffic pages, causing NRIs and HNIs to drop off before your sales team ever hears from them.`

Keep this whole section **zero-fluff, insight-dense, and very easy to skim**.

Add `---` after the last problem sub-section.

---

## 4. FUNNELS & SIGNALS (EDUCATION, BUT TIGHT)

Create a heading:

text

`## **Why Your Digital 'Funnels' and 'Signals' Matter More Than Ever**`

This section should be **short and to the point**, around **100–130 words total**, split into:

- **1 short paragraph on Funnels** (industry-specific).
    
- **1 short paragraph on Signals** (game theory concept).
    

Rules:

- No long storytelling, no fluff.
    
- Each paragraph: **3–5 concise sentences**.
    

Content guidance:

- **Funnels (industry-tuned)**:
    
    - Describe the core journey using the client’s industry (e.g., luxury real estate = discover → explore projects → shortlist → enquire/visit).
        
    - Point out **where their funnel leaks** based on earlier bullets (no CTAs, low engagement, missing trust cues).
        
- **Signals (game theory)**:
    
    - Define “signals” simply as cues that tell prospects “this brand is serious and premium.”
        
    - Give **3–4 examples** of digital signals relevant here (speed, animations, testimonials, consistent visuals, responsive mobile).
        
    - Connect it back to why **competitors with stronger signals feel safer** to high-ticket buyers.
        

You may use **one short bullet list** inside this section if it improves clarity, but keep the overall word count tight and focused.

End with `---`.

---

## 5. HOW WE CAN HELP (OUR EXPERTISE)

Create a heading:

text

`## **How We Can Help: Our Expertise**`

Write **1–2 short paragraphs**, together around **80–110 words**, that:

- Clearly state **what you do**:
    
    - Modern, interactive websites/apps.
        
    - Smooth animations, clean UI/UX.
        
    - Funnels and lead flows tailored to their industry.
        
- Bring in **BITS Pilani** subtly:
    
    - Experienced team of BITS Pilani students/alumni.
        
    - 2+ years of freelancing experience each for premium brands.
        
- Differentiate from agencies:
    
    - Agency-level quality.
        
    - More flexible and affordable due to lean structure.
        
- Tie it back to **their specific situation**:
    
    - Align digital experience with premium offline brand.
        
    - Tighten funnels, strengthen trust signals, improve lead capture.
        

This section should feel **credible, calm, and confident**, not hyped.

End with `---`.

---

## 6. CTA (PERSONAL, STRONG, BUT NOT SALESY)

Create a heading:

text

`## **Let’s Talk – No Pressure, Just Ideas**`

Write **one short closing paragraph (40–70 words)** that:

- Feels like a **personal note**, not a campaign.
    
- Emphasizes:
    
    - You already have a few **concrete, tailored ideas** for them.
        
    - You’re happy to walk them through **what could be changed in the next 30 days**.
        
- Invites a **quick 5–10 minute call**, but:
    
    - No “Reply with ‘KEYWORD’” mechanics.
        
    - No “first X people get…” or promotional language.
        
- Optionally suggest **1–2 time windows** as examples, while staying flexible around their schedule.
    

Example style (adapt, do not copy):

> If this resonates, I’d be glad to share a few specific ideas for your current site and funnel that you could implement over the next month. Would you be open to a quick 5–10 minute call sometime this week or next? I’m happy to propose a few time slots, or we can work around whatever suits you best.

Then close with:

text

`--- **Best regards,**   **[Your Name]**   Founder, [Your Company Name]   BITS Pilani   📧 [your.email@company.com] · 📱 +91-XXXXX-XXXXX · 🌐 [yourwebsite.com]`

---

## STYLE & QUALITY CHECK (WHAT YOU MUST SELF-ENFORCE)

Before finalizing the email, ensure:

- Total length is **≤ 600 words**.
    
- Hook = **2 lean paragraphs**, not a huge block.
    
- Problem section = **only headings + bullets**, no dense prose.
    
- Funnels/signals section = **short, informative, no fluff**.
    
- Emojis are only in **section headings** (2–5 total), not scattered inside bullets.
    
- Tone is:
    
    - Respectful of their success.
        
    - Direct and honest about gaps.
        
    - Calmly confident about solutions.
        
    - Always feels like it was written **specifically for this one client**.
        

---

Now, using the CLIENT DATA, COMPETITOR DATA, and WEB RESEARCH INTEL I provided, generate:

1. **Three subject line options**, then
    
2. The **email body** following the exact structure and constraints above.
```

**Output:** Complete, copy-ready email with placeholders for images.


***

### **STEP 4: Visual Assembly & Image Creation (10 mins)**

**Option A: Gamma.app (AI-Powered, Easiest)**

1. Go to **gamma.app** (free account)
2. Click "Create new" → "Document"
3. Paste your email from Step 3
4. Gamma AI will:
   - Auto-format with headings/bullets
   - Suggest image placements
   - Create charts (for SEO comparisons)
5. Use Gamma's **AI image generator** for:
   - "SEO score comparison bar chart: Tribeca C+ vs AMALFI D-"
   - "Modern real estate website with animations vs static website"
6. Export as **PDF** or copy formatted HTML

**Option B: Canva (Manual but Professional)**

1. Create **Email Newsletter** template
2. Paste email text
3. Add elements:
   - **SEO Comparison Chart:** Use Canva's chart tool
     - Bar chart: Tribeca (71), AMALFI (22)
   - **Website Screenshots:** Use browser extensions:
     - Fireshot (Chrome) - screenshot competitor sites
     - Add before/after style comparison
4. Export as **HTML** or copy sections to Gmail

**Option C: Notion AI (Quickest)**

1. Create new Notion page
2. Paste email
3. Use `/ai` command: "Format this as a professional sales email with headings"
4. Add images manually from Unsplash/Canva
5. Copy to Gmail (preserves formatting)

**Image Placeholders to Create:**
- SEO score comparison (bar/line chart)
- Competitor website screenshot (highlight good CTAs)
- Optional: Funnel diagram (awareness→interest→action)

***

### **STEP 5: Email Delivery (2 mins)**

**Best Practice:**
1. **Gmail Compose** → Paste formatted text from Gamma/Notion
2. **Inline images:** Upload SEO chart, competitor screenshot (embed directly)
3. **Preview send** to yourself first
4. **Subject line:** Pick strongest from Claude's suggestions

**Pro Formatting Tips:**
- Use Gmail's "Rich text" mode (not plain text)
- Bold headings manually if formatting breaks
- Reduce image sizes (<500KB each) for fast loading
- Add clickable CTA button: Insert → Link → "SCHEDULE CALL"

***

## **Complete Workflow Summary (Time Per Client)**

| Step | Tool | Time | Action |
|------|------|------|--------|
| 1. Parse .md | Claude.ai | 3 min | Upload files → Get JSON |
| 2. Research | Perplexity AI | 3 min | Query company intel |
| 3. Generate Email | Claude.ai | 5 min | Mega-prompt → Draft |
| 4. Add Visuals | Gamma.app | 8 min | Format + AI images |
| 5. Send | Gmail | 2 min | Preview + Send |
| **TOTAL** | | **21 min** | Full personalized email |

**Batch Processing (10 clients):**
- Parse all .md files in one Claude session (30 min)
- Research all companies in Perplexity (30 min)
- Generate 10 emails in Claude (50 min)
- Format in Gamma batch (1.5 hrs)
- **Total: 3-4 hours for 10 clients**

***

## **Alternative: All-in-One AI Workflow Tools**

If you want **even less manual work**, consider these:

### **Option 1: Relay.app (Recommended)**
**What:** No-code AI workflow builder
**Setup:**
1. Create workflow: "When .md files uploaded → Parse with AI → Research with Perplexity → Generate email"
2. Connect Gmail for auto-send
3. Add human approval step

**Pros:** Fully automated, visual builder
**Cons:** $20/mo, 30min learning curve
**Best for:** Processing 10+ clients/week

### **Option 2: Make.com (formerly Integromat)**
**What:** Visual automation platform with AI modules
**Setup:**
1. Scenario: Google Drive (upload .md) → OpenAI API → Perplexity → Gmail
2. Add conditional logic for pain point prioritization

**Pros:** Powerful, integrates everything
**Cons:** $10/mo, steeper learning curve
**Best for:** Complex workflows with CRM integration

### **Option 3: Zapier + AI (Easiest)**
**What:** Simple automation
**Setup:**
1. Trigger: New file in Google Drive folder
2. Action: ChatGPT → Parse .md
3. Action: ChatGPT → Generate email
4. Action: Send to Gmail draft

**Pros:** Beginner-friendly
**Cons:** Limited AI control, $20/mo
**Best for:** Basic automation

***

## **Cost Breakdown**

**Free Tier (Recommended to Start):**
- Claude.ai: Free (rate limits: 5 msgs/hr)
- ChatGPT-4o: Free (limited daily)
- Gamma.app: Free (10 docs/month)
- Perplexity: Free (5 searches/4hrs)
- **Total: ₹0/month** (21 min/client)

**Paid Power User:**
- Claude Pro: $20/mo (unlimited)
- Perplexity Pro: $20/mo (unlimited research)
- Gamma Pro: $10/mo (unlimited)
- **Total: $50/mo (₹4,200)** (12 min/client)

**Full Automation:**
- Relay.app: $20/mo
- Perplexity API: $5-10/mo
- **Total: $30/mo** (2 min review/client)

***

## **Sample Output Preview**

Using your Tribeca + AMALFI files:

**Subject:** Why Tribeca Developers' Website is Costing You ₹50L+ in Lost Leads

**Hook:**
"Congratulations on your Horizon Centre project on Golf Course Road—prime location, premium positioning. But here's a hard truth: Your website's C+ SEO score means **65% of high-net-worth prospects searching "luxury Gurgaon developers" never find you.** AMALFI Infraventures, despite a weaker D- score, converts 3x more leads through superior UX. Let's fix that."

**Pain Points:**
🎯 **Your Landing Page Loses 8 Out of 10 Visitors**
Your hero section lacks clear calls-to-action and engaging animations. Prospects land, see static images, and leave within 10 seconds...

[Full email continues with funnels explanation, BITS Pilani intro, CTA]

***

## **Quick Start Guide (Today)**

**Next 30 Minutes:**
1. ✅ Sign up: claude.ai (free)
2. ✅ Sign up: perplexity.ai (free)
3. ✅ Sign up: gamma.app (free)
4. ✅ Upload Tribeca.md + AMALFI.md to Claude with Step 1 prompt
5. ✅ Take JSON output → Use in Step 2 Perplexity prompt

**Tomorrow:**
6. ✅ Generate first email with Step 3 mega-prompt
7. ✅ Format in Gamma, add 2 images
8. ✅ Send test email to yourself

**This Week:**
9. ✅ Process 3-5 clients
10. ✅ Refine prompts based on results


# #double #grey
# References

# #line #grey
# Tags
#lgeneration #incomplete
