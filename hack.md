You are working inside a hackathon repo with four given resources: dogfoodhack.com.md, spec.md, fixtures.json, and run.py. Nothing here is décor — every file is a signal, and I want you to extract maximum information from all four before writing a single line of implementation code. Most competitors will skim spec.md, glance at fixtures.json, and start coding. You are going to do the opposite: treat this like a forensic audit of a grading system, because run.py almost certainly IS the grading system, or a close proxy for it.

PHASE 1 — FULL INTELLIGENCE PASS (do not write code yet)
1. Read dogfoodhack.com.md completely. Extract: what the hackathon/org actually cares about, any stated judging criteria, tone/values, anything that hints at what "winning" looks like beyond raw correctness.
2. Read spec.md completely, twice. First pass for explicit requirements. Second pass hunting for implicit or soft requirements — words like "should," "ideally," "bonus," "nice to have," or examples that imply behavior not stated as a hard rule. List every requirement as a numbered checklist, tagging each as HARD REQUIREMENT, IMPLIED REQUIREMENT, or LIKELY BONUS.
3. Read run.py line by line as if it's an adversary you need to fully understand, not a script to run blindly. Identify:
   - Exactly what it checks, how it scores/passes/fails, and in what order.
   - Any tolerance thresholds, timeouts, formatting checks, or comparison logic (exact match vs fuzzy vs structural).
   - Any code paths never exercised by the given fixtures — those are landmines or bonus opportunities.
   - Any edge cases run.py explicitly defends against (tells you what NOT to break).
4. Read fixtures.json fully. For every entry, infer what specific behavior or edge case it's designed to test. Group into categories (happy path, boundary conditions, malformed input, performance stress, format edge cases, etc.). Explicitly flag any fixture testing something spec.md never mentions directly — that's a hidden requirement.
5. Cross-reference all four files against each other. Produce INTEL.md containing: the full requirement checklist, the fixture-to-requirement mapping, anything in run.py unexplained by spec.md/fixtures.json, and a ranked list of what actually separates a passing submission from a dominant one.

PHASE 2 — DECISION-LEVEL IMPLEMENTATION PLAN (this is the most important phase — do not rush it)

Do not write a generic plan. Write PLAN.md as a list of discrete, named engineering decisions. For EVERY decision, use this exact structure:

  DECISION: [short name, e.g. "Output serialization format"]
  WHAT MOST TEAMS WILL DO: [the obvious/lazy/default implementation a typical participant reaches for first]
  WHY THAT'S WEAK: [the specific failure mode, edge case, or judge-visible flaw it creates — be concrete, not vague]
  WHAT WE DO INSTEAD: [the exact alternative approach, named precisely — library, algorithm, data structure, pattern]
  WHY THIS WINS: [the specific mechanism by which this beats the common approach — faster, handles an edge case others miss, matches run.py's exact comparison logic, produces cleaner output a human judge will visually notice, etc. Tie it back to something concrete in INTEL.md whenever possible.]

Cover this decision-by-decision for AT MINIMUM: 
- input parsing / validation strategy
- core algorithm or data model choice
- error handling and malformed-input behavior
- output format and how it's guaranteed to match run.py's exact expectations (byte-for-byte if relevant)
- performance/complexity choices if fixtures.json contains anything resembling scale or stress cases
- how bonus/implied requirements get implemented (not skipped) and where they're placed in the code so a human judge reading it would notice
- logging/README/code clarity decisions if human judges will read the code itself, framed as "this is what makes a grader trust the solution instantly vs. squint at it"

Do not hedge. Every "WHAT WE DO INSTEAD" must be a specific, committed technical decision — not "we could consider X." If two approaches are close, pick one and state why in one sentence. This document is the difference between a team that got lucky and a team that engineered a win — treat it that way.

PHASE 3 — BUILD, RUN, DIAGNOSE, REPEAT
1. Implement exactly against PLAN.md, decision by decision.
2. Run run.py after every meaningful change — not just at the end.
3. On any failure, don't patch the symptom — check whether it reveals a requirement misread in Phase 1, or a decision in PLAN.md that needs updating. Update both docs if so.
4. Once all fixtures pass, write your OWN additional adversarial test cases beyond fixtures.json — inputs a judge or hidden test set would plausibly throw at this that the given fixtures don't cover — and verify against those too.
5. Final pass for code clarity, comments, and a short README explaining what was built and the key decisions made, in case a human judge reads the code itself.

PHASE 4 — REPORT BACK
Give me: (a) INTEL.md findings, (b) PLAN.md in full, (c) confirmation every fixture passes plus your own adversarial tests, (d) anything in spec.md or run.py still ambiguous enough to need my judgment call before final submission.

Do not ask permission to do the deep-reading or planning phases — just do them, then surface PLAN.md to me before touching code if anything is genuinely ambiguous enough to need my input.
