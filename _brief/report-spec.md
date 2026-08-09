# Report Spec

## Report identity
- Report name: Model_Report — Patient Pipeline Business Review
- Semantic model: Model_Report (local PBIP + live in Power BI Desktop via powerbi-modeling-mcp)
- Audience: Mixed — executives/leadership and operations staff (SLPs, account managers, care coordinators), in a recurring (monthly) business review meeting
- Primary purpose: Give leadership a fast, honest read on pipeline health, revenue, and retention, while giving operations staff enough drill-in to find stalled patients and act before the next review
- Delivery target: Local PBIP first (build and validate in Desktop). No Fabric workspace connected — publishing is a later decision, not part of this build.

## User decisions and constraints
- Scope: Standard build — 4 pages, covering funnel/pipeline, revenue, NPS, and retention/cohort. Deferred: staff/channel leaderboard page, dedicated what-if scenario page (folded into Operational Pipeline instead), drill-through detail pages.
- Page count: 4
- Interactivity: Moderate — inline slicers on Executive Summary (1 max) and Revenue & NPS (1), a 5-slicer filter rail on Operational Pipeline, a single cohort-year slicer on Retention & Cohort. Cross-filtering default (Filter, not Highlight) throughout.
- Design direction: Clinical Calm tone, single-accent-teal signature (S5, with sage as secondary and red reserved strictly for genuine churn/risk alerts) + hairline rules (S10) instead of borders.
- Publishing: Not in scope for this build.
- Tooling: powerbi-modeling-mcp (connected), powerbi-report-authoring (for PBIR generation/validation), Power BI Desktop (open, available for reload/screenshot), Node.js (available).
- Model edit permissions: Full — this is the same model built and iterated on in this session.
- Accessibility: WCAG AA minimum on every text/background pair; state never encoded by color alone (pair with icon/shape where used); alt text required on every chart.
- Data caveats: Synthetic 180-patient dataset. Patient Status/Churn Reason only populated for the 47 delivered patients (26%) — every visual using these fields must make that "of delivered patients" scope visually explicit, not implied as a share of all 180. Speech-Language Pathologist is 44-value high-cardinality — search dropdown only, never tiles. ~36% of patients have no Account Manager assigned.

## Narrative
- Core story: Referrals are steady, but the pipeline leaks hardest at Funding Approval, and delivered patients churn at a rate (44.7%) worth leadership attention.
- Audience promise: Leadership gets the headline in under 10 seconds; operations staff can drill from that headline straight to the specific patients and stages that need action.
- Key questions answered: Is the pipeline healthy? Where exactly does it leak, and by how much would fixing one stage help? Where does revenue come from and are patients satisfied? Who's churning, why, and how fast do cohorts typically convert?

