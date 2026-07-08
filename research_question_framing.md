Research Question Framing

Author: Rida Tanveer
Date: July 2026
Status: Provisional — lane and question may be revised by end of Week 4

Provisional Lane

Content vs. Performance Alignment

This lane looks at pages that are already ranking for search queries, and checks whether what's actually on the page matches what the searcher is trying to accomplish. Misalignment — ranking for a query the content doesn't fully serve — is often invisible until content and intent are compared side-by-side at scale.

The Question

For a sample set of ranking (query, URL) pairs, where does the page's actual content diverge from the underlying intent of the query it ranks for — and does that divergence correlate with weaker performance signals (lower estimated CTR relative to position, higher bounce/pogo-sticking, or unstable rank over time)?

Restated as a decision question: Which currently-ranking pages have a content/intent mismatch large enough, and a performance cost clear enough, that rewriting them should be prioritized over writing new pages from scratch?

Unit of Analysis

A (query, ranking URL) pair — not the page alone, and not the query alone. Alignment is inherently relational: the same page can be well-aligned for one query it ranks for and poorly aligned for another. Analyzing at the pair level keeps that distinction visible instead of averaging it away.

Output

A ranked table of (query, URL) pairs, each scored on two axes:


Alignment score — how well the page's content matches the query's inferred intent (e.g., semantic similarity between a query-intent representation and the page content, or intent-category matching)
Performance signal — a proxy for how the pair is actually doing (estimated CTR vs. expected CTR for that position, or rank volatility over the sample window)


Pairs get bucketed roughly as:


Misaligned & underperforming — top priority
Misaligned but performing — edge case worth a second look (maybe the intent read is wrong)
Aligned but underperforming — probably not a content problem; likely technical or authority-related
Aligned & performing — leave alone


Decision & Action

A content strategist or SEO lead uses the "misaligned & underperforming" bucket to decide which 3–5 existing pages get rewritten or rebriefed next sprint, instead of defaulting to writing new pages from scratch.

Cost of a Wrong Recommendation


Rewriting a page that wasn't actually the problem — burns editor/writer hours, and if the rewrite strips out content serving a different sub-intent well, it can hurt rankings rather than help.
Mistaking ordinary rank volatility/noise on a small sample for a genuine alignment problem.
Misreading an "aligned but underperforming" pair as a content issue when it's really a technical or backlink/authority issue — fixing the wrong lever wastes the sprint.


Why Data/ML Helps

Manually reading every ranking page against every query it ranks for doesn't scale past a handful of examples — a real site can have hundreds or thousands of ranking query-page pairs. Semantic similarity models make it possible to systematically compare "what does this query want" against "what does this page actually cover," surfacing the biggest gaps for human review instead of a person skimming a spreadsheet and guessing which ones matter.

Why This Isn't Just "Train a Model"

Fitting a similarity or classification model here is the easy part. The real work is:


Defining "intent" categories that are stable and meaningful for this content vertical
Choosing a performance proxy that's honest about its limits (estimated CTR curves are not the same as real Search Console data)
Spot-checking that the model's "misaligned" flags actually look wrong to a human editor, not just mathematically distant
Ruling out confounds — technical SEO, backlink profile, seasonality, SERP feature changes — before recommending a content rewrite


None of that is a modeling problem. It's judgment about what the output can be trusted to mean, and that judgment is the actual deliverable.

Assumptions & Caveats


This framing assumes the capstone runs on a small public or synthetic dataset (e.g., a scraped sample of queries + SERP results, or a public SEO dataset) rather than live client Search Console data — to be confirmed with mentor.
Performance is treated as a proxy, not ground truth, unless real click data becomes available; conclusions should be stated as directional, not definitive.
Any correlation found between "misalignment" and "underperformance" is correlational, not causal — other factors are not controlled for at this stage.
Sample scope will likely be limited to one topic vertical, so findings may not generalize beyond it.
