# 🏋️ n8n Hevy Gemini Routine Creator

> **Automate your workout routine creation** by converting text descriptions into structured Hevy routines using AI-powered processing with Google Gemini and n8n.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Setup Guide](#setup-guide)
- [How to Import](#how-to-import)
- [Usage](#usage)
- [Workflow Steps](#workflow-steps)
- [API Schema](#api-schema)
- [Troubleshooting](#troubleshooting)
- [Links](#links)

---

## 🎯 Overview

This n8n workflow automates the creation of fitness routines in **Hevy** by:
1. Accepting text-based workout descriptions (via form input)
2. Fetching your existing exercises from Hevy
3. Using **Google Gemini AI** to parse and structure the workout data
4. Creating the routine directly in your Hevy account via API

Perfect for converting handwritten notes, OCR data, or text descriptions into properly formatted Hevy routines! 💪

---

## ✨ Features

- ✅ **AI-Powered Parsing** - Google Gemini intelligently matches exercise names and structures data
- ✅ **Bilingual Support** - Works with English & Spanish input
- ✅ **Automated Exercise Matching** - Matches your text to actual Hevy exercise IDs
- ✅ **Superset Support** - Automatically detects and groups supersets
- ✅ **Tempo Recognition** - Captures tempo information for exercises
- ✅ **Rest Period Detection** - Automatically extracts rest times between exercises
- ✅ **No Manual Data Entry** - From text to Hevy in seconds

---

## 🏗️ Architecture

```
┌─────────────────┐
│  Form Input     │  Paste your workout text
│   (Textarea)    │
└────────┬────────┘
         │
         ▼
┌─────────────────────────┐
│ Fetch Hevy Exercises    │  Download exercise library
│  (Pagination: 100 items)│  (Needed for matching)
└────────┬────────────────┘
         │
         ▼
┌──────────────────────────┐
│  Format & Combine Data   │  Prepare exercise list
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│  Google Gemini AI        │  Parse text & structure JSON
│  (Structured Output)     │  Match exercise IDs
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│  Create Hevy Routine     │  POST to Hevy API
│  (Final REST API Call)   │
└──────────────────────────┘
```

---

## 📦 Prerequisites

Before setting up this workflow, you'll need:

### 1. **n8n Instance**
- [n8n Self-Hosted](https://docs.n8n.io/hosting/) or [n8n Cloud](https://n8n.io/cloud/)
- Admin access to create/import workflows

### 2. **Hevy API Credentials**
- A Hevy account with API access
- **API Key** (obtainable from Hevy settings)
- Create a credential named `hevy-api-key` with header auth

### 3. **Google Gemini API**
- Google Cloud project with Gemini API enabled
- **API Key** from Google Cloud Console
- Minimum quota for text processing

---

## 🚀 Setup Guide

### Step 1: Prepare Your Credentials

#### **Hevy API Key**
1. Log into your [Hevy account](https://hevyapp.com/)
2. Navigate to **Settings** → **API** (or similar)
3. Generate an API key
4. Copy it securely

#### **Google Gemini API Key**
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable **Gemini API**
4. Create an API key in **Credentials** section
5. Copy it securely

### Step 2: Import the Workflow

Follow the tutorial below in **How to Import** section.

### Step 3: Configure Credentials in n8n

1. After importing, open the workflow in n8n
2. **Locate HTTP nodes** (Get Exercise List from Hevy & Create Hevy Routine):
   - Click on the node
   - Find **Authentication** dropdown
   - Create **New HTTP Header Auth** credential
   - Key: `hevy-api-key`
   - Value: (paste your Hevy API key)

3. **Locate Gemini node** (Google Gemini Chat Model):
   - Click on the node
   - Select **New Credential** for Google Gemini
   - Paste your **API Key**

---

## 📥 How to Import

### **Method 1: Direct JSON Import (Recommended)**

1. **Open n8n** and create a new workflow or go to workflows page
2. Click **"↓ Open"** or **"Menu"** → **"Import from file"**
3. Select `n8n-hevy-gemini-template.json` from this repository
4. n8n will automatically create all nodes and connections
5. Proceed to **Step 3: Configure Credentials** above

### **Method 2: Copy-Paste Template**

1. Open `n8n-hevy-gemini-template.json` in this repository
2. Copy the entire JSON content
3. In n8n, go to **Menu** → **Import from Clipboard**
4. Paste the JSON
5. Confirm import

### **Method 3: Git Clone (For Repository Workflow)**

```bash
git clone https://github.com/malopezr7/n8n-hevy-gemini-routine-creator.git
cd n8n-hevy-gemini-routine-creator
# Import n8n-hevy-gemini-template.json through n8n UI
```

---

## 💪 Usage

### **Running the Workflow**

1. **Open the workflow** in n8n editor
2. Click **"Test workflow"** or **"Activate"** (for production)
3. When activated, a webhook will be created with a unique URL
4. Click **"Open Form"** button to access the input form
5. **Paste your workout text** in the textarea field
6. Click **"Submit"**
7. ✅ Your routine will be created in Hevy!

### **Example Input Formats**

```
Pull Day
- Lat Pulldown: 4x8 @ 60kg
- Barbell Rows: 4x6 @ 80kg (Tempo: 3010)
Rest 90s
- Face Pulls: 3x12 @ 20kg
- Bicep Curls: 3x10 @ 15kg (superset)
```

Or in Spanish:
```
Día de Pecho
- Press de Banca: 5x5 @ 100kg
- Aperturas con Mancuernas: 4x8 @ 25kg
Descanso 60s
```

---

## 🔄 Workflow Steps

### **1️⃣ Input Trigger** 
- **Node:** On form submission
- **Type:** Form trigger (webhook)
- **Input:** User pastes workout text in textarea

### **2️⃣ Fetch Exercises**
- **Node:** Get Exercise List from Hevy
- **Action:** Retrieves all exercises from your Hevy account
- **Pagination:** Fetches 100 items per page (max 10 pages)
- **Purpose:** Provides exercise library for AI matching

### **3️⃣ Format Data**
- **Nodes:** Format Exercise Names → Combine Exercise List
- **Action:** Structures exercise data for AI processing
- **Output:** Clean, merged exercise list

### **4️⃣ AI Processing** ⚡
- **Node:** Match Exercises & Structure Routine
- **Model:** Google Gemini (LLM)
- **Parser:** Structured Output Parser
- **Magic:** 
  - Parses OCR/text input
  - Matches exercises to Hevy IDs
  - Detects supersets (A1/A2 format)
  - Extracts tempos, rest times, sets/reps
  - Outputs valid JSON schema

### **5️⃣ Create Routine**
- **Node:** Create Hevy Routine
- **Action:** POST request to Hevy API
- **Endpoint:** `https://api.hevyapp.com/v1/routines`
- **Result:** ✅ Routine appears in your Hevy app!

---

## 📊 API Schema

The workflow uses the following JSON schema for routine creation:

```json
{
  "routine": {
    "title": "string (required)",
    "folder_id": "number | null (optional - null = 'My Routines')",
    "notes": "string (optional)",
    "exercises": [
      {
        "exercise_template_id": "string (required)",
        "superset_id": "integer | null (optional - groups exercises)",
        "rest_seconds": "integer | null (optional - never negative)",
        "notes": "string | null (optional)",
        "sets": [
          {
            "type": "enum: warmup | normal | failure | dropset",
            "weight_kg": "number | null",
            "reps": "integer | null (never negative)",
            "distance_meters": "integer | null",
            "duration_seconds": "integer | null",
            "custom_metric": "number | null (steps/floors)"
          }
        ]
      }
    ]
  }
}
```

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| **"Invalid Authentication"** | Verify Hevy API key is correct and set in HTTP node credentials |
| **"Gemini API Error"** | Check Google API key is valid and Gemini API is enabled in Cloud Console |
| **"Exercise Not Found"** | Ensure the exercise exists in your Hevy account; AI will use closest match |
| **"Webhook Returns 404"** | Activate workflow first or click "Test workflow" to generate webhook URL |
| **"Request Timeout"** | Your Hevy has many exercises; increase timeout in HTTP node settings |
| **"Invalid JSON Output"** | Check Gemini is properly configured; verify input format |

---

## 🔗 Links

- **[Hevy App](https://hevyapp.com/)** - Fitness tracking platform
- **[Hevy API Docs](https://hevyapp.com/api)** - API reference
- **[n8n Documentation](https://docs.n8n.io/)** - n8n workflows & setup
- **[Google Gemini API](https://ai.google.dev/)** - Gemini documentation
- **[n8n Community](https://community.n8n.io/)** - Get help & share workflows

---

## 📄 License

This project is open source. Feel free to use, modify, and share!

---

## 🤝 Contributing

Found a bug or have an improvement? Open an issue or submit a PR! 

---

**Happy lifting! 🏋️‍♂️** Let Gemini automate your routine creation!