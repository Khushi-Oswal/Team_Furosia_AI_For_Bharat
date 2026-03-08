🧬 BioClock — Multi-Agent Predictive Health System
Predict. Prevent. Thrive. — For Every Indian.

AWS Bedrock Region Languages Agents Analysis

Team: The Furiosa | Leader: Khushi Oswal

Live Demo: [https://staging.d28iblk6unybu5.amplifyapp.com](https://staging.d1ayudw2b2ig4f.amplifyapp.com/)

GitHub: https://github.com/Khushi-Oswal/Team_Furosia_AI_For_Bharat

📋 Table of Contents
What is BioClock?
The Problem
Our Solution
System Architecture
AI Agent Pipeline
Data Lake Architecture
AWS Services
Getting Started
API Reference
Project Structure
Hackathon Highlights
🧬 What is BioClock?
BioClock transforms routine blood test reports into comprehensive disease risk predictions in under 40 seconds — powered by a 4-agent Amazon Bedrock pipeline with an autonomous supervisor running on AWS serverless infrastructure.

User pastes lab report  →  AI extracts biomarkers  →  4 agents analyze  →  Full report in 20-40s
Text from Apollo/SRL/       Bedrock AI reads           Temporal trends       Disease predictions
Thyrocare/Dr. Lal            unstructured text           Correlations          Intervention plan
PathLabs report                                         Trajectories          8 Indian languages
🚨 The Problem
Challenge	Scale
Indians with diabetes	77 million (2nd highest globally)
Pre-diabetics (unaware)	136 million
Deaths from chronic diseases	63% of all deaths
Healthcare spending out-of-pocket	70% of families
Average diabetes detection delay	5-7 years
Preventable chronic diseases	80% with early intervention
Doctors' appointment duration	15 minutes (can't track multi-year trends)
The core issue: Healthcare in India is reactive. We wait for symptoms before acting. By diagnosis, organ damage has already occurred. Blood tests contain warning signals years before symptoms — but nobody connects the dots.

What happens today:
  Year 1: HbA1c 5.4% → "Normal"
  Year 2: HbA1c 5.6% → "Normal"
  Year 3: HbA1c 5.8% → "Normal"
  Year 4: HbA1c 6.1% → "Normal"
  Year 5: HbA1c 6.5% → "DIABETIC" 😱
                        ↑
                The rising trend was visible 4 years ago.
                Nobody tracked it.

What BioClock does:
  Year 1: HbA1c 5.4% → BioClock: "Rising at 0.15%/year"
  Year 2: HbA1c 5.6% → BioClock: "Pre-diabetic trajectory detected"
  Year 3: HbA1c 5.8% → BioClock: "78% diabetes risk in 3 years"
                        → "Start ragi diet, Surya Namaskar, Vitamin D"
                        → "Will cross threshold in 18 months without intervention"
💡 Our Solution
BioClock provides instant, AI-powered disease risk prediction with India-specific interventions — from a routine blood test.

