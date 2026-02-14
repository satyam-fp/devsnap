# update-profile

Automatically generate an impact-driven summary of recent work and update your GitHub profile README.

---

## Trigger

The user invokes this skill by running:

```
/update-profile
```

---

## Instructions

When this skill is invoked, follow these steps **exactly**:

### Step 1 — Gather Data

1. **Git commits**: Run `git log --oneline -5` in the current working directory to retrieve the last 5 commits. Parse each commit message for what was accomplished.
2. **Session context**: Review the current conversation history for any tasks completed, bugs fixed, features built, or concepts explored during this session.

### Step 2 — Classify Progress

Categorize every meaningful item from Step 1 into exactly one of these three buckets:

| Category | Icon | What belongs here |
|---|---|---|
| **Shipping** | :rocket: | New features, completed projects, first releases, shipped endpoints, deployed services |
| **Learning** | :books: | New libraries adopted, languages explored, research papers read, unfamiliar domains entered |
| **Refining** | :wrench: | Optimizations, bug fixes, performance improvements, architectural refactors, test coverage gains |

### Step 3 — Write the Summary

Compose the summary following these rules:

- **Tone**: Professional, high-energy, and technical — but accessible to a non-expert reader.
- **Focus on impact, not activity.** Write *"Optimized 3D mesh generation speed by 20%"* instead of *"Worked on mesh code."*
- **Format**: Choose whichever fits the volume of material better:
  - **Bulleted list** (when there are 3+ distinct items):
    ```
    - :rocket: Shipped a real-time WebSocket notification service handling 10k concurrent connections.
    - :books: Picked up Rust's `tokio` async runtime for the first time.
    - :wrench: Cut Docker image size from 1.2 GB to 340 MB by switching to a multi-stage build.
    ```
  - **3-sentence narrative** (when items are closely related):
    ```
    Shipped v2.0 of the analytics dashboard with sub-100ms query latency. Learned BigQuery's optimized JOIN strategies along the way. Refactored the ETL pipeline to reduce daily compute costs by 35%.
    ```

### Step 4 — Locate the Profile README

1. Determine the user's GitHub username. Check, in order:
   - The git remote of the **current repo** (`git remote get-url origin` → extract the owner).
   - Ask the user if it cannot be inferred.
2. Look for a local directory at the **same level as the current repo** named after that username (this is the conventional GitHub profile repo, e.g., `../satyam-fp/`).
3. Inside that directory, open `README.md`.

### Step 5 — Patch the README

1. **Look for sentinel tags:**
   ```html
   <!-- RECENT-VELOCITY:START -->
   ...existing content...
   <!-- RECENT-VELOCITY:END -->
   ```
2. **If the tags exist:** Replace everything between them (exclusive of the tags themselves) with the new summary from Step 3.
3. **If the tags do NOT exist:** Append the following block to the **end** of the README:

   ```markdown

   ## :hammer_and_wrench: Recent Velocity

   <!-- RECENT-VELOCITY:START -->
   {summary from Step 3}
   <!-- RECENT-VELOCITY:END -->
   ```

4. Write the file back to disk using the Edit or Write tool.

### Step 6 — Report Back

Print a short confirmation to the user:

```
Profile README updated with your latest velocity snapshot.
Categories: {list categories that had entries}
File: {absolute path to the README that was modified}
```

---

## Constraints

- Never fabricate commits or activity. Only summarize what is verifiable from git log output or the current session.
- If there are zero meaningful items to report, inform the user instead of writing an empty section.
- Do not remove or alter any content in the README outside of the sentinel tags or the appended section.
