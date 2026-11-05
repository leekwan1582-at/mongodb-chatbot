# mongosh Tutor & Interactive Sandbox — Comprehensive User Guide

Welcome to the **mongosh Tutor & Interactive Sandbox**, an interactive web-based learning environment designed to help developers and database administrators master the modern MongoDB Shell (`mongosh`).

This guide details all five core feature modules, the interactive AI mentor, sandbox database tools, keyboard shortcuts, and local deployment options.

---

## Table of Contents

- Overview & Architectural Highlights
- Feature 1: Visual Aggregation Pipeline Builder & Debugger
- Feature 2: Query Performance & Index Visualizer (explain())
- Feature 3: Live LLM API Integration (Bring Your Own Key)
- Feature 4: Schema Design, Validation ($jsonSchema) & Data Import/Export
- Feature 5: Advanced Shell Scripting & ~/.mongoshrc.js Playground
- The mongosh Terminal Simulator & Database Explorer
- Interactive Hands-on Challenges
- Data Persistence & Privacy Policy
- Local Deployment & VS Code / WSL Workflow

---

## Overview & Architectural Highlights

`mongosh` Tutor combines an in-memory MongoDB 7.0 database engine, a full JavaScript (Node.js v20) execution context, and a conversational AI tutor into a single, self-contained application.

| Capability | Specification | Details |
| :--- | :--- | :--- |
| **Shell Version** | `mongosh v2.3.8` | Emulates modern Node.js shell syntax, output formatting, and methods |
| **Server Engine** | `MongoDB v7.0.14` | In-memory BSON document store with query matching, indexes, and validation |
| **Preloaded Datasets** | `shop`, `sample_university`, `analytics` | Realistic e-commerce, university registrar, and event streaming schemas |
| **State Persistence** | Browser `localStorage` | Preserves database modifications, command history, and challenges across reloads |
| **AI Integration** | BYOK (Gemini, OpenAI, Ollama) | Optional live LLM generative query authoring and error diagnosis |
| **Dependencies** | Zero backend required | Operates directly inside any modern web browser or offline local file |

---

## Feature 1: Visual Aggregation Pipeline Builder & Debugger

The Aggregation Pipeline is one of MongoDB's most powerful data transformation features. The **Aggregation Builder** tab allows you to construct multi-stage pipelines visually and inspect documents stage-by-stage.

### Supported Aggregation Stages

| Stage Operator | Purpose | Example Syntax |
| :--- | :--- | :--- |
| **`$match`** | Filters documents entering the pipeline | `[{ $match: { status: "Delivered", total: { $gt: 50 } } }]` |
| **`$lookup`** | Left outer join to another collection in the active DB | `[{ $lookup: { from: "customers", localField: "customer", foreignField: "name", as: "customerDetails" } }]` |
| **`$unwind`** | Deconstructs an array field to output a document per item | `[{ $unwind: "$customerDetails" }]` or `[{ $unwind: "$tags" }]` |
| **`$addFields` / `$set`**| Computes and appends new fields using expressions | `[{ $addFields: { totalWithTax: { $multiply: ["$price", 1.08] } } }]` |
| **`$group`** | Aggregates documents by `_id` with accumulator functions | `[{ $group: { _id: "$category", count: { $sum: 1 }, avgPrice: { $avg: "$price" } } }]` |
| **`$project`** | Reshapes output documents, renaming or filtering fields | `[{ $project: { customer: 1, total: 1, city: "$customerDetails.city", _id: 0 } }]` |
| **`$sort`** | Sorts documents by one or more fields | `[{ $sort: { avgPrice: -1, count: 1 } }]` |
| **`$limit` / `$skip`** | Paginates the document stream | `[{ $skip: 10 }, { $limit: 5 }]` |

### Stage-by-Stage Inspector Workflow