## Design identity (from `powerbi-report-design` Step 1)
- Tone: Clinical Calm — cool blue-white surface (#F0F9FF), teal + sage accents, generous spacing, healthcare-feeling without being sterile. Matches the AAC/assistive-tech domain directly.
- Signature: Single-accent teal discipline (S5) — one saturated teal (#0D9488) carries emphasis across every KPI, hero chart, and NPS panel; sage (#84CC16) marks revenue/opportunity measures; everything else stays muted grey/slate. Red (#D13438) is reserved exclusively for genuine churn/risk signals, never decorative. Paired with hairline rules (S10) instead of visual borders.
- Brownfield delta: N/A — greenfield build (current report is a single empty default page).

## Page plan (archetypes from `powerbi-report-design` Step 3)

1. Executive Summary
   - Archetype: Executive Summary
   - Layout variant: A (Hero-Right) — 4 KPIs of comparable importance plus one clear hero metric (the funnel shape) with a meaningful explanatory chart
   - Purpose: Leadership comprehends pipeline/revenue/churn state in ≤10 seconds
   - Visuals: 4 KPI cards w/ YoY sparklines · hero funnel-shape chart (referral→delivery stages) · referral-channel variance bar · stalled-patient exception table
   - Fields/measures: # Patients, Referral To Delivery Rate (%), Total First-Year Value, Churn Rate (%), # Patients (ytd)/(ly), Total First-Year Value (ytd)/(ly), the 6 funnel-stage count measures, Days Since Referral [new]
   - Slicers/interactions: One Date[Year] dropdown (top-right, inline)

2. Operational Pipeline
   - Archetype: Analytical Canvas
   - Layout variant: A (Filter-Rail) — 5 filter dimensions justify a left rail (≥50% fill)
   - Purpose: Ops staff diagnose exactly where and for whom the pipeline is stalling, and test what-if scenarios
   - Visuals: 5-slicer filter rail · hero stage pass-through-rate chart · what-if sensitivity chart (Incremental Devices at +10pp) · SLP performance ranking · patient-level detail matrix
   - Fields/measures: Stage Pass-Through Rate (%) [new], Incremental Devices at +10pp, Referral To Delivery Rate (%), Days Since Referral [new], all 6 stage flag columns
   - Slicers/interactions: Referral Channel, Diagnosis, Funding Pathway (tile lists), Speech-Language Pathologist, Account Manager (search dropdowns)

3. Revenue & NPS
   - Archetype: Comparative Benchmark
   - Layout variant: A (Side-by-Side) — ranking revenue by category, small-multiples for the 5 NPS touchpoints
   - Purpose: Show where revenue concentrates and where patient satisfaction lags
   - Visuals: Headline ranked bar (Total First-Year Value by Funding Pathway) · derived-share callout · 5-panel NPS small-multiples (shared scale) · secondary ranked bar (revenue by Diagnosis)
   - Fields/measures: Total First-Year Value, Leading Funding Pathway Share (%) [new], all 5 Average NPS measures
   - Slicers/interactions: One Date[Year] dropdown (top-right, inline)

4. Retention & Cohort
   - Archetype: Analytical Canvas
   - Layout variant: C (Small-Multiples-Grid) — the cohort-curve comparison IS the page; deliberately different variant from page 2 so the two Analytical Canvas pages don't repeat
   - Purpose: Show how referral cohorts convert over time, and what's driving churn among those who convert
   - Visuals: Cohort-curve trellis (one panel per referral month, cumulative devices delivered × months since referral) · churn-reason ranked bar · Active vs. Churned split
   - Fields/measures: # Devices Delivered (cohort, cumulative), Cohort Maturity (%), # Churned Patients, # Active Patients, # Devices Delivered
   - Slicers/interactions: One Date[Year] tile/dropdown to bound which cohorts appear in the trellis

## Design system summary
- Theme: `assets/base.json` adapted — surface #F0F9FF, accent teal #0D9488 + sage #84CC16, alert red #D13438 (alerts only), hairline borders #E5E7EB at 8px radius.
- Color semantics: Volume/count measures (# Patients, # Devices Delivered, NPS) → teal. Revenue/opportunity measures (Total First-Year Value, Incremental Devices at +10pp) → sage. Churn/risk measures (Churn Rate %, # Churned Patients) → red, reserved.
- Typography: Humanist sans display (Segoe UI/Inter) 22–32pt, same family 11pt body. Tabular numerals on all KPI values and table columns.
- Layout: 12×12 grid, FHD 1920×1080, 32px margin, 24px gutter, 8px snap. Hairline rules (#E5E7EB) mark section breaks instead of borders.
- Accessibility: WCAG AA contrast on every pairing; state never color-only; alt text on every chart; SLP/Account Manager use search-enabled dropdowns (44 and 8 values respectively).

## Model requirements
- Existing measures used: # Patients, Referral To Delivery Rate (%), Total First-Year Value, Churn Rate (%), # Active Patients, # Churned Patients, # Devices Delivered, Incremental Devices at +10pp, all 6 funnel-stage counts, all 5 Average NPS measures, # Patients (ytd)/(ly), Total First-Year Value (ytd)/(ly), # Devices Delivered (cohort, cumulative), Cohort Maturity (%)
- New measures required:
  1. `Days Since Referral` — `DATEDIFF('Patients'[Referral Date], TODAY(), DAY)`, for stalled-patient exception views on pages 1 and 2
  2. `Stage Pass-Through Rate (%)` — SELECTEDVALUE-driven against the existing `Funnel Stage` table, reusing the rate logic already inside `Incremental Devices at +10pp`, for the page 2 hero chart
  3. `Leading Funding Pathway Share (%)` — top funding pathway's share of Total First-Year Value, for the page 3 callout
- New calculated columns: None
- Relationship/sort requirements: None — all required relationships and sort-by columns already exist

## Canonical design contract

```yaml
Design Brief:
  generated_by: powerbi-report-design
  contract_version: 1
  mode: greenfield
  design_identity:
    tone: "Clinical Calm — cool blue-white surface, teal/sage accents, generous spacing, healthcare-feeling without being sterile"
    signature: "Single-accent teal discipline (S5): teal (#0D9488) carries every KPI/hero/NPS emphasis, sage (#84CC16) marks revenue/opportunity, everything else greyscale; red (#D13438) reserved strictly for churn/risk alerts. Paired with hairline rules (S10) instead of borders."
  archetype: Executive Summary
  color_map:
    - measure: _Measures[# Patients]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Referral To Delivery Rate (%)]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[# Devices Delivered]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[# Active Patients]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Total First-Year Value]
      color: "#84CC16"
      tint: "#ECFCCB"
    - measure: _Measures[Incremental Devices at +10pp]
      color: "#84CC16"
      tint: "#ECFCCB"
    - measure: _Measures[Churn Rate (%)]
      color: "#D13438"
      tint: "#FDE8E8"
    - measure: _Measures[# Churned Patients]
      color: "#D13438"
      tint: "#FDE8E8"
    - measure: _Measures[Average NPS (TD.com)]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Average NPS (E-Funding)]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Average NPS (Post-Funding Case)]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Average NPS (Post Support)]
      color: "#0D9488"
      tint: "#CCFBF1"
    - measure: _Measures[Average NPS (MYTD)]
      color: "#0D9488"
      tint: "#CCFBF1"
  pages:
    - name: "180 Patients Referred, 26% Reached Delivery — Churn Running High at 45%"
      role: landing
      archetype: Executive
      layout_variant: A
      variant_rationale: "4 KPIs of comparable importance plus one clear hero metric (the funnel shape) with a meaningful explanatory chart — matches Hero-Right's selection signal exactly."
      page_background: "#F0F9FF"
      layout_summary: "KPI quartet (left) + funnel hero (right) prove the headline; channel variance and a stalled-patient exception list give the mixed audience one operational hook without breaking the ≤10s scan budget."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:   [1, 1, 9, 2]
            filters:  [9, 1, 13, 2]
            kpis:     [1, 2, 7, 7]
            hero:     [7, 2, 13, 7]
            variance: [1, 7, 7, 12]
            risk:     [7, 7, 13, 12]
            footer:   [1, 12, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "180 Patients Referred, 26% Reached Delivery — Churn Running High at 45%"
            purpose: "State the pipeline's headline tension before any chart."
          - id: year_slicer
            region: filters
            kind: slicer
            field_bindings: Date[Year]
            slicer_type: dropdown
          - id: kpi_patients
            region: kpis
            kind: cardVisual
            purpose: "How many patients entered the pipeline, and is that growing?"
            field_bindings: { value: _Measures[# Patients], reference: _Measures[# Patients (ly)] }
            color_strategy: measure_match
            slot: 1
            of: 4
          - id: kpi_delivery_rate
            region: kpis
            kind: cardVisual
            purpose: "What share of referred patients ultimately get a device?"
            field_bindings: _Measures[Referral To Delivery Rate (%)]
            color_strategy: measure_match
            slot: 2
            of: 4
          - id: kpi_revenue
            region: kpis
            kind: cardVisual
            purpose: "How much first-year value is the pipeline generating, and is that growing?"
            field_bindings: { value: _Measures[Total First-Year Value], reference: _Measures[Total First-Year Value (ly)] }
            color_strategy: measure_match
            slot: 3
            of: 4
          - id: kpi_churn
            region: kpis
            kind: cardVisual
            purpose: "What share of delivered patients have churned?"
            field_bindings: _Measures[Churn Rate (%)]
            color_strategy: semantic
            slot: 4
            of: 4
          - id: hero_funnel
            region: hero
            kind: funnelChart
            purpose: "Where does the pipeline lose patients between referral and delivery?"
            field_bindings: { values: [_Measures[# Patients], _Measures[# SLP Evaluations Completed], _Measures[# Trials Arranged], _Measures[# Trials Completed], _Measures[# Applications Submitted], _Measures[# Fundings Approved], _Measures[# Devices Delivered]] }
            color_strategy: gradient
          - id: channel_variance
            region: variance
            kind: barChart
            purpose: "Which referral channels are driving the most first-year value?"
            field_bindings: { category: "Referral Channel[Referral Channel]", value: _Measures[Total First-Year Value] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: stalled_patients
            region: risk
            kind: tableEx
            purpose: "Which referred patients have gone the longest without a device decision?"
            field_bindings: ["Patients[Patient ID]", "Referral Channel[Referral Channel]", "Patients[Referral Date]", "_Measures[Days Since Referral]"]
            sort_policy: value_desc
            insight_basis: "Filtered to Device Delivered = No, sorted oldest-referral-first — the operational exception list a leadership+ops audience can act on directly."
          - id: footer_note
            region: footer
            kind: textbox
            text: "Source: Model_1.xlsx patient pipeline export (synthetic data) · Click a channel bar to filter the exception list"
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: hero, pct_of_content: 22.7 }
          balance_rationale: "KPI strip, funnel hero, channel variance, and exception list form four roughly equal quadrants (22.7% each) plus a lean 9% footer — appropriate for a mixed exec+ops landing page where neither the headline nor the operational hook should visually dominate the other."

    - name: "Funding Approval Is the Biggest Leak in the Referral Pipeline"
      role: detail
      archetype: Analytical
      layout_variant: A
      variant_rationale: "5 filter dimensions (Channel, Diagnosis, Funding Pathway, SLP, Account Manager) fill well over 50% of a left rail — Filter-Rail is justified, not Inline-Slicers."
      page_background: "#F0F9FF"
      layout_summary: "Rail filters everything; hero shows per-stage pass-through rate so staff see exactly where the leak is; what-if and SLP-ranking panels below explain impact and ownership; detail matrix names the specific stalled patients."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header: [1, 1, 13, 2]
            rail:   [1, 2, 3, 13]
            hero:   [3, 2, 13, 6]
            whatif: [3, 6, 8, 9]
            slp:    [8, 6, 13, 9]
            detail: [3, 9, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Funding Approval Is the Biggest Leak in the Referral Pipeline"
            purpose: "State the diagnostic finding before the charts that prove it."
          - id: rail_channel
            region: rail
            kind: slicer
            field_bindings: "Referral Channel[Referral Channel]"
            slicer_type: list
            slot: 1
            of: 5
          - id: rail_diagnosis
            region: rail
            kind: slicer
            field_bindings: "Diagnosis[Diagnosis]"
            slicer_type: list
            slot: 2
            of: 5
          - id: rail_funding_pathway
            region: rail
            kind: slicer
            field_bindings: "Funding Pathway[Funding Pathway]"
            slicer_type: list
            slot: 3
            of: 5
          - id: rail_slp
            region: rail
            kind: slicer
            field_bindings: "Speech Language Pathologist[Speech Language Pathologist ID]"
            slicer_type: dropdown
            slot: 4
            of: 5
            insight_basis: "44-value high-cardinality dimension — search-enabled dropdown, never tiles."
          - id: rail_account_manager
            region: rail
            kind: slicer
            field_bindings: "Account Manager[Account Manager ID]"
            slicer_type: dropdown
            slot: 5
            of: 5
            insight_basis: "~36% of patients have no Account Manager — dropdown naturally exposes a (Blank) option rather than hiding it."
          - id: hero_stage_rate
            region: hero
            kind: clusteredColumnChart
            purpose: "Which single stage has the lowest pass-through rate?"
            field_bindings: { category: "Funnel Stage[Stage]", value: "_Measures[Stage Pass-Through Rate (%)]" }
            sort_policy: natural_order
            color_strategy: semantic
            insight_basis: "Natural pipeline order (via Stage Order) matters more than value-sorting here; semantic color (red below 70%, amber 70-85%, green above) flags the leak at a glance."
          - id: whatif_sensitivity
            region: whatif
            kind: barChart
            purpose: "If we improved one stage's rate by 10 percentage points, which stage yields the most additional devices?"
            field_bindings: { category: "Funnel Stage[Stage]", value: _Measures[Incremental Devices at +10pp] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: slp_ranking
            region: slp
            kind: barChart
            purpose: "Which Speech-Language Pathologists have the highest and lowest delivery rates?"
            field_bindings: { category: "Speech Language Pathologist[Speech Language Pathologist ID]", value: _Measures[Referral To Delivery Rate (%)] }
            sort_policy: value_desc
            color_strategy: gradient
            insight_basis: "Filtered to top 10 + bottom 5 by rate — 44 SLPs is too dense to show unfiltered in this panel width."
          - id: patient_detail
            region: detail
            kind: tableEx
            purpose: "Which specific patients are stalled, and at which stage?"
            field_bindings: ["Patients[Patient ID]", "Patients[SLP Evaluation Done]", "Patients[Trial Arranged]", "Patients[Trial Completed]", "Patients[Application Submitted]", "Patients[Funding Approved]", "Patients[Device Delivered]", "_Measures[Days Since Referral]"]
            sort_policy: value_desc
            insight_basis: "Conditional-formatting icons on each Yes/No stage column give a status-board scan; sorted by Days Since Referral desc surfaces the most overdue patients first."
        space_audit:
          content_cell_count: 110
          placed_cell_count: 110
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: hero, pct_of_content: 36.4 }
          balance_rationale: "Hero diagnostic chart and detail matrix are intentionally tied at 36.4% each — the 'where' and the 'who' carry equal weight for an ops audience; what-if and SLP-ranking split the remaining band evenly. Rail (22 cells) excluded from content per filter-rail convention."

    - name: "Where Revenue Concentrates by Funding Pathway, and Where Satisfaction Lags by Touchpoint"
      role: detail
      archetype: Comparative
      layout_variant: A
      variant_rationale: "Ranking funding-pathway and diagnosis revenue, plus comparing 5 NPS touchpoints on a shared scale, is fundamentally a 'relative to what' ranking question — Side-by-Side's headline + small-multiples shape fits directly."
      page_background: "#F0F9FF"
      layout_summary: "Headline ranked bar proves where revenue concentrates; callout adds the derived share figure; NPS small-multiples compare satisfaction across the patient journey on one scale; a secondary ranked bar covers diagnosis without crowding the primary comparison."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:    [1, 1, 9, 2]
            filters:   [9, 1, 13, 2]
            headline:  [1, 2, 8, 6]
            callout:   [8, 2, 13, 6]
            multiples: [1, 6, 13, 10]
            secondary: [1, 10, 7, 13]
            footer:    [7, 10, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Where Revenue Concentrates by Funding Pathway, and Where Satisfaction Lags by Touchpoint"
            purpose: "Frame both halves of the page's comparison before the charts."
          - id: year_slicer
            region: filters
            kind: slicer
            field_bindings: Date[Year]
            slicer_type: dropdown
          - id: revenue_by_pathway
            region: headline
            kind: barChart
            purpose: "Which funding pathway generates the most first-year value?"
            field_bindings: { category: "Funding Pathway[Funding Pathway]", value: _Measures[Total First-Year Value] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: leading_pathway_callout
            region: callout
            kind: textbox
            purpose: "Name the gap between the top funding pathway and the field."
            field_bindings: "_Measures[Leading Funding Pathway Share (%)]"
            callout_value_basis: "Leading funding pathway's share of total first-year value vs. an even split across 4 pathways (25% baseline) — a gap-to-benchmark figure, not a duplicate of the headline chart's absolute values."
          - id: nps_td_com
            region: multiples
            kind: columnChart
            purpose: "How satisfied are patients at the TD.com touchpoint?"
            field_bindings: _Measures[Average NPS (TD.com)]
            color_strategy: measure_match
            slot: 1
            of: 5
          - id: nps_e_funding
            region: multiples
            kind: columnChart
            purpose: "How satisfied are patients during e-funding?"
            field_bindings: _Measures[Average NPS (E-Funding)]
            color_strategy: measure_match
            slot: 2
            of: 5
          - id: nps_post_funding_case
            region: multiples
            kind: columnChart
            purpose: "How satisfied are patients on the post-funding case/call?"
            field_bindings: _Measures[Average NPS (Post-Funding Case)]
            color_strategy: measure_match
            slot: 3
            of: 5
          - id: nps_post_support
            region: multiples
            kind: columnChart
            purpose: "How satisfied are patients after a support interaction?"
            field_bindings: _Measures[Average NPS (Post Support)]
            color_strategy: measure_match
            slot: 4
            of: 5
          - id: nps_mytd
            region: multiples
            kind: columnChart
            purpose: "How satisfied are patients with the MyTD portal?"
            field_bindings: _Measures[Average NPS (MYTD)]
            color_strategy: measure_match
            slot: 5
            of: 5
          - id: revenue_by_diagnosis
            region: secondary
            kind: barChart
            purpose: "Which diagnoses drive the most first-year value?"
            field_bindings: { category: "Diagnosis[Diagnosis]", value: _Measures[Total First-Year Value] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: footer_note
            region: footer
            kind: textbox
            text: "SEK values reflect first-year device + software + accessories revenue for the 47 delivered patients. NPS scored 0-10 per touchpoint."
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: multiples, pct_of_content: 36.4 }
          balance_rationale: "NPS small-multiples (36.4%) lead as the page's second headline story alongside the revenue ranking (21.2%); callout, secondary ranking, and footer fill the remainder without an oversized single-value hero."

    - name: "Devices Typically Take 6-8 Months to Deliver After Referral"
      role: detail
      archetype: Analytical
      layout_variant: C
      variant_rationale: "The analytical question IS comparison across many referral-month cohorts — a trellis grid of cumulative-delivery curves is the page itself, not a supporting panel. Deliberately rotated from page 2's Filter-Rail variant per the no-mono-archetype-repeat rule."
      page_background: "#F0F9FF"
      layout_summary: "Cohort trellis dominates by design (Variant C's explicit intent); churn-reason ranking and Active/Churned split give the retention half of the story equal footing below."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:      [1, 1, 8, 2]
            filters:     [8, 1, 13, 2]
            trellis:     [1, 2, 13, 8]
            ranked:      [1, 8, 9, 12]
            statussplit: [9, 8, 13, 12]
            footer:      [1, 12, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Devices Typically Take 6-8 Months to Deliver After Referral"
            purpose: "State the cohort-curve finding before the trellis that proves it."
          - id: cohort_year_slicer
            region: filters
            kind: slicer
            field_bindings: Date[Year]
            slicer_type: dropdown
            insight_basis: "Bounds which referral-month cohorts populate the trellis so it doesn't overcrowd with 18+ months of panels at once."
          - id: cohort_trellis
            region: trellis
            kind: smallMultiplesChart
            purpose: "How quickly does each referral cohort convert to delivered devices over time?"
            field_bindings: { panel: "Date[Year Month]", category: "Months Since Referral[Months Since Referral]", value: "_Measures[# Devices Delivered (cohort, cumulative)]" }
            sort_policy: natural_order
            color_strategy: measure_match
            insight_basis: "Shared Y axis across all panels is mandatory per Small-Multiples-Grid rules — independent axes would silently distort cohort-to-cohort comparison."
          - id: churn_reason_ranked
            region: ranked
            kind: barChart
            purpose: "What's driving churn among patients who received a device?"
            field_bindings: { category: "Churn Reason[Churn Reason]", value: _Measures[# Churned Patients] }
            sort_policy: value_desc
            color_strategy: semantic
            insight_basis: "Red/semantic color — this is a genuine risk signal, the one place on this page red is used."
          - id: status_split
            region: statussplit
            kind: barChart
            purpose: "Of delivered patients, what share are still active versus churned?"
            field_bindings: { category: "Patient Status[Patient Status]", value: _Measures[# Devices Delivered] }
            sort_policy: value_desc
            color_strategy: semantic
            insight_basis: "Grounds the churn-reason ranking in its base: only the 47 delivered patients, never implied as a share of all 180."
          - id: footer_note
            region: footer
            kind: textbox
            text: "Cohort maturity horizon (92nd percentile of historical time-to-device) is ~7.2 months — cohorts younger than that may still be converting."
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: trellis, pct_of_content: 54.5 }
          balance_rationale: "Trellis exceeds the general 45% non-hero cap deliberately: Small-Multiples-Grid's archetype variant explicitly makes the trellis the page's primary content, not a supporting visual. Churn-reason and status-split panels remain full 4-row regions, not squeezed footnote strips, so they stay independently readable."
  interaction_pattern:
    drill_targets: []
    cross_filter_rules: "Default Filter (not Highlight) behavior throughout. Page 2 rail slicers filter both the hero chart and supporting panels. Page 1 channel-variance bar cross-filters the stalled-patient exception table."
  accessibility:
    alt_text_strategy: "headline+trend for KPI cards and hero/headline charts (state metric + direction); chart+structure for the cohort trellis and NPS small-multiples (describe axis/panel structure, since pattern-reading is the point); comparison framing for all ranked bars (name what's being compared and the sort order)."
    contrast_notes: "Teal #0D9488, sage #84CC16, and red #D13438 all tested for WCAG AA against the #F0F9FF surface. Red never used decoratively — only for Churn Rate, # Churned Patients, and the churn-reason ranking. State on the SLP/Account Manager dropdowns is never color-only."
  theme:
    base: "assets/base.json adapted to Clinical Calm: surface #F0F9FF, dataColors led by #0D9488 (teal) and #84CC16 (sage), sentimentColors using #D13438 for negative/alert, hairline borders #E5E7EB at 8px radius, humanist sans typography (Segoe UI display 22-32pt / body 11pt), tabular numerals on all KPI and table values."
    user_overrides: "None — greenfield build, no existing brand/theme to preserve."
```

## Implementation notes

- Model changes: Add 3 new measures (`Days Since Referral`, `Stage Pass-Through Rate (%)`, `Leading Funding Pathway Share (%)`) to `_Measures`, in the `Core Measures\Conversion Funnel` folder for the latter two and a new `Core Measures\Engagement` placement for the first — validate each with `EVALUATE INFO.MEASURES()` for errors and a spot-check query before authoring visuals against them.
- PBIR/report authoring: Hand off to `powerbi-report-authoring` with this spec. Replace the existing empty default page rather than adding a 5th page.
- Validation: All PBIR JSON parses; `definition.pbir` still points at Model_Report.SemanticModel; 4 pages generated; visual counts match this spec.
- Desktop screenshot verification: Reload Model_Report.pbip in Desktop after authoring; screenshot all 4 pages; verify no visual overlap, SLP/Account Manager slicers are searchable dropdowns (not tiles), and the stalled-patient table sorts oldest-first.
- Publishing boundary: Local only. No Fabric publish step in this build.
- Risks: Synthetic small-N data (some cells single-digit) — verify no visual implies false precision (e.g. NPS averages on a 5-6 person Churn Reason slice). Cohort trellis panel count depends on how many referral months exist in the selected year — verify it doesn't overcrowd; may need to default the Year slicer to the most complete year.
