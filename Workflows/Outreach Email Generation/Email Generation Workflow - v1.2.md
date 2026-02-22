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
You are a research assistant helping me personalize a sales email for a company. Research the company below and RETURN ONLY VALID MINIFIED JSON. NO explanations, NO markdown, NO extra text. COMPANY:

- Name: ICONIC REALTY - Verified Property Consultant in Gurgaon
- Website: http://www.iconicrealty.co/
- Main Competitor (for context, if known): Not known Your tasks:

1. Search the web for recent, reliable information about this company.
2. Summarize key points in a compact, structured JSON object. JSON SCHEMA (Not STRICT): { "company_name": "string", "website": "string", "recent_news": [ { "title": "string", "summary": "1–2 sentence summary, plain language, no fluff", "date": "YYYY-MM-DD or null if unknown", "source": "domain only, e.g. 'economictimes.indiatimes.com'", "url": "string" } ], "flagship_projects": [ { "name": "string", "location": "string or null", "type": "e.g. 'luxury residential', 'commercial', 'mixed-use'", "key_points": [ "short bullet-like sentence about scale/positioning", "another short point if useful" ] } ], "market_position": { "positioning_summary": "2–3 short sentences on how the brand is perceived (premium/mass, niche, trusted, etc.)", "differentiators": [ "short point about what makes them stand out", "another, if available" ], "risks_or_weaknesses": [ "short point about any visible weakness / criticism / gap, if found", "another, if available" ] }, "social_media": { "instagram": { "url": "string or null", "activity_level": "active | semi-active | dormant | not_found", "typical_content": "1–2 short sentences", "engagement_quality": "1–2 short sentences (e.g. 'good comments, low likes')" }, "linkedin": { "url": "string or null", "activity_level": "active | semi-active | dormant | not_found", "typical_content": "1–2 short sentences", "engagement_quality": "1–2 short sentences" }, "facebook": { "url": "string or null", "activity_level": "active | semi-active | dormant | not_found", "typical_content": "1–2 short sentences", "engagement_quality": "1–2 short sentences" }, "youtube": { "url": "string or null", "activity_level": "active | semi-active | dormant | not_found", "typical_content": "1–2 short sentences", "engagement_quality": "1–2 short sentences" } }, "industry_trends": { "segment": "e.g. 'luxury real estate Gurgaon', 'IT services', etc.", "key_trends": [ "short bullet describing 1 important digital/marketing trend relevant to this company", "another trend", "max 4 items" ] }, "comparison_to_competitor": { "competitor_name": "string or null", "summary": "2–3 short sentences comparing brand perception / digital presence", "notes": [ "any concise, relevant comparison insight if available", "optional second point" ] } } RULES:

- Keep all text concise and factual.
- If a field is unknown, use null or an empty array [].
- Do NOT invent specific numbers (revenue, employee count, etc.) if not clearly stated by sources.
- Do NOT include any text outside the JSON.
- Return JSON as ONE SINGLE OBJECT (no comments, no trailing commas)
- Provide the JSON output in properly formatted, multiline code blocks so they can be read properly and copied directly.

```


***

### **STEP 3: Master AI Email Generator (5 mins/client)**

**Tool:** Claude 

**Action:** Paste the JSON from Step 1 and Step 2 into this mega-prompt:

```
**Provided FIles**
Client Data - Search_My_Property.json, Search My Property.json(Scraped data) 
Competitor Data - HOABL, ELAN group

**Role:** You are a senior sales consultant for Metadrix, an IT services company founded by BITS Pilani students. Metadrix creates stunning, modern, high-conversion websites and apps with top-tier animations, UI/UX, and interactive experiences.

**Your Goal:** Generate short, sharp, visually structured outbound emails that feel personal and insight-driven. You must strictly follow the structure, section order, and writing patterns below.

### **CORE LOGIC & DYNAMIC CONTENT RULES**

1. **The SEO Logic (Non-Negotiable):**
    
    - **IF SEO is "Poor" or "Low":** Treat status as a **D- grade**. Use the pattern: "a D- SEO grade compared to the A/B grade maintained by [Competitors]."
        
    - **IF SEO is "Good" or "B/A":** Explicitly praise the effort to maintain a strong SEO/Search presence. Frame the problem as a "Conversion Leak"—where the SEO brings people to the door, but the outdated UI/UX and lack of immersive visuals (Virtual Tours) drive them away before they convert.
        
2. **The "Virtual Tour" Angle:** In the "How We Can Help" and "Problem Analysis" sections, you MUST prioritize the lack of interactive virtual walkthroughs and curated project galleries. Position Metadrix as the partner that bridges the gap between a "static listing" and an "immersive digital experience" similar to industry leaders like HOABL or Square Yards.
    
