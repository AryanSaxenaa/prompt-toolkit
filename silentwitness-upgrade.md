Let me build a comprehensive product enhancement blueprint for SilentWitness.Here is the complete product enhancement blueprint — every upgrade is designed to run 100% offline on an 8 GB RAM, CPU-only machine, with Actian VectorAI DB remaining the irreplaceable core.

---

## The philosophy upgrade

Before touching the stack, the framing shifts: SilentWitness is no longer a "CCTV search tool." It is an **offline visual investigation platform** — the kind of tool a forensic analyst, loss-prevention manager, or site security team would pay for. Every enhancement below serves that identity.

---

## 1. Pipeline intelligence: what you add around CLIP

CLIP alone is powerful but narrow. The real product moat comes from enriching every frame with structured signals *before* they reach Actian, so that searches hit on multiple axes simultaneously.

**YOLO-nano object detection (CPU-optimised).** The `yolov8n` model runs in ~80ms per frame on CPU. For every kept frame, you run a quick object detection pass and store the detected class names as a metadata field in Actian: `["person", "handbag", "bottle"]`. This unlocks a query mode that CLIP alone cannot deliver — exact structured filters like "any frame containing a person AND a bag" without relying on semantic approximation. Actian's metadata filtering becomes dramatically more precise.

**Tesseract OCR on every frame.** Security footage often contains license plates, price tags, locker numbers, or digital clocks visible in frame. Running Tesseract on each kept frame and storing extracted text in Actian means a user can search `CAM42 door` — combining text found inside the frame with a camera filter. This is a unique capability no generic video search tool offers.

**Scene-level classification.** A lightweight zero-shot classifier labels each frame as indoor/outdoor, daytime/night, crowded/empty. Stored in Actian as structured metadata, this lets users filter results to "night-time frames only" or "crowded scenes" — the kind of filter that matters enormously in real investigations.

**Background subtraction upgrade.** Replace the basic OpenCV frame-diff with MOG2 (Mixture of Gaussians), which separates moving foreground from static background far more accurately, especially in scenes with slow lighting changes. This reduces junk frames entering Actian and keeps the index lean.

---

## 2. Actian VectorAI DB — deepening its role

Actian is the heart. The upgrade is to treat it as a *multi-index*, not just a vector store.

**Separate tables per signal.** Today you have one frame-vector table. The upgrade: `frame_vectors` (CLIP embeddings), `ocr_tags` (text extracted per frame), `object_detections` (YOLO class arrays), `scene_labels`, `motion_scores`, `events`. Each is a separate Actian table. All join on `frame_id`.

**Hybrid query execution.** A single user query like `"person near shelves after 9pm"` now becomes a multi-part Actian query: vector similarity on CLIP embedding + filter on `hour > 21` + filter on `object_detections CONTAINS 'person'`. Actian handles the metadata filter pushdown before the vector scan, which means the ANN search operates on a dramatically smaller candidate set — faster and more relevant.

**Persistent event graph.** When temporal clustering groups frames into events, those events are stored back into Actian as first-class records — not computed at query time. Each event has its own centroid embedding (average of its constituent frame vectors). Searching at event-level becomes a distinct query mode: faster, higher-level, better for investigation summaries.

**Similarity search at event level.** Instead of "find frames like this frame," the upgrade enables "find events like this event" — using the event centroid stored in Actian. This is the difference between pixel-level recall and scene-level recall.

---

## 3. The reranking engine: smarter than a weighted sum

The current reranker is: `score = semantic_similarity + α × motion_score`. This is fine but not differentiated.

**MMR (Maximal Marginal Relevance).** MMR is a classic IR technique that penalises redundant results. If five frames from the same 10-second burst all score high, MMR surfaces only the most representative one and deprioritises the rest. The user sees diverse results from across the video, not five near-identical frames. This is a straightforward algorithm, runs in pure Python, and makes the results feel significantly more professional.

**Temporal diversity bonus.** In addition to MMR on embedding space, add a light temporal spread bonus: if two results are within 5 seconds of each other, penalise the weaker one. Users get spread across time, not clustered at one incident.

**Object-count boost.** Frames where YOLO detected multiple persons get a small score boost — more activity, more likely to be the interesting moment. This is one line of logic on top of the existing pipeline.

---

## 4. The OCR-and-object search mode

This is the feature that makes the product feel enterprise-ready.

A dedicated search tab called **Structured Search** sits alongside the semantic search bar. It offers:
- Plate / text fragment search: queries the `ocr_tags` table in Actian directly, returns exact frame hits, then fetches their CLIP vectors to offer "find visually similar to this plate sighting" as a follow-up.
- Object filter chips: click-to-toggle chips for `person`, `vehicle`, `bag`, `phone`, `umbrella` etc. These are filter-pushdowns on Actian's `object_detections` table. Zero ML inference at query time — pure structured lookup.