┌──────────────────────────────────────────────────────────────────┐
│                          BioClock                                 │
│                                                                   │
│  INPUT              AI PIPELINE                OUTPUT             │
│  ─────              ───────────                ──────             │
│  Lab Report  ────→  Bedrock AI Extraction  ──→  12 Biomarkers     │
│  (text paste)       Agent 1: Temporal      ──→  Risk Level        │
│                     Supervisor: SKIP/CONT      Disease Predictions│
│  OR Manual          Agent 2: Correlator    ──→  1yr/3yr/5yr/10yr  │
│  12 Biomarkers      Agent 3: Trajectory    ──→  Intervention Plan │
│                     Agent 4: Intervention   ──→  8 Languages       │
│                                                                   │
│  Total Time: 20-40 seconds  │  Cost: ~$0.12/analysis             │
└──────────────────────────────────────────────────────────────────┘
🏗️ System Architecture
High-Level Flow
┌──────────┐      ┌─────────────┐      ┌──────────────┐      ┌─────────────────┐
│  React   │      │ API Gateway │      │  Cognito JWT │      │  Lambda         │
│  Web App │────▶│  REST API   │────▶│  Authorizer  │────▶│  bioclock-      │
│ (Amplify)│      │  ap-south-1 │      │              │      │  ingest         │
└──────────┘      └─────────────┘      └──────────────┘      └────────┬────────┘
                                                                      │
                                                                      ▼
                                                            ┌─────────────────┐
                                                            │  S3 Bronze      │
                                                            │  medallion/     │
                                                            │  bronze/        │
                                                            │  {patient_id}/  │
                                                            └────────┬────────┘
                                                                     │
                                                                     ▼
                                                            ┌─────────────────┐
                                                            │  Lambda         │
                                                            │  bioclock-etl   │
                                                            │  Bronze→Silver  │
                                                            │  →Gold          │
                                                            └────────┬────────┘
                                                                     │
                                                            ┌────────▼────────┐
                                                            │  DynamoDB       │
                                                            │  (risk update)  │
                                                            └────────┬────────┘
                                                                     │
                                                                     ▼
                                                            ┌─────────────────┐
                                                            │  Lambda         │
                                                            │  Function URL   │
                                                            │  bioclock-      │
                                                            │  analyze        │
                                                            └────────┬────────┘
                                                                     │
                              ┌──────────────────────────────────────┤
                              │           SEQUENTIAL                 │
                              ▼                                      │
                    ┌─────────────────┐                              │
                    │  Lambda         │                              │
                    │  Agent 1:       │                              │
                    │  Temporal       │                              │
                    │  (Bedrock Nova) │                              │
                    └────────┬────────┘                              │
                             │                                       │
                             ▼                                       │
                    ┌─────────────────┐                              │
                    │  🧠 SUPERVISOR  │                              │
                    │  SKIP or        │                              │
                    │  CONTINUE?      │──── SKIP ──→ Return (60%    │
                    └────────┬────────┘              cost saved)     │
                             │ CONTINUE                              │
                             ▼                                       │
                    ┌─────────────────┐                              │
                    │  Lambda         │                              │
                    │  Agent 2:       │                              │
                    │  Correlator     │                              │
                    │  (Bedrock Nova) │                              │
                    └────────┬────────┘                              │
                             │                                       │
                             ▼                                       │
                    ┌─────────────────┐                              │
                    │  Lambda         │                              │
                    │  Agent 3:       │                              │
                    │  Trajectory     │                              │
                    │  (Bedrock Nova) │                              │
                    └────────┬────────┘                              │
                             │                                       │
                             ▼                                       │
                    ┌─────────────────┐                              │
                    │  Lambda         │                              │
                    │  Agent 4:       │                              │
                    │  Intervention   │                              │
                    │  (Bedrock Nova) │                              │
                    │  → S3 Gold      │                              │
                    │  → DynamoDB     │                              │
                    └────────┬────────┘                              │
                             │                                       │
                             ▼                                       │
                    ┌─────────────────┐                              │
                    │  Results to UI  │◀─────────────────────────────┘
                    │  + S3 audit     │
                    └─────────────────┘

PARALLEL BATCH PIPELINE (Glue):
  knowledge/CSV  →  Glue Job 1  →  bronze-parquet/
                    Glue Job 2  →  silver-parquet/ (partitioned by risk)
                    Glue Job 3  →  gold-parquet/
                                       │
                                 Glue Crawlers → Glue Catalog
                                       │
                                 Athena SQL queries
