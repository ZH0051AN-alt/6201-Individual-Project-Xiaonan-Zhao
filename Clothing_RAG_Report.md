# Report on the Clothing Review RAG System

## Project Outcome and Scope

The prototype answers clothing fit and quality questions using product-filtered Top-3 reviews and GPT-4o mini through OpenRouter, with Review ID citations or an explicit refusal. I compared TF-IDF and Sentence Transformers on identical questions, using 100 reviews split by product into 30 development and 70 evaluation reviews. The final evaluation contains the original 30 questions and 25 supplementary questions. The outcome is a functioning, inspectable prototype.

## What the Project Changes for Shoppers

Today, shoppers must search review pages and reconcile subjective accounts themselves. This prototype changes the interaction: a specific question produces a short answer alongside identifiable evidence. Product filtering prevents mixing evidence from different garments, while refusal makes missing information visible. The practical contribution is easier evidence inspection, not a universal size recommendation or a prediction of returns, which the dataset cannot support.

The project demonstrates a practical improvement over conventional review browsing by turning scattered comments into concise, question-specific answers with traceable evidence. Its contribution is a working prototype that helps shoppers locate relevant experiences and inspect the basis of an answer. Citations and explicit refusals also provide a foundation for more transparent shopping assistance. Although customer benefits have not yet been measured through a user study, the prototype makes those benefits testable.

## Metrics Performance and Its Implications

**Table 1  Retrieval performance on answerable questions**

| Evaluation subset | Answerable cases | TF-IDF Hit / Recall at 3 | Semantic Hit / Recall at 3 |
| --- | ---: | ---: | ---: |
| Original questions | 25 | 88.0% / 77.3% | 88.0% / 62.7% |
| Supplementary questions | 22 | 31.8% / 31.8% | 50.0% / 50.0% |
| Combined questions | 47 | 61.7% / 56.0% | 70.2% / 56.7% |

The initial result challenged my expectation that semantic retrieval would outperform keywords. Both methods reached 88.0 percent Hit@3 on the 25 answerable original questions, but TF-IDF retrieved substantially more gold evidence. Many questions shared words such as small, sheer, and long with reviews; with only ten candidates per product, lexical matching was competitive. This suggests that retrieval choice should follow query characteristics rather than an assumption that embeddings are inherently superior.

I retained the original results and added 25 supplementary questions to investigate wording sensitivity. On the 22 answerable supplementary questions, semantic retrieval achieved 50.0 percent Hit@3 versus 31.8 percent for TF-IDF, an encouraging advantage of 18.2 percentage points. This supports the practical value of semantic retrieval when shoppers express questions differently from the review authors, reducing dependence on exact keyword overlap. Across all 47 answerable questions, semantic retrieval found relevant evidence for 33 questions versus 29 for TF-IDF. Similar overall Recall@3 indicates that its main advantage was finding at least one relevant review, rather than recovering substantially more evidence. Although the small, post-hoc test requires independent validation, these findings identify wording variation as a promising use case for the semantic retrieval component of RAG.

Grounding is the more important release constraint. All 55 outputs passed format and citation checks, yet the recorded manual audit accepted only 44, or 80.0 percent, below the 100 percent grounding goal. This includes ten refusals; among 45 substantive answers, only 34 were grounded, or 75.6 percent. All eight unanswerable cases were refused correctly, but two answerable questions were also refused. Safety and usefulness must therefore be assessed together, rather than rewarding abstention alone.

The 80 percent retrieval target was met on the original answerable subset but missed on the combined set. Grounding passed for 31 of 33 semantic hits versus five of 14 misses, including refusals. This association identifies retrieval coverage as a priority, without proving that retrieval alone caused every failure. Even a successful hit can leave contradictory reviews outside Top-3, allowing a locally plausible answer to misrepresent the broader review record.

The estimated model cost was USD 0.0068637 across 55 questions, approximately USD 0.000125 each. This supports a cheap pilot, not a low total cost of ownership. On a scale, review labour could dominate token cost.

## Evaluation Strengths and Limitations

The fixed baseline, gold Review IDs, product-level split, and deliberately unanswerable questions made failures measurable. Gold evidence was specified for evaluation and was not supplied to GPT. Manual review assessed whether answer claims followed from the supplied evidence, whereas automatic checks only established valid structure and citation membership. That distinction explains why a complete automated pass coexisted with eleven unsupported outputs.

After finding that keyword retrieval performed well in the original test, I added 25 questions to explore whether semantic retrieval would work better when questions used different wording from the reviews. This follow-up test helped investigate my explanation, but further testing is needed before drawing a general conclusion. I therefore report the original and additional results separately to show where each method performed better.

Both retrievers faced identical inputs, but only semantic evidence fed the generation stage. Consequently, the experiment compares retrieval methods, not the end-to-end answer quality of two complete assistants. Gold labels also require scrutiny: excluding a genuinely relevant review would penalise useful retrieval, while vague supported facts could make grounding judgements inconsistent. Independent label review should precede further tuning.

Audit reliability is another limitation. The supplied workbook records one set of binary judgements, with no independent second reviewer or agreement statistic. A future rubric should distinguish unsupported claims, omitted relevant evidence, and whether a refusal is justified. Evidence absent from Top-3 must not be described as absent from all reviews: Q02 denied oversized or unusually long reports although another review described that experience. Hit@3 rewards one relevant item without testing completeness, and Recall@3 does not measure ranking. Neither measures shopper usefulness. The eight unanswerable cases and missing latency measurements also leave safety and responsiveness insufficiently tested.

## Remaining Rough Edges and Future Path

The project now has a working pipeline, but some answers still go beyond what the reviews actually support. For example, Q50 used comments about upper-body fit to suggest who should avoid buying the dress. The reviews could support a description of the fit, but they did not provide enough evidence for that purchasing advice. In other cases, the system treated information missing from the three retrieved reviews as information missing from all reviews. These examples show why answers should describe the available evidence carefully, rather than turn limited observations into general conclusions.

The system must also preserve the distinction between a reviewer’s experience and an objective product fact. For example, “one reviewer found the shoulders tight” is more accurate than “the dress has tight shoulders.” Different shoppers may experience the same garment differently. When reviews disagree, the answer should acknowledge those differences rather than present one experience as a conclusion that applies to everyone.

The next stage would build on the current results by preparing a new test set before further adjustments. I would investigate whether combining keyword and semantic retrieval improves evidence selection. I would also test a second ranking step to reorder retrieved reviews, and compare retrieving three reviews with retrieving more. These choices should be tested on development products before the selected approach is evaluated on the new test set.

Further improvements should focus on answer reliability and practical usefulness. Each statement could be checked against its cited review, and uncertain answers could explain more clearly what the evidence does and does not establish. A second reviewer would help assess the consistency of grounding judgements. Recording response times and comparing the prototype with ordinary review browsing would also help establish whether it saves shoppers time without reducing accuracy. The project provides a useful foundation for these improvements; readiness for a customer pilot should depend on more reliable answers and demonstrated user benefit, alongside retrieval performance.