This mode is genuinely useful in investigations where the operator knows exactly what they're looking for (`"find all frames with a blue van"`) rather than relying on semantic approximation.

---

## 5. The case builder — what converts a tool into a product

Right now the output is "here are results." The upgrade adds a **Case Builder** — a lightweight evidence workspace that lives in the React frontend.

A user can:
- Pin frames or events to a case
- Annotate them with freetext notes ("suspect enters at 19:03, exits at 19:17")
- Draw bounding boxes on thumbnails to highlight the area of interest
- Export the case as a self-contained PDF report: timestamp, thumbnail, annotation, camera ID, similarity score, event window duration

The PDF export requires no internet — it uses a lightweight Python library like `reportlab` to assemble a structured investigation report that can be handed to law enforcement or management. This single feature moves the product from "search tool" to "investigation workflow."

---

## 6. The alert engine — proactive mode

An **Alert Rule** is a stored query with a threshold. A user defines: "if a frame matching `person near safe` with score > 0.82 is indexed, trigger an alert."

The alert engine is a lightweight background process that:
- Runs every N seconds (configurable)
- Executes the stored query against Actian
- Compares result scores against the threshold
- Writes to a local `alerts` log and surfaces a notification badge in the UI

This is entirely offline, entirely local, and transforms the product from reactive (search after the fact) to proactive (know as it happens). For live RTSP mode, this becomes a genuine real-time monitoring capability.

---

## 7. The ingestion architecture: Celery + Redis (local)

The current indexing is synchronous — you click scan, it blocks. The upgrade uses **Celery with a local Redis instance** (both Docker containers, zero network dependency).

Every video file becomes a Celery task. Tasks are queued and processed by worker processes. This means:
- UI never freezes during indexing
- Multiple videos index in parallel (bounded to 2 workers on 8 GB RAM)
- Job progress is streamed back to the UI via a WebSocket endpoint
- Failed frames are retried automatically

This is the infrastructure upgrade that makes the product feel stable and producible rather than demo-quality.

---

## 8. RAM-budget-conscious quantisation

On 8 GB RAM with no GPU, the two models that matter are CLIP and Whisper. Both can be quantised for significant RAM reduction.

**CLIP:** Use `openai/clip-vit-base-patch32` with FP16 weights via `torch` — cuts VRAM equivalent memory from ~600 MB to ~300 MB. Embedding quality is perceptually identical for security footage.

**Whisper:** Use `whisper-tiny` quantised via `ctranslate2`. Runs in ~200 MB RAM, transcribes a 10-second voice query in under 3 seconds on CPU. Faster-Whisper is the library.

**YOLO-nano:** `yolov8n.pt` is 6 MB. Runs comfortably in CPU mode with OpenCV's DNN backend as a fallback if PyTorch is unavailable.

Total model footprint at runtime: well under 1.5 GB, leaving 6+ GB for Actian, the OS, and the application — comfortable on an 8 GB machine.

---

## 9. Developer experience: what makes it shippable

**Unified `docker-compose.yml`** spins up: Actian VectorAI DB, FastAPI backend, Celery worker, Redis, React dev server — one command, zero manual setup. This is the difference between a demo and a product someone can actually install.

**`.env` configuration** covers: footage folder path, RTSP stream URLs, alert thresholds, frame sample rate, motion sensitivity, CLIP model choice. No code changes needed for deployment.

**Health dashboard** in the UI: shows Actian connection status, indexed frame count, active jobs, worker queue depth, storage used. An operator can tell at a glance whether the system is healthy.

---

## 10. What the product becomes

After these upgrades, SilentWitness is:

- A **multi-modal investigation platform** — semantic search, structured object/OCR search, and similarity pivot all operating over the same Actian index
- A **proactive monitoring system** — alert rules fire without manual queries
- An **evidence workflow** — case builder and PDF export make it court and management-presentable
- A **stable production application** — async pipeline, job queue, health monitoring
- A **lean CPU application** — quantised models, RAM-aware design, runs comfortably on any modern laptop or office desktop

Actian VectorAI DB is not just storage in this architecture — it is the multi-table, multi-index intelligence layer that every other component feeds into and queries from. Every enrichment (YOLO tags, OCR text, scene labels, event centroids) lands in Actian. Every retrieval (semantic, structural, hybrid, event-level, similarity) goes through Actian. Nothing else in the stack could play this role without being replaced wholesale.
