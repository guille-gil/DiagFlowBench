# DiagFlowBench — Revision TODO

## A. Paper changes (no new data required)

- [ ] **Limitations**: drop the "domain-agnostic" claim; revise the sentence to describe what is measured (graph structure and entailment between utterance and edge label); frame cross-domain replication as natural future work supported by the released pipeline
- [ ] **Section 3 — failure-mode table**: add a small table defining FA, FM, CA (and IA) alongside the prose definitions already in §3, as requested by Reviewer ZNm
- [ ] **Section 3 (or §5.3) — formal metric definitions**: add displayed definitions for SA, TR, FA, FM, CA, as requested by Reviewer 7YEU
- [ ] **Language statement**: add an explicit sentence stating that all conversations are in English
- [ ] **Related Work — Laban citation**: give \citet{laban2025lost} more prominence; currently cited only in §6.3, should appear in the related work discussion of post-injection effects
- [ ] **Dataset first mention — licence and provenance signpost**: add a sentence at the first mention of the dataset in the paper body pointing to the licence (CC-BY 4.0) and anonymised release
- [ ] **Related Work — hallucination / abstention citation**: cite literature on LLM hallucination attributing part of the effect to benchmarks not offering an explicit abstention option (to support the counterfactual framing in §5.5)

## B. Analysis from existing data (no new inference required)

- [x] **FM/CA uncertainty band**: derive the FM-versus-CA confusion rate from the existing 100-turn judge-validation sample; express as a sensitivity band on the CA and FM columns in Table 2; add a short derivation in Appendix D (new subsection)
  - **Action taken**: derived analytically from κ=0.79, N=98 (po≈0.898, ~10 disagreements). Worst-case all disagreements are FM↔CA: rates of 5/31=16% FM→CA and 5/63=8% CA→FM. Maximum bias ≤9 pp (Qwen3 30B), ≤4 pp for 8 of 10 models, no qualitative ordering reversal. Added as new subsection "Label-error sensitivity" (label: app:sensitivity) in Appendix D of `docs/paper/paper_revised.tex` and `docs/paper/paper_tracking.tex`.

## C. New experiment — abstention counterfactual

**Design**: Re-ran all 1,654 injection turns per model for Llama 3.3 70B, Nemotron 3 Super, Gemini 2.5 Flash under a choice-framed prompt offering SUGGEST A PROCEDURE STEP and ABSTAIN as two explicit equal-weight options. Conversation histories held constant from the original run to isolate the prompt effect. Script at `scripts/run_counterfactual.py`. Results in `DiagFlowBench_Dataset/evaluation_results/counterfactual/`.

- [x] **Run the experiment** — completed, all 1,654 injection turns per model (4,962 total)
  - **Action taken**: ran `scripts/run_counterfactual.py` via OpenRouter; ~$12, ~4h wall time; results and judge cache saved to `evaluation_results/counterfactual/`.

- [x] **Results and interpretation**
  - **Action taken**: FM drops 6.6–14.2 pp under the choice-framed prompt; CA rises to 87–89% for all three models. Residual FM of 9–11.6% persists even under the idealised prompt, confirming a genuine model tendency independent of format. Model ordering preserved.

  | Model | Original FM | CF FM | Δ FM | Original CA | CF CA |
  |---|---|---|---|---|---|
  | Llama 3.3 70B | 15.7% | 9.0% | −6.6 pp | 81.3% | 89.1% |
  | Nemotron 3 Super | 22.1% | 11.6% | −10.6 pp | 73.9% | 87.3% |
  | Gemini 2.5 Flash | 25.6% | 11.4% | −14.2 pp | 72.0% | 87.0% |

  **Framing**: the counterfactual uses a laboratory prompt optimised purely for abstention — not representative of real industrial deployments, where prompts are optimised for task performance. Original FM rates reflect realistic deployment conditions (upper bound); counterfactual establishes an idealised lower bound. Even at that lower bound, forced mapping remains non-trivial.

- [x] **Original prompt correction**: added one sentence to the evaluation prompt in Appendix C of `paper_revised.tex` and `paper_tracking.tex` — "If the operator's observation does not correspond to any node in the procedure graph, do not suggest a procedure step." — and updated `src/prompts/evaluation.yaml` to match. Rationale: the paper is framed as an abstention study; the appendix prompt must reflect that. The brief note also reflects how abstention would appear in a real deployment prompt: one instruction among many, where the model must balance abstention against its primary goal of being a useful assistant and providing actionable advice. The counterfactual framing shifts from "presence vs. absence of abstention" to "implicit note vs. explicit choice-framed option" — and the gap between the two quantifies how much FM is driven by the structural prominence of the abstention affordance rather than its mere presence.
- [ ] **Report results in paper**: add FM/CA comparison table (§5.5 or Appendix E); report Δ FM in pp; use the following framing — "We gave models the most explicit abstention option possible: a choice-framed prompt where ABSTAIN is a named, first-class response equal in weight to SUGGEST A PROCEDURE STEP. FM still did not reach zero. A 9–11.6% residual persists even under this idealised affordance, confirming a genuine model tendency to force-map off-procedure inputs independent of prompt format." Include deployment framing (real prompts are optimised for task performance, not abstention) and note that conversation histories were held constant to isolate the prompt effect.

## D. Reviewer response

- [ ] Draft complete response once B and C are done
- [ ] Confirm Laban citation placement satisfies Reviewer 7YEU framing request
- [ ] Confirm NLI correlation is explicitly deferred to journal extension in the response
