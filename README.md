# 🎯 LinkedIn Lead Generation from Job Boards 123456

An intelligent n8n workflow that scrapes job boards, finds company LinkedIn profiles, extracts comprehensive company data, and generates AI-powered sales intelligence reports. Perfect for B2B lead generation and sales prospecting.

![Status](https://img.shields.io/badge/status-active-success.svg)
![n8n](https://img.shields.io/badge/n8n-workflow-EA4B71?logo=n8n)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?logo=openai)
![LinkedIn](https://img.shields.io/badge/LinkedIn-Scraping-0077B5?logo=linkedin)

## ✨ Features

- **Job Board Integration** - Scrapes RSS feeds from job boards (Remotive, etc.)
- **Google X-Ray Search** - Finds LinkedIn company pages using advanced search
- **LinkedIn Data Extraction** - Comprehensive scraping of company profiles
- **Employee Discovery** - Extracts key team members with LinkedIn URLs
- **Company Intelligence** - Products, locations, recent updates, affiliations
- **AI-Powered Analysis** - GPT-4o-mini generates prospect summaries
- **Email Notifications** - Sends formatted reports to sales team
- **Rate Limiting** - Built-in delays to avoid detection
- **Error Handling** - Graceful fallbacks for missing data
- **Scalable Processing** - Loop-based architecture for multiple jobs

## 🔄 Workflow Overview

### Complete Pipeline

```
Manual Trigger
      ↓
Configure (API Keys)
      ↓
Get Job Board RSS Feed
    (Remotive Marketing Jobs)
      ↓
Limit Results (Optional: 2 for testing)
      ↓
    ┌─────┴─────┐
    Loop Over Items
      ↓
Scrape Job Ad
    (Extract job details + location)
      ↓
Extract Location Data
      ↓
Has Location? (If node)
    Yes ↓         No → Skip to Next
      ↓
Google X-Ray Search
    (site:linkedin.com/company + company + location)
      ↓
Results Found? (If node)
    Yes ↓         No → Skip to Next
      ↓
Set LinkedIn URL from First Result
      ↓
Clean LinkedIn URL (regex)
      ↓
Scrape LinkedIn Company Page
      ↓
Extract Main Blocks (7 sections)
    ├─ Info (name, category, area, followers)
    ├─ Locations
    ├─ About Us (description + definitions)
    ├─ Employees (names, titles, LinkedIn URLs)
    ├─ Updates (recent posts, last 3 months)
    ├─ Products (if available)
    └─ Affiliated Pages (if available)
      ↓
Process Each Section
    (HTML extraction, filtering, aggregation)
      ↓
Merge All Data
      ↓
Aggregate into Single Object
      ↓
AI: Summarize Available Data
    (GPT-4o-mini analyzes prospect quality)
      ↓
Send Email Report
    (Gmail with formatted HTML)
      ↓
Wait 5 Seconds (rate limiting)
      ↓
Loop to Next Job → Repeat
```

## 🚀 Quick Start

### Prerequisites

- **n8n instance** (v1.0+, self-hosted or cloud)
- **Google Custom Search API** (for X-ray search)
- **Google Custom Search Engine ID**
- **OpenAI API key** (GPT-4o-mini)
- **Gmail account** (for email notifications)

### Installation

#### 1. Import Workflow

```bash
1. Download "template_job_board_linkedin_leadgen.json"
2. In n8n: Import from File
3. Upload JSON file
4. Workflow loads with all 60+ nodes
```

#### 2. Configure API Keys

**Google Custom Search**
```javascript
// Configure node - Edit these values:
GOOGLE_API_KEY: "{YOUR_GOOGLE_API_KEY}"
GOOGLE_CUSTOM_SEARCH: "{YOUR_GOOGLE_CUSTOM_SEARCH_ID}"
```

**How to Get Google API Keys**:
```
1. Go to: https://console.cloud.google.com
2. Create project: "n8n-linkedin-leadgen"
3. Enable "Custom Search API"
4. Create API Key (Credentials → Create → API Key)
5. Copy key to Configure node

6. Create Custom Search Engine:
   - Go to: https://programmablesearchengine.google.com
   - Click "Add" → "Search the entire web"
   - Get your Search Engine ID (cx parameter)
   - Copy to Configure node
```

**OpenAI**
```javascript
// OpenAI Chat Model node
1. Add credentials in n8n
2. Type: OpenAI API
3. API Key: sk-...
4. Select "gpt-4o-mini" model
```

**Gmail**
```javascript
// Send email node
1. Add Gmail credentials in n8n
2. OAuth2 authentication
3. Grant permissions
4. Update recipient email: "youremail@gmail.com"
```

#### 3. Customize Settings

**Job Board Source**
```javascript
// Get job board RSS feed node
URL: "https://remotive.com/remote-jobs/feed/marketing"

// Other options:
// - https://remotive.com/remote-jobs/feed/software-dev
// - https://weworkremotely.com/categories/remote-programming-jobs.rss
// - https://www.flexjobs.com/rss
```

**Result Limit**
```javascript
// Limit node
maxItems: 2  // For testing, increase for production

// Or remove Limit node entirely to process all jobs
```

**Email Recipient**
```javascript
// Send email node
sendTo: "your-sales-team@company.com"
// Can add multiple: "sales@co.com, manager@co.com"
```

#### 4. Test Execution

```bash
1. Click "Execute Workflow" (manual trigger)
2. Monitor progress in execution log
3. Check each node for data
4. Verify email received
5. Review LinkedIn data extracted
```

## ⚙️ Configuration

### Core Settings

**Configure Node**
```javascript
{
  GOOGLE_API_KEY: "{YOUR_GOOGLE_API_KEY}",
  GOOGLE_CUSTOM_SEARCH: "{YOUR_GOOGLE_CUSTOM_SEARCH_ID}"
}
```

**Limit Node** (Sticky Note: "Change or remove the limit")
```javascript
maxItems: 2  // Testing: process 2 jobs
maxItems: 10 // Production: process 10 jobs
// Remove node: process entire RSS feed
```

**Loop Over Items**
```javascript
batchSize: 1  // Process one job at a time
options: {}   // Default settings
```

**Wait 5s Node**
```javascript
amount: 5
unit: "seconds"
// Increase for more caution: 10-15 seconds
// Decrease for speed: 3 seconds (risky)
```

### Job Board Customization

**Change Job Category**
```javascript
// Marketing jobs (default)
URL: "https://remotive.com/remote-jobs/feed/marketing"

// Software development
URL: "https://remotive.com/remote-jobs/feed/software-dev"

// Customer support
URL: "https://remotive.com/remote-jobs/feed/customer-support"

// All remote jobs
URL: "https://remotive.com/remote-jobs/feed"
```

**Alternative Job Boards**
```javascript
// WeWorkRemotely
URL: "https://weworkremotely.com/categories/remote-programming-jobs.rss"
// May need different CSS selectors for scraping

// Indeed (no RSS, would need API or different approach)
// FlexJobs (requires paid account)
// LinkedIn Jobs (complex anti-scraping)
```

### Google X-Ray Search Query

**Current Query**
```javascript
// Google X-Ray Search node
q: "site:linkedin.com/company ${company_name} ${location}"

// Example: "site:linkedin.com/company Acme Corp San Francisco"
```

**Alternative Queries**
```javascript
// More specific (company page only)
q: "site:linkedin.com/company ${company_name}"

// Include company size
q: "site:linkedin.com/company ${company_name} employees"

// Include industry
q: "site:linkedin.com/company ${company_name} ${industry}"
```

### LinkedIn Data Extraction

**Sticky Note Warning**: "The CSS selectors could occasionally change - don't panic, very easy to fix"

**Main Sections Extracted**:
```javascript
// Extract main blocks node
{
  info: ".top-card-layout__entity-info:nth-child(1)",
  about_us: ".core-section-container[data-test-id='about-us']",
  locations: ".core-section-container.locations",
  employees: ".core-section-container[data-test-id='employees-at']",
  updates: ".core-section-container[data-test-id='updates']",
  products: ".base-card",
  affiliate_pages: "section[data-test-id='affiliated-pages']",
  crunchbase: "a[data-tracking-control-name='funding_crunchbase']"
}
```

**If Selectors Break**:
1. Open LinkedIn company page in browser
2. Right-click → Inspect element
3. Find the section you need
4. Copy the CSS selector
5. Update in corresponding Extract node
6. Test the workflow

### AI Prompt Customization

**Current Prompt** (Summarize available data node):
```javascript
"Our company sells marketing services.
We scraped a list of marketing jobs..."

Important signals:
1. Company is hiring
2. Company has a team we target

Structure:
1. Company name
2. Quick summary
3. Why good prospect
```

**Customize for Your Business**:
```javascript
// For SaaS companies:
"Our company provides project management software.
We target companies with:
- 50-500 employees
- Remote teams
- Growing engineering department"

// For recruiting agencies:
"Our company provides recruitment services.
We target companies that:
- Are actively hiring
- Have multiple open roles
- Lack internal recruiters"

// For consulting:
"Our company provides digital transformation consulting.
We target companies that:
- Are traditional businesses
- Show signs of digital initiatives
- Have budget for external consulting"
```

### Email Template

**Current Template** (Send email node):
```html
<h1>Company name: {{ company_name }}</h1>
<p>Category: {{ category }}</p>
<p>Linkedin Page: {{ linkedin_url }}</p>
<p>Website: {{ website }}</p>
<p>Crunchbase: {{ crunchbase }}</p>

<h2>Summary</h2>
<p>{{ ai_summary }}</p>

<h2>Why is it a good prospect</h2>
<p>{{ ai_why_good_prospect }}</p>
```

**Customization Options**:
```html
<!-- Add more fields -->
<h2>Company Details</h2>
<p><strong>Employees:</strong> {{ followers }}</p>
<p><strong>Locations:</strong> {{ locations }}</p>
<p><strong>Industry:</strong> {{ category }}</p>

<!-- Add employee list -->
<h2>Key Contacts</h2>
<ul>
{{ employees.map(e => `<li>${e.name} - ${e.title}</li>`).join('') }}
</ul>

<!-- Add recent updates -->
<h2>Recent Activity</h2>
{{ updates.slice(0, 3).map(u => `<p>${u.content}</p>`).join('') }}

<!-- Add products -->
<h2>Products</h2>
{{ products.map(p => `<p><strong>${p.name}:</strong> ${p.description}</p>`).join('') }}
```

## 🏗️ Architecture Deep Dive

### Phase 1: Job Board Scraping (5-10 seconds)

**Nodes**:
1. `When clicking 'Test workflow'` - Manual trigger
2. `Configure` - Set API keys
3. `Get job board RSS feed` - HTTP request to Remotive
4. `Limit` - Optional: limit results for testing
5. `Loop Over Items` - Process jobs one by one

**RSS Feed Structure**:
```xml
<item>
  <title>Marketing Manager - Remote</title>
  <link>https://remotive.com/jobs/12345</link>
  <dc:creator>Acme Corp</dc:creator>
  <description>Job description...</description>
</item>
```

**Output**:
```javascript
{
  title: "Marketing Manager - Remote",
  link: "https://remotive.com/jobs/12345",
  "dc:creator": "Acme Corp",  // Company name
  description: "Job description..."
}
```

### Phase 2: Job Ad Extraction (3-5 seconds per job)

**Nodes**:
6. `Scrape the job ad` - HTTP request to job URL
7. `Extract job ad` - HTML extraction
8. `Extract location data1` - Parse location from info

**Extraction**:
```javascript
// Extract job ad node
{
  info: ".tw-mt-4.tw-text-sm",           // Location, salary, etc.
  job_ad: ".tw-mt-8 > .left > div"      // Full job description
}

// Example info text:
"Location: San Francisco, CA | Salary: $100k-$150k | Remote"
```

**Code Node Logic**:
```javascript
if (item.json.info.includes('Location:')) {
  const location = item.json.info.split('Location:')[1]
  item.json.location = location.slice(0, location.length - 1)
    .replace(/\n/g, '')
    .trim()
}
// Output: "San Francisco, CA"
```

### Phase 3: LinkedIn Discovery (2-5 seconds)

**Nodes**:
9. `If` - Check if location exists
10. `Google X-Ray Search` - Find LinkedIn company page
11. `Results?` - Verify search returned results
12. `Set Linkedin URL from the first result` - Extract URL and description
13. `Clean linkedin url` - Regex to clean URL

**Google Custom Search API**:
```javascript
// Request
GET https://www.googleapis.com/customsearch/v1
?key={API_KEY}
&cx={SEARCH_ENGINE_ID}
&q=site:linkedin.com/company Acme Corp San Francisco

// Response
{
  searchInformation: { totalResults: "5" },
  items: [
    {
      title: "Acme Corp | LinkedIn",
      link: "https://www.linkedin.com/company/acme-corp?param=123",
      pagemap: {
        metatags: [{
          "og:description": "Acme Corp is a leading provider..."
        }]
      }
    }
  ]
}
```

**URL Cleaning**:
```javascript
// Before: https://www.linkedin.com/company/acme-corp?trk=xyz&ref=abc
// After:  https://www.linkedin.com/company/acme-corp

const companySlugMatch = url.match(/linkedin\.com\/company\/([^\/]+)/);
const companySlug = companySlugMatch[1];
cleanUrl = `https://www.linkedin.com/company/${companySlug}`;
```

### Phase 4: LinkedIn Scraping (10-15 seconds)

**Nodes**:
14. `Scrape LinkedIn` - HTTP request with headers
15. `Extract main blocks` - Parse 7 major sections

**Critical Headers**:
```javascript
// Scrape LinkedIn node
headers: {
  "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)...",
  "referer": "https://www.google.com/"
}
// Referer = Google makes it seem like organic traffic
```

**Extracted Sections**:
```javascript
{
  info: "Basic company info (name, category, followers)",
  about_us: "Company description + key facts (size, HQ, etc.)",
  locations: "All office locations",
  employees: "Featured employees with LinkedIn profiles",
  updates: "Recent posts and activity",
  products: "Company products/services",
  affiliate_pages: "Related companies (subsidiaries, etc.)",
  crunchbase: "Link to Crunchbase profile (if available)"
}
```

### Phase 5: Data Processing (10-20 seconds)

**Info Section** (3 nodes):
- `Extract info` → `Code1` → `Merge`
```javascript
// Extracted:
{
  name: "Acme Corp",
  category: "Software Development",
  area_html: "San Francisco Bay Area<span>·</span>10,000 followers",
  summary: "Leading provider of cloud solutions"
}

// Code1 splits area_html:
{
  area: "San Francisco Bay Area",
  followers: "10,000 followers"
}
```

**Locations Section** (2 nodes):
- `Extract locations` → `If1` → `Merge` (or `Edit Fields1` if empty)
```javascript
// Extracted:
{
  locations: [
    "San Francisco, CA",
    "New York, NY",
    "Austin, TX"
  ]
}
```

**About Us Section** (6 nodes):
- `Extract about us` → `If2` → `Split Out` → `HTML5` → `Code2` → `Merge`
```javascript
// Extracted definitions (key-value pairs):
{
  Website: "https://acme.com",
  Industry: "Information Technology",
  "Company size": "1,001-5,000 employees",
  Headquarters: "San Francisco, CA",
  Founded: "2010",
  Specialties: "Cloud, SaaS, AI"
}

// Code2 restructures into object:
{
  Website: "https://acme.com",
  Industry: "Information Technology",
  // ...
  description: "We build amazing cloud solutions..."
}
```

**Employees Section** (5 nodes):
- `Extract employees` → `If3` → `Split Out1` → `HTML7` → `Aggregate` → `Merge`
```javascript
// Extracted per employee:
{
  name: "John Doe",
  title: "VP of Engineering",
  linkedin_url: "https://linkedin.com/in/johndoe"
}

// Aggregated into array:
{
  employees: [
    { name: "John Doe", title: "VP of Engineering", linkedin_url: "..." },
    { name: "Jane Smith", title: "CMO", linkedin_url: "..." },
    // ...
  ]
}
```

**Updates Section** (7 nodes):
- `Extract updates` → `If4` → `Split Out2` → `HTML9` → `Code3` → `Filter1` → `Aggregate1` → `Merge`
```javascript
// Extracted per update:
{
  link: "https://linkedin.com/feed/update/123",
  content: "We're excited to announce...",
  reshared_content: null,
  time: "2w"  // 2 weeks ago
}

// Code3 converts time to date:
{
  posted_at: "2024-09-24",  // Calculated from "2w"
  // Supports: "5h", "3d", "2w", "1mo"
}

// Filter1 keeps only last 3 months:
// Only includes updates where posted_at > (now - 3 months)

// Aggregated:
{
  updates: [
    { link: "...", content: "...", posted_at: "2024-09-24" },
    // Only recent updates
  ]
}
```

**Products Section** (5 nodes):
- `Are there products?` → `Split Out3` → `Extract products` → `Aggregate2` → `Merge`
```javascript
// Extracted per product:
{
  name: "Acme Cloud Platform",
  category: "Cloud Computing",
  description: "Enterprise-grade cloud infrastructure..."
}

// Aggregated:
{
  products: [
    { name: "Acme Cloud Platform", category: "...", description: "..." },
    { name: "Acme Analytics", category: "...", description: "..." }
  ]
}
```

**Affiliated Pages Section** (5 nodes):
- `Are there affiliated pages?` → `Split Out4` → `Extract affiliated pages` → `Aggregate4` → `Merge`
```javascript
// Extracted per page:
{
  title: "Acme Europe",
  link: "https://linkedin.com/company/acme-europe"
}

// Aggregated:
{
  affiliate_pages: [
    { title: "Acme Europe", link: "..." },
    { title: "Acme Asia", link: "..." }
  ]
}
```

### Phase 6: Data Merging (1-2 seconds)

**Nodes**:
16. `Merge` - Combine all 7 data streams
17. `Aggregate3` - Create single object with all data

**Merge Strategy**:
```javascript
// Mode: combineByPosition
// Number of inputs: 7

// Input 0 (info): { name, category, area, followers, summary }
// Input 1 (locations): { locations: [...] }
// Input 2 (about_us): { Website, Industry, description, ... }
// Input 3 (employees): { employees: [...] }
// Input 4 (updates): { updates: [...] }
// Input 5 (products): { products: [...] }
// Input 6 (affiliate_pages): { affiliate_pages: [...] }

// Output: Single object with all fields merged
```

**Final Data Structure**:
```javascript
{
  data: [{
    // Info
    name: "Acme Corp",
    category: "Software Development",
    area: "San Francisco Bay Area",
    followers: "10,000 followers",
    summary: "Leading provider...",
    
    // Locations
    locations: ["San Francisco, CA", "New York, NY"],
    
    // About Us
    Website: "https://acme.com",
    Industry: "Information Technology",
    "Company size": "1,001-5,000 employees",
    Headquarters: "San Francisco, CA",
    Founded: "2010",
    Specialties: "Cloud, SaaS, AI",
    description: "We build amazing...",
    
    // Employees
    employees: [
      { name: "John Doe", title: "VP of Engineering", linkedin_url: "..." },
      // ...
    ],
    
    // Updates
    updates: [
      { link: "...", content: "...", posted_at: "2024-09-24" },
      // ...
    ],
    
    // Products
    products: [
      { name: "Acme Cloud Platform", category: "...", description: "..." },
      // ...
    ],
    
    // Affiliates
    affiliate_pages: [
      { title: "Acme Europe", link: "..." },
      // ...
    ]
  }]
}
```

### Phase 7: AI Analysis (5-10 seconds)

**Nodes**:
18. `Summarize available data` - GPT-4o-mini analyzes prospect
19. `Structured Output Parser` - JSON schema validation

**AI Prompt**:
```
<Instruction>
Our company sells marketing services.
We scraped marketing jobs and found the LinkedIn profile.

Create a summary for the sales team.

Important signals:
1. Company is hiring (job ad info)
2. Company has team we target

Structure:
1. Company name
2. Quick summary
3. Why good prospect
</Instruction>

<JobAd>
{{ job_ad }}
</JobAd>

<Data>
{{ JSON.stringify(company_data, null, 2) }}
</Data>
```

**Output Schema**:
```javascript
{
  type: "object",
  properties: {
    company_summary: {
      type: "string",
      description: "Summary of the company"
    },
    why_good_prospect: {
      type: "string",
      description: "Why this is a good prospect"
    }
  },
  required: ["company_summary", "why_good_prospect"]
}
```

**Example AI Output**:
```javascript
{
  company_summary: "Acme Corp is a 5,000-person software company specializing in cloud infrastructure. They're based in San Francisco with offices in NYC and Austin. Recent LinkedIn updates show strong engagement with developer community. They offer 3 main products: Cloud Platform, Analytics, and Security Suite.",
  
  why_good_prospect: "Excellent fit for our marketing services: (1) Actively hiring Marketing Manager, indicating growth mode and budget. (2) Recent posts show they're launching new products - will need marketing support. (3) Engineering-heavy team (200+ engineers listed) but smaller marketing team, suggesting need for external help. (4) VP of Marketing Sarah Johnson recently joined (3 months ago), likely building her team and evaluating vendors."
}
```

### Phase 8: Email Notification (2-3 seconds)

**Nodes**:
20. `Send email` - Gmail node with formatted HTML
21. `Wait 5s` - Rate limiting before next iteration
22. Loop back to `Loop Over Items`

**Email Template**:
```html
<h1>Company name: Acme Corp</h1>
<p>Category: Software Development</p>
<p>Linkedin Page: https://linkedin.com/company/acme-corp</p>
<p>Website: https://acme.com</p>
<p>Crunchbase: https://crunchbase.com/organization/acme-corp</p>

<h2>Summary</h2>
<p>Acme Corp is a 5,000-person software company...</p>

<h2>Why is it a good prospect</h2>
<p>Excellent fit for our marketing services: (1) Actively hiring...</p>
```

**Rate Limiting**:
```javascript
// Wait 5s node
// Prevents:
- LinkedIn rate limiting
- Google API throttling
- Being flagged as bot

// Recommended wait times:
- Development/testing: 5 seconds
- Production (careful): 10-15 seconds
- Production (very safe): 20-30 seconds
```

## 🎨 Customization Examples

### Change Target Industry

**Modify Job Board**:
```javascript
// Get job board RSS feed node

// Sales jobs
URL: "https://remotive.com/remote-jobs/feed/sales"

// Engineering jobs
URL: "https://remotive.com/remote-jobs/feed/software-dev"

// Customer service
URL: "https://remotive.com/remote-jobs/feed/customer-support"
```

**Update AI Prompt**:
```javascript
// Summarize available data node

// For DevTools companies:
"Our company sells developer tools and APIs.
We target companies with:
- Large engineering teams (50+ developers)
- Modern tech stack
- API-heavy products"

// For HR software:
"Our company provides HR management software.
We target companies that:
- Are rapidly hiring (5+ open positions)
- Don't use modern HR tech
- Have distributed teams"
```

### Add Slack Notifications

**Replace Gmail with Slack**:
```javascript
// Remove: Send email node
// Add: Slack node

// Configuration:
Channel: "#sales-leads"
Message: `
🎯 *New Prospect: ${company_name}*

*Category:* ${category}
*Website:* ${website}
*LinkedIn:* ${linkedin_url}

*Summary:*
${company_summary}

*Why Good Prospect:*
${why_good_prospect}

*Key Employees:*
${employees.map(e => `• ${e.name} - ${e.title}`).join('\n')}
`
```

### Export to CRM

**Add After Email** (Sticky Note: "Push the collected data into your CRM, Airtable, Google Sheets, Notion, postgres..."):

**Salesforce**:
```javascript
// Add Salesforce node
Operation: Create
Resource: Lead

Fields:
- Company: {{ company_name }}
- Website: {{ website }}
- LinkedIn: {{ linkedin_url }}
- Industry: {{ category }}
- Employees: {{ followers }}
- Description: {{ company_summary }}
- Lead Source: "Job Board Automation"
- Rating: "Hot" (if hiring)
```

**Airtable**:
```javascript
// Add Airtable node
Operation: Append
Table: "Prospects"

Fields:
- Company Name: {{ name }}
- Website: {{ Website }}
- LinkedIn URL: {{ linkedin_url }}
- Category: {{ category }}
- Locations: {{ locations.join(', ') }}
- Employee Count: {{ followers }}
- Summary: {{ company_summary }}
- Why Good: {{ why_good_prospect }}
- Products: {{ products.map(p => p.name).join(', ') }}
- Key Contacts: {{ employees.map(e => `${e.name} (${e.title})`).join('; ') }}
- Date Added: {{ $now.toISO() }}
```

**Google Sheets**:
```javascript
// Add Google Sheets node
Operation: Append Row
Spreadsheet: "Sales Prospects"
Sheet: "Leads"

Columns:
A: {{ name }}
B: {{ category }}
C: {{ Website }}
D: {{ linkedin_url }}
E: {{ followers }}
F: {{ locations[0] }}
G: {{ company_summary }}
H: {{ why_good_prospect }}
I: {{ $now.toFormat('yyyy-MM-dd') }}
```

**PostgreSQL**:
```javascript
// Add Postgres node
Operation: Insert

SQL:
INSERT INTO prospects (
  company_name, category, website, linkedin_url,
  employee_count, locations, summary, why_good_prospect,
  products, key_contacts, scraped_at
) VALUES (
  $1, $2, $3, $4, $5, $6, $7, $8, $9, $10, $11
)

Parameters:
$1: {{ name }}
$2: {{ category }}
$3: {{ Website }}
$4: {{ linkedin_url }}
$5: {{ followers }}
$6: {{ JSON.stringify(locations) }}
$7: {{ company_summary }}
$8: {{ why_good_prospect }}
$9: {{ JSON.stringify(products) }}
$10: {{ JSON.stringify(employees) }}
$11: {{ $now.toISO() }}
```

### Add Filtering Logic

**Filter by Company Size**:
```javascript
// Add Filter node after Extract info
// Before LinkedIn scraping

Conditions:
- followers > 100 (at least 100 followers)
- followers < 10000 (not too large)

// Regex to extract number:
const followerCount = followers.match(/[\d,]+/)[0].replace(',', '');
const count = parseInt(followerCount);
```

**Filter by Location**:
```javascript
// Add Filter node after Extract location data

Conditions:
- location contains "United States"
- OR location contains "Canada"
- OR location contains "UK"

// Only target specific regions
```

**Filter by Industry**:
```javascript
// Add Filter node after Extract info

Conditions:
- category contains "Technology"
- OR category contains "Software"
- OR category contains "SaaS"

// Target tech companies only
```

### Enrich with Additional Data

**Add Crunchbase Scraping**:
```javascript
// After Extract main blocks
// Check if crunchbase link exists

// Add new branch:
If crunchbase exists:
  → HTTP Request to Crunchbase
  → Extract: funding, investors, valuation
  → Add to final data object
```

**Add Company Website Scraping**:
```javascript
// After Extract about us
// If Website field exists

// Add new branch:
→ HTTP Request to company website
→ Extract: tech stack, pricing, contact info
→ Use BuiltWith API to detect technologies
→ Add to final data object
```

**Add Email Finding**:
```javascript
// After Extract employees
// For each employee

// Add Hunter.io API node:
→ Find email pattern for company domain
→ Generate emails for key employees
→ Verify emails
→ Add to employee objects
```