🤖 AI Agent Pipeline
4 Specialist Agents + Autonomous Supervisor
                        ┌──────────────────────────────────────┐
                        │        PATIENT GOLD DATA              │
                        │  12 biomarkers, trends, risk flags    │
                        └──────────────────┬───────────────────┘
                                           │
                                           ▼
                              ┌─────────────────────┐
                              │  📈 AGENT 1:         │
                              │  TEMPORAL ANALYZER   │
                              │                      │
                              │  • Rate of change    │
                              │  • Trend acceleration│
                              │  • Threshold timing  │
                              │  • Pattern detection │
                              │                      │
                              │  "HbA1c rising at    │
                              │   0.15%/month —      │
                              │   crosses 6.5% in    │
                              │   8 months"          │
                              └──────────┬───────────┘
                                         │
                                         ▼
                              ┌─────────────────────┐
                              │  🧠 SUPERVISOR       │
                              │                      │
                              │  Risk = LOW +        │
                              │  All Stable?         │
                              │                      │
                              │  YES → SKIP          │
                              │  (save 60% cost)     │
                              │                      │
                              │  NO → CONTINUE       │
                              │  (full 4-agent)      │
                              └──────────┬───────────┘
                                         │ CONTINUE
                                         ▼
                              ┌─────────────────────┐
                              │  🔗 AGENT 2:         │
                              │  CORRELATOR          │
                              │                      │
                              │  • Metabolic syndrome│
                              │  • Atherogenic dysli │
                              │  • Insulin resistance│
                              │  • CV risk cluster   │
                              │  • TG/HDL ratio calc │
                              │                      │
                              │  "TG 210 + HDL 39 =  │
                              │   Atherogenic Dysli- │
                              │   pidemia (TG/HDL    │
                              │   5.38, high risk)"  │
                              └──────────┬───────────┘
                                         │
                                         ▼
                              ┌─────────────────────┐
                              │  🔮 AGENT 3:         │
                              │  TRAJECTORY          │
                              │                      │
                              │  • 1yr probability   │
                              │  • 3yr probability   │
                              │  • 5yr probability   │
                              │  • 10yr probability  │
                              │  • 6+ diseases       │
                              │                      │
                              │  "Type 2 Diabetes:   │
                              │   1yr: 45%           │
                              │   3yr: 72%           │
                              │   5yr: 90%           │
                              │   10yr: 98%"         │
                              └──────────┬───────────┘
                                         │
                                         ▼
                              ┌─────────────────────┐
                              │  💊 AGENT 4:         │
                              │  INTERVENTION        │
                              │                      │
                              │  • Indian diet plan  │
                              │  • Yoga + exercise   │
                              │  • Supplements       │
                              │  • Monitoring        │
                              │  • Referrals         │
                              │                      │
                              │  "Replace rice with  │
                              │   ragi roti. Add 25g │
                              │   methi seeds daily. │
                              │   Surya Namaskar 12  │
                              │   rounds. Vitamin D3 │
                              │   60,000 IU/week."   │
                              └─────────────────────┘
RAG Context (Indian Medical Knowledge)
Every agent prompt includes 120+ lines of Indian medical reference data:

Category	Data Source	Key Facts
Metabolic	ICMR 2023, INDIAB Study	Indian BMI cutoff 23 (not 30), 11.4% diabetes prevalence
Cardiovascular	INTERHEART South Asia	Indians get MI at age 53 (vs 63 in West), atherogenic dyslipidemia #1 pattern
Renal	SEEK-India	17% CKD prevalence, diabetes + hypertension = dual hit
Thyroid	Indian Thyroid Society	11% prevalence, higher in women and coastal regions
Vitamin D	INDIAB, CURES studies	70-80% urban Indians deficient despite tropical climate
Diet	NIN (Hyderabad)	Ragi GI 54, Jowar GI 62, Methi reduces FG by 12-15 mg/dL
Exercise	Yoga Mimamsa Journal	Surya Namaskar improves insulin sensitivity by 20%
3-Layer Fallback Mechanism
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Layer 1          │     │  Layer 2          │     │  Layer 3          │     │  Layer 4          │
│  Nova Lite        │────▶│  Nova Pro         │────▶│  S3 Cache         │────▶│  Rule-Based       │
│  (3 retries)      │fail │  (3 retries)      │fail │  (last success)   │fail │  (hardcoded)      │
│  $0.06/M tokens   │     │  $0.80/M tokens   │     │  $0 (free)        │     │  $0 (free)        │
└──────────────────┘     └──────────────────┘     └──────────────────┘     └──────────────────┘