1. Navigate to the **🧪 Aggregation Builder** tab in the right pane.
2. Select your **Source Collection** (e.g. `orders`).
3. Choose a preset or build custom stages using the **➕ Add Stage** button.
4. Click any stage pill in the **Pipeline Progression Stepper** (e.g., `#1 $lookup`, `#2 $unwind`).
5. Inspect the **Transformation Diff Viewer**:
   - **Left Card (Before Stage)**: The document structure entering the selected stage.
   - **Right Card (After Stage)**: The resulting document exiting the stage, highlighting joined fields or array expansions.
   - Use the pagination controls (`◀ Doc X of Y ▶`) to flip through records.
6. Click **▶ Run in Shell** to export and execute the pipeline directly inside the active `mongosh` terminal.

---

## Feature 2: Query Performance & Index Visualizer (explain())

Unindexed queries can degrade database performance by forcing a **Collection Scan (`COLLSCAN`)**. This module simulates MongoDB's query planner and execution metrics in real time.

### Key Metrics Tracked

| Metric | Description | COLLSCAN (Unindexed) | IXSCAN (Indexed) |
| :--- | :--- | :--- | :--- |
| **Winning Stage** | The execution strategy chosen by the query planner | `COLLSCAN` | `FETCH` + `IXSCAN` |
| **`totalDocsExamined`** | Number of physical documents scanned from disk | Scans 100% of collection records | Scans only matching target records |
| **`totalKeysExamined`** | Number of B-tree index entries traversed | `0` (no index used) | Proportional to query filter hits |
| **`nReturned`** | Number of documents matching query criteria | Equal to filter matches | Equal to filter matches |
| **`executionTimeMillis`** | Estimated server execution latency | High latency (proportional to DB size) | Minimal latency (~1ms) |

### Using the Performance Visualizer

1. Open the **⚡ Performance & explain()** tab.
2. Select a collection and query preset (e.g., `price > 50`), or enter a custom JSON query filter.
3. Click **🔬 Analyze Execution Plan**:
   - The left card shows the unindexed `COLLSCAN` penalty.
   - The right card displays the `IXSCAN` B-tree traversal plan.
   - The top banner provides a calculated speedup multiplier (e.g., *~12x faster with 70% less I/O*).
4. Click **Create Index on { field: 1 }** to create an active index dynamically and witness the execution plan switch to `IXSCAN`.
5. Click **▶ Run .explain() in Terminal** to output authentic MongoDB 7.0 JSON diagnostic output matching real server telemetry.

---

## Feature 3: Live LLM API Integration (Bring Your Own Key)

While MongoBot includes an offline rule-based knowledge engine, you can connect your own API key to enable live generative query generation, complex code explanations, and automatic terminal error troubleshooting.

### Supported AI Providers

