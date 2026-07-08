Research Question Framing

**Author:** Rida Tanveer
**Date:** July 2026
**Status:** Provisional — lane and question may be revised by end of Week 4

## Provisional Lane
**CTR / Engagement Opportunity Scoring** — one of the four core lanes defined in `docs/ml-intern-dataset-and-lane-guide.md` (Lane 4).

## The Question
Among pages that are already visible in search (meaningful impressions, a reasonable average position), which ones under-capture clicks or engagement relative to what similar pages typically achieve at that position — and is that gap large and stable enough to be worth a metadata/content review, rather than normal variance or a different root cause entirely?

Restated as a decision question: **Which visible, reasonably-well-ranked pages should be prioritized for a title/meta or on-page review, because their CTR or engagement falls meaningfully short of what pages in the same position tier typically get?**

## Unit of Analysis
One **content item (page), evaluated over a fixed prior window** (e.g., trailing 90 days), benchmarked against a peer group defined by **position tier** (and likely content type / intent, since a how-to page and a product page probably shouldn't share the same expected-CTR curve). The page is the thing being scored; the position tier is what makes the comparison fair.

## Output
A ranked table of pages, each with:
- Observed CTR / engagement rate (and scroll rate, where relevant)
- Expected CTR for that position tier (the peer benchmark)
- A CTR/engagement **gap score** (residual vs. expected)
- Reason codes (e.g., high impressions + low CTR + strong position + enough volume; or enough sessions + weak engagement)
- A suggested action: rewrite title/meta, improve intent match, improve snippet structure, improve on-page engagement, or monitor

## Decision & Action
A content strategist or SEO lead uses the ranked gap list to choose which pages get a title/meta rewrite or on-page review next sprint — instead of relying on raw CTR alone, which is misleading without adjusting for position (higher positions get more clicks regardless of quality).

## Cost of a Wrong Recommendation
- Rewriting a title/meta for a page whose low CTR is actually an intent mismatch, not a metadata problem — burns editor time and can make CTR worse if the new title chases the wrong intent even harder.
- Flagging a low-impression page as a "big opportunity" when the gap is just noise from too little volume.
- Treating a low-engagement page as a title/meta issue when it's really an on-page content-quality issue — fixing the wrong lever wastes the review cycle.

## Why Data/ML Helps
CTR naturally varies by position — position 1 gets far more clicks than position 10 for the same impressions — so ranking pages by raw CTR would just reproduce the position ranking, not reveal real under-performance. Systematically comparing each page's CTR against a fair, position-adjusted expectation, across hundreds or thousands of pages, isn't something a person can eyeball in a spreadsheet. That's what makes a baseline/model useful here, not because the math itself is hard.

## Why This Isn't Just "Train a Model"
The starter pipeline already ships a working baseline and models — fitting a model isn't the hard part. The real work is:
- Choosing a fair peer group (position tier alone, or also content type/intent — these likely have different expected-CTR curves)
- Setting a sensible minimum-volume filter so low-impression pages don't dominate the list with noise
- Telling apart "CTR gap because of a weak title/meta" from "CTR gap because of a genuine intent mismatch" — these need different fixes, and the guide explicitly warns against assuming low CTR always means the title is the problem
- Turning a numeric residual into a reason code and action a content editor can actually trust and act on

## Assumptions & Caveats
- Working from the starter dataset (`data/raw/content_refresh_anonymized.csv`, ~30,000-row anonymized slice) unless a mentor grants access to the full warehouse release; findings are scoped to this slice and may not generalize.
- CTR/engagement figures are aggregated over a fixed 90-day window and are **directional, not causal** — a CTR gap doesn't prove the title/meta is the cause.
- Position-tier benchmarking assumes similar pages behave similarly within a tier; some unexplained variance will remain (seasonality, brand strength, content type not fully captured by the tier).
- A flagged page means "review this first," not "guaranteed to improve if edited" — proving causation would need an actual experiment.
- Low-volume pages will be filtered or flagged separately so noise isn't mistaken for a genuine opportunity.

