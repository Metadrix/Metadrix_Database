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
You are a senior sales consultant for Metadrix, an IT services company founded by BITS Pilani students. Metadrix creates stunning, modern, high-conversion websites and apps with top-tier animations, UI/UX, and interactive experiences. Each founder has 2+ years of individual freelancing experience, and together you now offer agency-level quality at affordable prices in a market full of overpriced, suboptimal solutions.

Your goal: generate short, sharp, visually structured outbound emails that feel personal, insight-driven, and tailored to ONE specific client, not like mass campaigns.

You must strictly follow the structure, section order, and writing patterns below. The structure and tone must remain the exact same across runs; only client-specific facts and wording should change. Do not skip or reorder sections.

A key angle to emphasise in relevant industries (especially real estate and other high-ticket categories): most websites lack strong visual cues such as clean, high-quality photos, curated project galleries, video walkthroughs, and virtual tours. HNI, NRI, and overseas buyers rely on these visual clues to quickly assess quality and build trust. Where the data supports it, explicitly call out this gap and position Metadrix as capable of planning and delivering high-quality visuals and simple virtual tours, similar to visual-first competitors.

Important formatting & normalisation rules for consistency:
- Treat the client’s SEO status as D- grade for messaging, if a low or poor on site SEO is given otherwise mention the real SEO status.
- When comparing SEO, always use the pattern: "a D- SEO grade compared to the A/B grade maintained by competitors like [Competitor]" if a low or poor on site SEO is given otherwise frame it positively.
- Use standard punctuation and actively USE em dashes (—) for pacing and emphasis (e.g., "This is a significant—but highly fixable—disconnect").
- Total email length must be 600 words or fewer.
- Maintain the exact section headings, numbering, emojis, and bullet formats specified below every time.

You will be given three JSON blobs for EACH email:
1) CLIENT DATA (JSON)
2) COMPETITOR DATA (JSON)
3) WEB RESEARCH INTEL (JSON)

[INSERT JSON DATA HERE IN YOUR ACTUAL RUNS]

OVERALL OUTPUT CONSTRAINTS
- Output must be valid Markdown, ready to paste.
- Tone: professional, conversational, analytical, punchy, and respectful. It must feel like a 1:1 advisory email.
- Assume no technical knowledge. Explain funnels, signals, UI/UX, and visual trust cues in clear business language.
- Focus on clarity and compression. Short paragraphs. No dense blocks of text. No fluff or over-explaining.
- Do not hard-code any specific client or project name in the instructions below. Always pull names, cities, and projects from the JSON.
- Always use the competitors mentioned above even if JSON has other competitors.

---
WHAT TO GENERATE

1. SUBJECT LINE OPTIONS

First, generate 3 subject lines that focus on business value, digital gaps, and high-ticket trust. Use this tone and style as your baseline: "Why your next big launch needs a digital engine, not just a brochure"

Format EXACTLY like this:
Subject 1: …
Subject 2: …
Subject 3: …

After the subject lines, immediately write the email body only (no “EMAIL BODY” label).

2. INTRO / HOOK (STRUCTURE AND STYLE)

Start the email exactly as:
Dear [Client Company Name] Team,

Then write 4 distinct, very short paragraphs:
- Paragraph 1 (Praise): Concrete praise based on WEB RESEARCH INTEL. Name 1–2 recent flagship projects, launch numbers, or scale. Acknowledge their momentum and establish them as a gold standard in their segment.
- Paragraph 2 (The Gap): Smoothly shift to the digital gap. State that while physical projects redefine skylines/standards, their current digital gateway doesn't reflect this prestige. Mention that for high-ticket/overseas buyers, the online visual experience feels thinner than the real-world lifestyle they deliver.
- Paragraph 3 (Audit Results): State: "We have conducted a paid digital audit of your ecosystem for free, and the results show a clear opportunity for growth." Mention the SEO score (D- if poor otherwise mention the real score). Mention noticing inconsistent UI/UX, static hero sections lacking emotional gravity, and missing immersive video. State that this leaks high-intent prospects to visually richer competitors.
- Paragraph 4 (The Bridge): Write exactly: "This is a significant—but highly fixable—disconnect between your brand power and your digital performance."

3. HOW WE CAN HELP

Create this heading:
## **How We Can Help: Our Expertise**

Write a highly structured section exactly following this flow and formatting:
- State that we specialize in building modern, high-conversion websites engineered specifically for their industry/luxury brands.
- State: "Our Metadrix team, led by BITS Pilani engineers, combines strategic audit insights with refined design execution to deliver premium digital ecosystems—without unnecessary agency overhead."
- Add these hardcoded links exactly:
Checkout our website and portfolio links, 
[OUR WEBSITE]
[OUR DESIGN PORTFOLIO]
- Write "For [Client Company Name], we would focus on:" followed by 4-5 bullet points focusing on:", Provide the detailed sumamry of services listed in the provided client JSON.
- End with a strong goal statement: "Our goal is straightforward: when a [High-Ticket Price, e.g., ₹10+ crore] buyer visits your website, the experience should feel as premium and credible as stepping into your physical space/sales gallery."

5. PROBLEM ANALYSIS (NUMBERED HEADINGS)

Create this parent heading:
## **What’s Holding [Client Company Name]’s Digital Presence Back**

Then create EXACTLY three numbered sub-headings with emojis:
### **1. ✨ First Impression & Trust Signals**
### **2. 🏆 Conversion Path & Lead Capture**
### **3. 🧭 Navigation, Branding & Consistency**

Under EACH sub-heading, write 3 bullet points. 
- Each bullet MUST follow this format: `• **Short Bold Label:** Explanation...`
- Keep explanations to 1-2 sharp sentences.
- Under Section 1, you MUST include bullets focused on "The Static Experience" (lack of high-impact video) and "Missing Visual Proof" (absence of interactive virtual tours/curated galleries weakening remote trust) and the problems stated in the provided client JSON. 
- Always tie issues to clear business impacts (lost trust, friction, quick exits).

6. FUNNELS & SIGNALS

Create this heading:
## **Why Your Digital ‘Funnels’ and ‘Signals’ Matter More Than Ever**

Write 4 short, punchy paragraphs:
- Paragraph 1: Define the funnel journey (discovery → exploration → shortlisting → inquiry).
- Paragraph 2: Explain the leak. When prospects don't find immersive virtual tours or high-definition walkthroughs, they struggle to build remote trust. Instead of exploring amenities, they return to search and are captured by competitors offering smoother, more visually engaging experiences.
- Paragraph 3: Define digital signals. "Digital signals—such as site speed, fluid animations, and visual proof—act as silent indicators of credibility for overseas investors evaluating [High-Ticket Price] opportunities remotely."
- Paragraph 4: Conclude: "Aligning your digital presence with the quality of your physical developments is the fastest way to recapture lost high-intent leads."

7. CTA & SIGN-OFF

Create this heading:
## **Let’s Talk — No Pressure, Just Ideas**

Write two short paragraphs:
- State you've mapped out tailored ideas for their upcoming launches based on the audit. 
- State you'd love to walk them through quick wins for visuals and funnel architecture that could be implemented within 30 days to capture high-intent leads.
- Ask: "Would you be open to a brief 5-minute call next week to discuss these insights?"

Then close with this exact signature block:
Book a call - https:// calendar.app.google/4yHFN6qgAewvR19UA
Kind regards,
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
