ML Task Framing

Builds on: work/research_question_framing.md — Lane: CTR / Engagement Opportunity Scoring
Status: Provisional, subject to revision after signal audit

Mapping to the ML Loop

Using the core contract from docs/ml-intern-dataset-and-lane-guide.md:

Loop stageWhat it means for this laneResearch questionWhich visible pages under-capture clicks/engagement relative to similar pages at the same position?Safe data contractStarter dataset only (content_id-level, anonymized): impressions_90d, ctr, avg_position, position_tier, main_intent, content_type, engagement_rate, scroll_rate, word_count, content_age_days. No raw queries, URLs, or product decision flags.Signal auditExplore how CTR and engagement actually vary by position tier, intent, content type, and freshness — establish what "normal" looks like for each peer group before flagging anything as an outlier.Baseline scoreExpected CTR (or engagement rate) per position-tier peer group (e.g., median CTR for that tier); residual = observed − expected.Selected modeling laneCTR / Engagement Opportunity Scoring — test whether adding more context (intent, content type, freshness, word count) to the expected-value estimate improves the ranking over the tier-only baseline.ValidationCompare feature-adjusted scoring against the simple tier-only baseline using Precision@K; spot-check top flagged pages with a human; run the leakage checklist.Ranked action recommendationsFinal ranked list of review candidates with reason codes, handed to a content strategist for next sprint.

Task Type

Scoring → Ranking. The core output is a continuous "opportunity score" per page (the gap between observed and expected CTR/engagement, adjusted for volume), which is then sorted into a ranked review queue. This is not a clean classification problem by default — there's no natural yes/no ground truth for "needs a rewrite." It can be re-expressed as classification for evaluation purposes (e.g., "is this page in the top-K by score, above a minimum-volume filter?" as a binary label), but that label is itself a threshold I choose, not something the data hands me — so ranking/scoring is the honest primary framing.

Target / Proxy

There is no verified ground-truth label here — only a proxy: expected CTR (or engagement rate), conditioned on position tier and likely content type/intent, computed from the data itself rather than picked as a business rule. The actionable signal is the residual: observed value minus expected value, weighted so low-volume pages don't dominate.

If a binary label is needed to compute Precision@K, the proxy label is: "top-K pages by residual score, above a minimum impressions/sessions threshold" = review candidate. This is explicitly a threshold I define, not a confirmed outcome — per the lane guide, it must be spot-checked by a human reviewer, and it must never be confused with a real future outcome (e.g., "CTR actually improved after review") without a proper before/after design.

Success Metric

Precision@K, where K matches realistic review capacity (e.g., Precision@50 if a reviewer can act on ~50 pages a sprint). Overall accuracy is meaningless here — most pages are fine, and only a minority are genuine opportunities, so a metric that rewards a good top-of-list is what matters. Average Precision as a secondary check if the full ranking order matters, not just the top slice.

Before trusting any model-based score, it has to beat the simplest fair baseline: ranking by tier-adjusted residual alone, not by raw CTR or raw impressions (both would just reproduce the position ranking). If a fancier model doesn't beat that baseline on Precision@K, the honest conclusion is that the fancier model isn't earning its complexity yet — not that the project failed.

What Action This Supports

Same as Week 1: a content strategist or SEO lead uses the ranked "review candidate" list to choose which pages get a title/meta rewrite, an intent-match review, or an on-page engagement review next sprint — instead of guessing, or ranking by raw CTR (which mostly just re-ranks by position).

Why This Isn't Just a Fixed Rule

A flat rule like "flag any page with CTR below 2%" ignores that expected CTR varies hugely by position — the same CTR can be great at position 8 and poor at position 2. An unconditional threshold would mostly rediscover the position ranking, not find real under-performers.

A tier-adjusted baseline (expected CTR per position tier) is already a step up from a flat rule — but it's still only accounting for one factor. The actual ML/analysis question this task is testing is empirical, not assumed: does adding more context (intent, content type, freshness) to the expected-value estimate meaningfully improve Precision@K over the tier-only baseline, or not? That has to be measured, not asserted — and if it doesn't help, the tier-only baseline is the right answer to ship, not a more complex model for its own sake.

Leakage & Confound Notes


Never use FlyRank's own product decision flags (health_score, priority_score, action_type) as a feature — they're the app's existing judgment, not evidence.
All features must be available at the point a reviewer would actually see them — no target-window information leaking into the "expected value" estimate.
Low CTR isn't automatically a title/meta problem — it could be an intent mismatch instead. The ranked output's reason codes need to make this distinction visible, not paper over it.


Assumptions & Caveats


Scoped to the starter dataset (~30,000-row anonymized slice) unless a mentor grants the full warehouse release.
"Review candidate" is a proxy label I define via threshold, not a verified outcome — a flagged page means "worth reviewing first," not "guaranteed to improve if edited."
Position-tier benchmarking assumes similar pages behave similarly within a tier; some unexplained variance will remain.