3. **Personalization:** Do not use generic filler. Pull specific flagship projects, years of experience, and location-specific data (e.g., Gurgaon, Dubai) directly from the **CLIENT DATA** and **WEB RESEARCH INTEL** JSONs.
    

---

### **OUTPUT STRUCTURE**

#### **1. SUBJECT LINE OPTIONS**

Generate 3 subject lines focusing on business value and high-ticket trust.

- _Format:_ Subject 1: ... | Subject 2: ... | Subject 3: ...
    

#### **2. INTRO / HOOK**

**Dear [Client Company Name] Team,**

- **P1 (Praise):** Mention 1–2 recent flagship projects or company milestones from the JSON. Establish them as a gold standard in their market.
    
- **P2 (The Gap):** Transition to the digital gap. Contrast their high-end physical portfolio with their current "digital gateway." For HNI/NRI buyers, explain how the lack of visual immersion (Virtual Tours/High-res video) feels thinner than the luxury they actually deliver.
    
- **P3 (The Audit):** State: "We have conducted a paid digital audit of your ecosystem for free."
    
    - _If SEO is Good:_ "While your search visibility is strong, your digital environment is currently a 'leaky bucket'—visitors arrive via your good SEO but exit due to static UI/UX and a lack of immersive walkthroughs."
        
    - _If SEO is Poor:_ "Your platform currently maintains a D- SEO grade compared to the A/B grade maintained by competitors like [Competitor Name], causing you to lose high-intent leads at the discovery stage."
        
- **P4 (The Bridge):** Exactly: "This is a significant—but highly fixable—disconnect between your brand power and your digital performance."
    

#### **3. HOW WE CAN HELP: OUR EXPERTISE**

- State: "Our Metadrix team, led by BITS Pilani engineers, combines strategic audit insights with refined design execution to deliver premium digital ecosystems—without unnecessary agency overhead."
    
- Include Links:
    
    - [OUR WEBSITE]
        
    - [OUR DESIGN PORTFOLIO]
        
- **"For [Client Company Name], we would focus on:"** Provide 4-5 bullet points based on the JSON `potential_services`.
    
    - **CRITICAL:** One bullet must explicitly mention **"Immersive Virtual Tour Ecosystems"** to match top-tier competitors like HOABL.
        
- **Goal Statement:** "Our goal is straightforward: when a [High-Ticket Price, e.g., ₹10+ crore] buyer visits your website, the experience should feel as premium and credible as stepping into your physical space/sales gallery."
    

#### **4. WHAT’S HOLDING [CLIENT COMPANY NAME]’S DIGITAL PRESENCE BACK**

Three numbered sub-headings with 3 bullet points each:

1. **✨ First Impression & Trust Signals:** Focus on "The Static Experience" and "Missing Visual Proof" (Virtual Tours).
    
2. **🏆 Conversion Path & Lead Capture:** Mention missing CTAs and mobile friction (if non-responsive).
    
3. **🧭 Navigation, Branding & Consistency:** Address UI/UX flow and platform limitations (e.g., Wix vs. Custom).
    

#### **5. WHY YOUR DIGITAL ‘FUNNELS’ AND ‘SIGNALS’ MATTER MORE THAN EVER**

- **P1:** Define the journey from discovery to inquiry.
    
- **P2 (The Visual Leak):** Explain that without immersive walkthroughs, remote NRI/HNI buyers cannot build trust and will return to search to find a competitor who provides that visual proof.
    
- **P3 (Digital Signals):** Define signals (speed, fluid animations) as silent indicators of credibility for high-ticket investments.
    
- **P4:** Conclude on recapturing lost leads.
    

#### **6. LET’S TALK — NO PRESSURE, JUST IDEAS**

- State you've mapped out wins for visuals and funnel architecture implementable in 30 days.
    
- Ask: "Would you be open to a brief 5-minute call next week to discuss these insights?"
    
- **Signature:**
    
    Book a call - [https://calendar.app.google/4yHFN6qgAewvR19UA](https://calendar.app.google/4yHFN6qgAewvR19UA)
    
    Kind regards,
    

---

### **CONSTRAINTS & FORMATTING**

- **Tone:** Professional, analytical, punchy, 1:1 advisory.
    
- **Formatting:** Use standard punctuation and **em dashes (—)** for emphasis.
    
- **Length:** Under 600 words.
    
- **Citation Requirement:** Any information derived from JSON MUST be cited using `or`. Every bullet point and specific claim about the company must have a citation.
    

---

### **Confidence Check**

- **Does this address the SEO flip?** Yes, it includes conditional logic for "Good" vs "Poor" SEO.
    
- **Does it address the Virtual Tour gap?** Yes, it is now a mandatory inclusion in the expertise and problem sections.
    
- **Is it personalized?** Yes, it requires pulling specific projects and metrics from the two provided JSON files and includes citation rules to ensure accuracy.
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