| Provider | Supported Models | Configuration Requirements |
| :--- | :--- | :--- |
| **Google Gemini API** | `gemini-1.5-flash`, `gemini-1.5-pro`, `gemini-2.0-flash` | API key from [Google AI Studio](https://aistudio.google.com/app/apikey) |
| **OpenAI API** | `gpt-4o-mini`, `gpt-4o`, `gpt-3.5-turbo` | API key from [OpenAI Platform](https://platform.openai.com/api-keys) |
| **Local LLM (Ollama / LM Studio)** | `llama3.1`, `mistral`, `deepseek-r1`, `qwen2.5` | Endpoint URL (e.g. `http://localhost:11434/v1/chat/completions`) |
| **Built-in Offline Engine** | Rule-based Curriculum & Matcher | Zero setup, completely offline |

### Setup Instructions

1. Click **⚡ AI Keys (BYOK)** in the top navigation bar or **⚙️ Built-in** in the chat header.
2. Select your provider from the dropdown.
3. Paste your API key (stored securely in `localStorage`).
4. Click **🧪 Test Key** to send a diagnostic handshake verifying connectivity and latency.
5. Click **Save & Activate**.

### Schema-Aware Assistance & Error Diagnosis

- **Schema Context**: When live AI is enabled, MongoBot automatically includes your active database name and sample document structures in its system prompt, ensuring generated queries match your real collection fields.
- **Runnable Code Actions**: Every generated code snippet includes **▶ Run in Shell**, **✏️ Edit in Prompt**, and **📋 Copy** buttons.
- **Terminal Error Diagnostician**: If a command produces a `MongoServerError` or syntax error in the terminal, click **🤖 Ask MongoBot to diagnose and fix this error** to immediately submit the failing query and error traceback for step-by-step remediation.

---

## Feature 4: Schema Design, Validation ($jsonSchema) & Data Import/Export

MongoDB supports flexible document structures while allowing strict schema enforcement where necessary through `$jsonSchema`.

### 1. JSON Schema Validation Engine

You can define collection rules using native MongoDB syntax in the shell or via the visual modal:

```javascript
db.createCollection("strict_products", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "price", "category"],
      properties: {
        name: {
          bsonType: "string",
          description: "Must be a string and is required"
        },
        price: {
          bsonType: "number",
          minimum: 0,
          description: "Must be a positive number and is required"
        },
        category: {
          bsonType: "string",
          enum: ["Electronics", "Accessories", "Audio", "Apparel"]
        },
        stock: {
          bsonType: "number",
          minimum: 0
        }
      }
    }
  }
})
```

- **Validation Enforcement**: Any `insertOne()`, `insertMany()`, or `updateOne()` call that violates these rules is rejected with an authentic `MongoServerError: Document failed validation`.
- **Schema Modal**: Click **🛡️ $jsonSchema** in the DB Explorer to select pre-built schema templates (e-commerce products, user accounts, strict inventory), edit rules, and test invalid inserts with one click.

### 2. JSON & CSV Data Import

1. In the **🗄️ DB Explorer**, click **📥 Import JSON/CSV**.
2. Select your target collection and choose **Append to existing** or **Replace collection**.
3. Choose a local `.json` or `.csv` file, or paste raw text.
4. The engine automatically coerces types (numbers, booleans), generates `ObjectId`s, previews record counts, validates against active `$jsonSchema` rules, and updates the database.

### 3. Collection Export

1. In the **🗄️ DB Explorer**, click **📤 Export**.
2. Select your desired format:
   - **JSON Document Array (`.json`)**: Exports formatted BSON/JSON records.
   - **CSV Spreadsheet (`.csv`)**: Flattens document properties into tabular rows compatible with Microsoft Excel and Google Sheets.
3. The browser immediately triggers a direct file download.

---

## Feature 5: Advanced Shell Scripting & ~/.mongoshrc.js Playground

`mongosh` is built on a full modern JavaScript V8 runtime. The **Script Editor** tab provides a scratchpad for writing multi-line code, bulk operations, and custom shell prompt configurations.

### Scripting Capabilities

| Feature | Description | Example Usage |
| :--- | :--- | :--- |
| **Custom Prompt** | Overwrite `prompt` with a custom function | `prompt = () => \`[${new Date().toLocaleTimeString()}] ${currentDb}> \`` |
| **For-Loops & Seeding** | Generate batches of records with JavaScript loops | `for (let i = 1; i <= 10; i++) { db.products.insertOne({ ... }); }` |
| **Bulk Operations** | Execute sequential inserts, updates, and deletes | `operations.forEach(op => { db.products.updateOne(...); });` |
| **Custom Helper Methods**| Attach reusable functions to the execution scope | `function categorySummary(cat) { return db.products.find({ category: cat }); }` |

### Script Editor Controls

- **Tab Key Indentation**: Pressing &lt;kbd&gt;Tab&lt;/kbd&gt; inserts 2 spaces without losing focus.
- **Run Shortcut**: Press &lt;kbd&gt;Ctrl+Enter&lt;/kbd&gt; (or &lt;kbd&gt;Cmd+Enter&lt;/kbd&gt; on macOS) to execute the script immediately.
- **Console Output Drawer**: Inspect printed `console.log()` lines, warnings, and return values side-by-side with execution duration in milliseconds.
- **Save as ~/.mongoshrc.js**: Click **💾 Save as ~/.mongoshrc.js** to persist your script. The saved configuration will automatically execute every time your session boots, providing custom shell prompts and global functions permanently.

---

## The mongosh Terminal Simulator & Database Explorer

### Interactive Terminal Features

- **Keyboard History**: Press &lt;kbd&gt;↑&lt;/kbd&gt; and &lt;kbd&gt;↓&lt;/kbd&gt; to cycle through previously executed commands.
- **Tab Autocompletion**: Press &lt;kbd&gt;Tab&lt;/kbd&gt; to autocomplete common collection names and query methods (`db.products.find()`, `show collections`, `aggregate([])`).
- **Screen Clearing**: Type `cls` or `clear` to reset the terminal buffer.
- **Direct JavaScript Evaluation**: Evaluate raw JavaScript expressions like `Math.max(10, 25)`, `new Date().toISOString()`, or `[1, 2, 3].map(x => x * 2)`.

### Database Explorer Features

- **Database Switcher**: Switch between `shop`, `sample_university`, and `analytics`.
- **Collection Tree**: Real-time document count badges that update immediately when insert or delete commands run in the shell.
- **Live Search Filter**: Filter visible documents instantly by typing keywords, field names, or values.
- **Manual Document Insertion**: Click **➕ Insert Document** to open a guided modal for adding custom documents with auto-generated `ObjectId`s.

---

## Interactive Hands-on Challenges

Test your skills with six progressive challenges located in the **🎯 Challenges** tab:

| Level | Title | Objective | Target Methods |
| :---: | :--- | :--- | :--- |
| **1** | Explore & Count Products | Switch to `shop` and count items | `use shop`, `db.products.countDocuments()` |
| **2** | Comparison Filtering | Find Electronics with price < $100 | `db.products.find({ category: "Electronics", price: { $lt: 100 } })` |
| **3** | Projections & Set Inclusion | Query Gold/Platinum customers for name & city | `db.customers.find({ membership: { $in: [...] } }, { name: 1, city: 1, _id: 0 })` |
| **4** | Atomic Updates ($inc, $push) | Increment stock by 10 and push tag "featured" | `db.products.updateOne(..., { $inc: { stock: 10 }, $push: { tags: "featured" } })` |
| **5** | Document Insertion | Add a new product record | `db.products.insertOne({ name: "...", price: ..., category: ... })` |
| **6** | Aggregation Mastery | Group by category for count & average price | `db.products.aggregate([{ $group: ... }, { $sort: ... }])` |

- **Verify Solution**: Click **Verify** on any challenge card to evaluate current database state and earn your **✓ PASSED** badge.
- **Progress Tracking**: Progress (e.g. `4 / 6`) updates automatically across the top navigation bar and persists across sessions.

---

## Data Persistence & Privacy Policy

- **100% Local Execution**: All database records, indexes, validation schemas, command history, and API keys are stored strictly in your browser's `localStorage`.
- **Zero Third-Party Relays**: API calls made when using Gemini, OpenAI, or Ollama communicate directly from your browser to the provider endpoint. Keys and data are never sent to intermediate servers.
- **Reset Sandbox**: Click **Reset Data** in the top navigation bar at any time to clear `localStorage` cache and restore original factory datasets.

---

## Local Deployment & VS Code / WSL Workflow

To take this application offline or run it in your local development environment:

### Option A: Direct Browser File
1. Copy or download `mongosh_learning_chatbot.html`.
2. Double-click the `.html` file to open it directly in Google Chrome, Microsoft Edge, Mozilla Firefox, or Apple Safari.

### Option B: Local HTTP Server (Python / Node)
Open your terminal in the folder containing `mongosh_learning_chatbot.html` and run:

```bash
# Python 3
python3 -m http.server 8000

# Or Node.js (npx)
npx serve .
```

Then visit `http://localhost:8000/mongosh_learning_chatbot.html` in your browser.

### Option C: VS Code & WSL Workflow
1. Open your project directory inside WSL:
   ```bash
   cd ~/projects/mongosh-tutor
   code .
   ```
2. Install the **Live Server** extension in VS Code.
3. Right-click `mongosh_learning_chatbot.html` and select **Open with Live Server**.
4. (Optional) Run a real local MongoDB Docker container alongside the tutor:
   ```bash
   docker run -d -p 27017:27017 --name mongo-dev mongo:latest
   ```
5. Use the official [MongoDB for VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode) extension to run `.mongodb.js` playbooks side-by-side with real databases.