Every response includes: _source = "bedrock_live" | "cache" | "rule_based_fallback"
🗄️ Data Lake Architecture
Dual Pipeline (Real-Time + Batch)
┌──────────────────────────────────────────────────────────────────┐
│                    REAL-TIME PIPELINE (Lambda)                     │
│                                                                    │
│  User Input → medallion/bronze/{id}/    (raw JSON, immutable)     │
│            → medallion/silver/{id}/    (validated, flagged)       │
│            → medallion/gold/{id}/      (aggregated, risk-scored)  │
│            → 4 AI Agents analyze Gold data                        │
│            → predictions/{id}/         (disease predictions)      │
│            → agent-sessions/{id}/      (reasoning audit trail)    │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                    BATCH PIPELINE (Glue → Athena)                 │
│                                                                    │
│  knowledge/*.csv → Glue Job 1 → bronze-parquet/ (raw Parquet)    │
│                  → Glue Job 2 → silver-parquet/ (risk flags)     │
│                  → Glue Job 3 → gold-parquet/   (risk scores)    │
│                  → Glue Crawlers → Glue Catalog                   │
│                  → Athena SQL queries for population analytics    │
└──────────────────────────────────────────────────────────────────┘
S3 Bucket Structure
bioclock-health-data/
├── medallion/
│   ├── bronze/{patient_id}/           Raw ingested JSON
│   ├── silver/{patient_id}/           Cleaned + validated
│   └── gold/{patient_id}/             Analytics summaries
├── agents/
│   ├── sessions/{patient_id}/         Agent reasoning chains
│   └── predictions/{patient_id}/      Disease predictions
├── cache/agents/                      Fallback cache (per agent)
├── knowledge/                         Historical CSV data
├── bronze-parquet/                    Glue batch output
├── silver-parquet/                    Partitioned by overall_risk
├── gold-parquet/                      Aggregated analytics
├── reasoning/                         Session reasoning JSONs
└── users/{patient_id}/                User profiles
☁️ AWS Services
13 Services Used
#	Service	Purpose	Region	Tier
1	Amplify	Frontend hosting	us-east-1	Free
2	Cognito	Auth (JWT + email OTP)	ap-south-1	Free (50K MAU)
3	API Gateway	REST API (7 routes)	ap-south-1	Free (1M req)
4	Lambda (×12)	Serverless compute	ap-south-1	Free (1M req)
5	S3	Data lake (Medallion)	ap-south-1	~$1/month
6	DynamoDB	Patient cache + lookups	ap-south-1	Free (25GB)
7	Bedrock	AI inference (Nova Lite/Pro)	us-east-1	~$65/month
8	Translate	8 Indian languages	ap-south-1	~$3/month
9	Glue	3 batch ETL PySpark jobs	ap-south-1	~$3/month
10	Glue Catalog	Schema metadata for Athena	ap-south-1	Free
11	Athena	SQL on Parquet	ap-south-1	$5/TB scanned
12	CloudWatch	Logging + billing alarms	ap-south-1	Free tier
13	IAM	Least-privilege roles	Global	Free
Additional: Lambda Function URL (bypasses API Gateway 29s timeout), Step Functions (orchestration definition)

Total monthly cost: Under $100

Lambda Function Configurations
Function	Timeout	Memory	IAM Policies
bioclock-ingest	30s	128MB	S3 + DynamoDB
bioclock-etl	1 min	256MB	S3 + DynamoDB
bioclock-analyze	5 min	512MB	S3 + Lambda (invoke agents)
bioclock-agent-temporal	2 min	256MB	S3 + Bedrock
bioclock-agent-correlator	2 min	256MB	S3 + Bedrock
bioclock-agent-trajectory	2 min	256MB	S3 + Bedrock
bioclock-agent-intervention	2 min	256MB	S3 + Bedrock
bioclock-patients	30s	128MB	DynamoDB
bioclock-predict	30s	128MB	DynamoDB + S3
bioclock-reasoning	30s	128MB	S3
bioclock-translate	2 min	256MB	Translate + Bedrock
bioclock-parse-report	2 min	256MB	S3 + Bedrock
🚀 Getting Started
Prerequisites
# Node.js >= 18
node --version

# npm >= 9
npm --version
Frontend Setup
# Clone repository
git clone https://github.com/Khushi-Oswal/temp.git
cd temp/frontend

# Install dependencies
npm install

# Start development server
npm run dev
# Opens at http://localhost:5173

# Build for production
npm run build
# Output in dist/ folder
Configuration
Edit frontend/src/utils/auth.jsx:

const POOL_DATA = {
  UserPoolId: 'ap-south-1_XXXXXXXX',   // Your Cognito User Pool ID
  ClientId: 'xxxxxxxxxxxxxxxxxx',        // Your App Client ID
}
Edit frontend/src/utils/api.js:

const BASE_URL = 'https://YOUR-API-ID.execute-api.ap-south-1.amazonaws.com/Dev'
const ANALYZE_URL = 'https://YOUR-FUNCTION-URL.lambda-url.ap-south-1.on.aws'
Deploy to AWS Amplify
cd frontend
npm run build
cd dist
zip -r ../deploy.zip .
# Upload deploy.zip to Amplify Console → Deploy updates
Amplify SPA Rewrite Rule (Required)
Source: /<*>    Target: /index.html    Status: 404-200
Deploy Lambda Functions
Deploy all 12 Python files from backend/lambda/ to AWS Lambda Console:

# For each Lambda function:
1. Lambda Console → Create/Select function
2. Paste code from backend/lambda/bioclock-{name}.py
3. Set Runtime: Python 3.11
4. Set Timeout and Memory (see table above)
5. Add Environment Variables: BUCKET_NAME, BEDROCK_REGION, DYNAMODB_TABLE
6. Add IAM Policies (see table above)
7. Click Deploy
Deploy Glue Scripts
# Upload Glue scripts to S3
aws s3 cp backend/glue/bioclock-bronze-etl.py s3://bioclock-health-data/glue-scripts/
aws s3 cp backend/glue/bioclock-silver-etl.py s3://bioclock-health-data/glue-scripts/
aws s3 cp backend/glue/bioclock-gold-etl.py   s3://bioclock-health-data/glue-scripts/

# Create Glue Jobs in Console:
# - Glue version: 4.0
# - Worker type: G.1X
# - Number of workers: 2
# - Timeout: 10 minutes
Create DynamoDB Tables
# Table 1: Patient records
aws dynamodb create-table \
  --table-name bioclock-patients \
  --attribute-definitions AttributeName=patient_id,AttributeType=S \
  --key-schema AttributeName=patient_id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region ap-south-1

# Table 2: Prediction cache
aws dynamodb create-table \
  --table-name bioclock-predictions \
  --attribute-definitions AttributeName=patient_id,AttributeType=S \
  --key-schema AttributeName=patient_id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region ap-south-1
📡 API Reference
Submit Patient Data
POST /api/ingest
Authorization: Bearer {cognito_jwt}
Content-Type: application/json

{
  "patient_id": "BIO8X2B14HH",
  "name": "Arjun Sharma",
  "age": 45,
  "sex": "Male",
  "city": "Mumbai",
  "bmi": 27.4,
  "smoker": true,
  "family_history": true,
  "hba1c": 6.8,
  "fasting_glucose": 118,
  "systolic_bp": 142,
  "diastolic_bp": 88,
  "ldl": 145,
  "hdl": 38,
  "triglycerides": 195,
  "creatinine": 1.1,
  "tsh": 3.2,
  "vitamin_d": 18,
  "uric_acid": 6.8,
  "hemoglobin": 13.5
}
Response:

{
  "message": "Patient data ingested successfully",
  "patient_id": "BIO8X2B14HH",
  "bronze_key": "medallion/bronze/BIO8X2B14HH/20260308_091523_abc12345.json",
  "biomarkers_count": 12,
  "source": "manual_entry",
  "storage": "dynamodb + s3"
}
AI Lab Report Parsing
POST /api/parse-report
Content-Type: application/json

{
  "report_text": "Apollo Diagnostics\nPatient: Arjun Sharma, 45/M\nHbA1c: 6.8%\nFasting Blood Sugar: 118 mg/dL\nTotal Cholesterol: 220 mg/dL\nLDL: 145 mg/dL\nHDL: 38 mg/dL\nTriglycerides: 195 mg/dL..."
}
Response:

{
  "extracted_biomarkers": {
    "hba1c": 6.8,
    "fasting_glucose": 118,
    "ldl": 145,
    "hdl": 38,
    "triglycerides": 195
  },
  "patient_info": {
    "name": "Arjun Sharma",
    "age": 45,
    "sex": "Male"
  },
  "biomarkers_found": 12,
  "confidence": "high",
  "_model_used": "amazon.nova-lite-v1:0"
}
Run AI Analysis
POST https://{function-url}.lambda-url.ap-south-1.on.aws/
Content-Type: application/json

{
  "patient_id": "BIO8X2B14HH"
}
Response (complete — all 4 agents):

{
  "session_id": "session_BIO8X2B14HH_20260308_094655",
  "patient_id": "BIO8X2B14HH",
  "elapsed_seconds": 20.18,
  "agents_run": 4,
  "overall_risk": "critical",
  "supervisor": {
    "decision": "CONTINUE",
    "reason": "Agent 1 found critical risk. Full 4-agent chain required."
  },
  "reasoning": {
    "agent1": "Temporal analysis reveals critical HbA1c at 6.8%, Systolic BP 142mmHg...",
    "agent2": "Metabolic syndrome confirmed (4/5 criteria met). Atherogenic dyslipidemia...",
    "agent3": "Type 2 Diabetes 90% in 1yr. Hypertension 85% in 1yr...",
    "agent4": "Replace rice with ragi roti. Surya Namaskar 12 rounds daily. Vitamin D3 60,000 IU/week..."
  },
  "predictions": [
    {"disease": "Type 2 Diabetes", "probability_1yr": 90, "probability_5yr": 98},
    {"disease": "Hypertension", "probability_1yr": 85, "probability_5yr": 95},
    {"disease": "Coronary Artery Disease", "probability_1yr": 40, "probability_5yr": 75}
  ],
  "fallback_info": {
    "agent1_source": "bedrock_live",
    "agent2_source": "bedrock_live",
    "agent3_source": "bedrock_live",
    "agent4_source": "bedrock_live"
  }
}
Get Patient List (Per-User Filtered)
GET /api/patients?user_id={cognito_sub}
Authorization: Bearer {cognito_jwt}
Translate
POST /api/translate
Content-Type: application/json

{
  "text": "Your HbA1c is 6.8% indicating diabetes...",
  "target_language": "hi"
}
📁 Project Structure
bioclock/
├── 📄  README.md                                 # This file
├── 🔒  .gitignore                                # Git ignore rules
│
├── 📁  frontend/                                  # React + Vite + Tailwind
│   ├── 📦  package.json                          # Dependencies (React 18, Vite, Tailwind)
│   ├── 📦  package-lock.json                     # Locked versions
│   ├── 🌐  index.html                            # Vite HTML shell
│   ├── ⚙️   vite.config.js                       # Vite build configuration
│   ├── 🎨  tailwind.config.js                    # Tailwind CSS configuration
│   ├── ⚙️   postcss.config.js                    # PostCSS configuration
│   ├── 📄  amplify.yml                           # AWS Amplify CI/CD build spec
│   ├── 🔒  .gitignore                            # Frontend-specific ignores
│   │
│   └── 📁  src/
│       ├── ⚛️   App.jsx                           # Root: Router + AuthProvider + LanguageProvider
│       ├── ⚛️   main.jsx                          # Vite entry point
│       ├── 🎨  index.css                          # Global styles (light theme)
│       │
│       ├── 📁  utils/
│       │   ├── 📄  api.js                        # Axios + JWT interceptor + all API calls
│       │   ├── ⚛️   auth.jsx                      # Cognito SDK: signup, login, verify, JWT
│       │   ├── ⚛️   i18n.jsx                      # 8-language translations (100+ keys)
│       │   ├── 📄  health.js                     # Biomarker reference ranges + units
│       │   └── 📄  toast.js                      # Toast notification system
│       │
│       ├── 📁  components/
│       │   ├── ⚛️   Layout.jsx                    # Navbar + Language Switcher + Spinner + RiskBadge
│       │   ├── ⚛️   AgentPanel.jsx                # Agent flow diagram + reasoning panels
│       │   ├── ⚛️   Charts.jsx                    # Disease prediction charts + progress bars
│       │   ├── ⚛️   HealthTools.jsx               # Health utility components
│       │   └── ⚛️   PatientCard.jsx               # Patient card + KPI stat card
│       │
│       └── 📁  pages/
│           ├── ⚛️   Landing.jsx                   # Homepage: "Predict. Prevent. Thrive."
│           ├── ⚛️   Login.jsx                     # Cognito: login + signup + email verify
│           ├── ⚛️   Dashboard.jsx                 # Per-user patient list + risk KPIs
│           ├── ⚛️   PatientInput.jsx              # AI report upload + manual entry + analysis
│           ├── ⚛️   PatientDetail.jsx             # Biomarker cards + predictions + reasoning
│           └── ⚛️   AnalyzeResults.jsx            # 4-agent analysis + progress animation
│
├── 📁  backend/
│   ├── 📁  lambda/                                # 12 Lambda functions (Python 3.11)
│   │   ├── 🐍  bioclock-ingest.py                # Save to DynamoDB + S3 Bronze (user-linked)
│   │   ├── 🐍  bioclock-etl.py                   # Bronze→Silver→Gold + DynamoDB risk update
│   │   ├── 🐍  bioclock-analyze.py               # Orchestrator: invokes 4 agents sequentially
│   │   ├── 🐍  bioclock-agent-temporal.py        # Agent 1: Temporal patterns (120+ lines RAG)
│   │   ├── 🐍  bioclock-agent-correlator.py      # Agent 2: Cross-biomarker correlations
│   │   ├── 🐍  bioclock-agent-trajectory.py      # Agent 3: Disease predictions at 1/3/5/10yr
│   │   ├── 🐍  bioclock-agent-intervention.py    # Agent 4: India-specific intervention plans
│   │   ├── 🐍  bioclock-patients.py              # List patients (DynamoDB, per-user filtered)
│   │   ├── 🐍  bioclock-predict.py               # Get predictions (DynamoDB + S3 fallback)
│   │   ├── 🐍  bioclock-reasoning.py             # Get agent reasoning chain from S3
│   │   ├── 🐍  bioclock-translate.py             # Amazon Translate (8 languages + Bedrock fallback)
│   │   └── 🐍  bioclock-parse-report.py          # Bedrock AI reads lab report text → JSON
│   │
│   ├── 📁  glue/                                  # 3 PySpark ETL scripts
│   │   ├── 🐍  bioclock-bronze-etl.py            # CSV → Bronze Parquet (audit columns)
│   │   ├── 🐍  bioclock-silver-etl.py            # Bronze → Silver (7 risk flags, Indian criteria)
│   │   └── 🐍  bioclock-gold-etl.py              # Silver → Gold (risk scores, disease labels)
│   │
│   └── 📁  step-functions/
│       └── 📄  bioclock-step-functions.json       # State machine: Ingest→ETL→Agents with branching
│
└── 📁  sample-data/                               # 5 test lab reports (PDF)
    ├── 📄  01_HEALTHY_Low_Risk_Sneha_Reddy.pdf
    ├── 📄  02_MODERATE_Risk_Vikram_Mehta.pdf
    ├── 📄  03_HIGH_Risk_Arjun_Sharma.pdf
    ├── 📄  04_CRITICAL_Risk_Rajesh_Iyer.pdf
    └── 📄  05_QUICK_TEST_Priya_Patel.pdf
🏆 Hackathon Highlights
AWS AI for Bharat — Key Innovations
┌──────────────────────────────────────────────────────────────────┐
│                    INNOVATION HIGHLIGHTS                          │
│                                                                   │
│  1. AI IS LOAD-BEARING (NOT DECORATIVE)                          │
│     Remove AI → system stops. Lab parsing, predictions,          │
│     interventions, translation ALL require Bedrock.              │
│                                                                   │
│  2. AUTONOMOUS SUPERVISOR                                        │
│     AI decides analysis depth. Healthy patients skip 3           │
│     Bedrock calls = 60% cost savings. True agentic behavior.    │
│                                                                   │
│  3. AGENT REASONING CHAIN                                        │
│     Each agent builds on previous output. Agent 3's              │
│     predictions differ based on Agent 2's findings.              │
│     Not parallel calls — contextual sequential reasoning.        │
│                                                                   │
│  4. INDIA-SPECIFIC MEDICAL KNOWLEDGE                             │
│     BMI 23 cutoff, ICMR guidelines, INDIAB study data,          │
│     South Asian Paradox, atherogenic dyslipidemia patterns.      │
│     120+ lines of RAG context per agent.                         │
│                                                                   │
│  5. MULTILINGUAL BY DESIGN                                       │
│     Hindi · Tamil · Telugu · Marathi · Bengali                   │
│     Kannada · Gujarati · Punjabi · English                       │
│     Client-side i18n (instant) + Amazon Translate (dynamic)      │
│                                                                   │
│  6. CULTURAL INTERVENTIONS                                       │
│     Ragi/Jowar/Bajra diet, Surya Namaskar, Pranayama,           │
│     Ekadashi fasting, cooking in iron kadhai, methi seeds.       │
│     Not generic Western recommendations.                         │
│                                                                   │
│  7. 3-LAYER FALLBACK = ZERO DOWNTIME                             │
│     Nova Lite → Nova Pro → S3 Cache → Rule-based.               │
│     Every response tagged with _source for transparency.         │
│                                                                   │
│  8. SERVERLESS + AFFORDABLE                                      │
│     Under $100/month. Free tier for Lambda, DynamoDB,            │
│     API Gateway, Amplify, Cognito. Zero infrastructure.          │
│                                                                   │
│  9. FULL AUDIT TRAIL                                             │
│     Every analysis saved to S3 with session ID.                  │
│     Agent reasoning chains fully transparent and auditable.      │
│                                                                   │
│  10. DUAL DATA PIPELINE                                          │
│      Real-time (Lambda): User → Bronze → Silver → Gold → AI      │
│      Batch (Glue): CSV → Parquet → Athena → Population analytics │
└──────────────────────────────────────────────────────────────────┘
Target Impact
77M+ diabetics in India (early detection saves lives)
136M pre-diabetics (intervention window before diagnosis)
80% of chronic diseases are preventable with early action
60% cost reduction in healthcare with prevention vs treatment
8 languages covering 90%+ of India's population
<div align="center"> **🧬 BioClock — Transforming Indian Healthcare with AI** *Predict. Prevent. Thrive.* `ap-south-1` · `Amazon Bedrock` · `Serverless` · `Made for Bharat` </div>
