Here is a roadmap of features we can build into the next phase of **mongosh Tutor**:

### 1. Visual Aggregation Pipeline Builder & Debugger

* **Stage-by-Stage Inspector**: Step through `$match`, `$unwind`, `$lookup`, `$group`, and `$project` one stage at a time to see how documents transform at each step.
* **Expanded Pipeline Operators**: Add support for `$lookup` (left outer joins across collections like `orders` and `customers`), `$unwind` (deconstructing arrays like `tags`), and `$addFields`.

### 2. Query Performance & Index Visualizer (`explain()`)

* **Execution Plan Simulator**: Run `.explain("executionStats")` to compare a full Collection Scan (`COLLSCAN`) against an Index Scan (`IXSCAN`).
* **Index Impact Metrics**: Show simulated `totalDocsExamined`, `nReturned`, and execution time differences before and after running `db.collection.createIndex()`.

### 3. Live LLM API Integration (Bring Your Own Key)

* **AI Query Generator & Debugger**: Add an optional settings panel where you can plug in a Gemini or OpenAI API key (or connect to a local Ollama model) so MongoBot can translate natural-language questions into custom `mongosh` queries and diagnose syntax errors on the fly.

### 4. Schema Design, Validation & Data Import/Export

* **JSON/CSV Import & Export**: Upload your own `.json` or `.csv` datasets to practice queries against your real project structures, and export modified collections.
* **JSON Schema Validation (`$jsonSchema`)**: Practice creating collections with strict field types and required rules, testing how `mongosh` rejects invalid document inserts.

### 5. Advanced Shell Scripting & `.mongoshrc.js` Playground

* **Multi-Line Script Editor**: A dedicated editor tab for writing multi-line JavaScript functions, `for` loops, bulk operations (`bulkWrite`), and custom prompt helpers (`~/.mongoshrc.js`).
* **Persistent Progress**: Save your custom databases, command history, and completed challenge badges in `localStorage` so your state stays intact across browser refreshes.

Which of these would you like me to build into the app first?
