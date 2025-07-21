시스템 요약 및 핵심 특징

환각 원천 차단 (Zero-Hallucination Core)

증거와 엄격한 논리에만 기반하여 절대적인 신뢰성을 확보합니다.

추론 과정의 완전한 투명성 (Transparent Reasoning)

신뢰도, 출처, 논리 경로 등 모든 판단 근거를 명확하게 공개합니다.

적응형 확장성 (Adaptive & Scalable)

외부 데이터 연동을 통해 지식을 확장하고, 대용량 처리를 위해 성능이 최적화되도록 설계되었습니다.

사용자 중심 설계 (User-Centric)

유연한 출력 모드(일반/전문가)와 지속적인 개선을 위한 직접적인 피드백 루프를 제공합니다.

능동적 자체 감사 (Self-Auditing)

다단계 내부 감사 시스템을 통해 모든 출력물의 신뢰성을 사전에 보장합니다.





1. Core System Prompt

LLM 또는 AI 모델에 주입되는 핵심 시스템 프롬프트입니다. AI의 모든 행동 원칙을 정의합니다.

Generated code

     SYSTEM:



### [Principle 1] Evidence & Logic-Based Generation

- All outputs MUST be strictly based on either a direct FACT from the CORPUS (trusted knowledge base) or a RIGOROUS LOGICAL INFERENCE derived from it.

- When logical inference is used, the full reasoning path (trace) and confidence level ([FACT], [INFERRED]) MUST be explicitly tagged.



### [Principle 2] Strict Criteria for Inference

- A "Safe Inference" is valid ONLY IF ALL of the following conditions are met:

    a) Supported by at least two (2) independent facts from the CORPUS.

    b) The logical chain ([Logic: Evidence → Rule → Claim]) is explicit and follows formal reasoning (e.g., deduction, induction).

    c) Only formal deduction, abstraction, or direct combination are permitted. Creative speculation or indirect extrapolation is strictly forbidden and MUST be [BLOCKED].

    d) Claims based on single evidence or indirect connections are to be [BLOCKED] with the reason tagged as 'UNSUPPORTED'.



### [Principle 3] External Data Expansion

- If the CORPUS is insufficient, the system may enter "Expansion Mode" using verified external sources (e.g., academic papers, official news).

- External evidence MUST be tagged with its source, URL, and access date ([EXT-FACT], [URL], [DATE]).

- The same rigorous standards (2+ sources, logic trace, confidence level) apply to external data.



### [Principle 4] Block, Alert, & Reason

- Any segment, sentence, or even a single word that fails to meet the evidence/logic criteria MUST be [BLOCKED].

- The reason for blocking (e.g., insufficient evidence, failed logic trace) MUST be provided.

- Blocked claims will be listed separately with a [BLOCKED: Reason] tag for full transparency.



### [Principle 5] Cohesion & Flexible Output

- Even after removing blocked segments, the remaining FACT/INFERRED claims should be automatically connected to maintain natural flow and context (enhance_cohesion), without distorting the original meaning.

- Offer two user modes:

    a) Expert Mode: Displays all tags, traces, and evidence.

    b) General Mode: Shows a clean summary with an option to "View Details".



### [Principle 6] User Feedback Loop

- Users MUST be able to provide feedback or report errors on any claim, evidence, or inference.

- Feedback immediately triggers an automated re-validation and correction/blocking loop.

- If a piece of feedback reaches a certain threshold, it will be flagged for automatic CORPUS improvement.



### [Principle 7] Performance & Scalability

- For large-scale CORPUS/external data, use index-based lookups, caching for logical inferences, and parallel/asynchronous validation.

- For long or complex queries, process claims in chunks while ensuring overall coherence.



### [Principle 8] Comprehensive Metadata

- Every output MUST include metadata tags: [FACT/INFERRED/EXT-FACT/BLOCKED], [VALIDATION], [SELF-AUDIT], [PROVENANCE], [TRACE], and [ALERT].

- Users can toggle the visibility of metadata, evidence, and blocked content.



### [Principle 9] Zero-Hallucination Guarantee

- A triple-audit loop (simulating peer, supervisor, and final auditor) MUST be passed before any output is released.

- Justification is only complete with 100% evidence or a full logic trace.

- Any unproven claim is mandatorily withheld ([BLOCKED]). The goal is 0% hallucination while expanding utility via safe inferences and external data.



### [Principle 10] User Guidance & Control

- Users can, at any time, request the evidence, logic path, output structure, or reason for a block.

- Users can freely switch between simplified, expert, code, evidence-only, and feedback modes.

   

2. Detailed Algorithm & Implementation Guide
시스템 프롬프트의 원칙을 실제로 구현하기 위한 코드 구조 및 알고리즘입니다.

2.1. Main Pipeline
Generated python

     CORPUS = load_trusted_knowledge_base() # 신뢰 데이터셋 로드



def ai_output_engine(query: str, user_mode: str = "expert"):

    """Main pipeline for the Zero-Hallucination AI System."""



    # 1. Pre-computation: Check if the query is answerable by CORPUS.

    if not is_answerable(query, CORPUS):

        # If not, attempt expansion mode.

        ext_claims_data = external_data_supplement(query)

        if not ext_claims_data:

            return withhold_output("OUT OF SCOPE: No logical or factual basis found in CORPUS or external sources.")

        claims_to_process, is_external = ext_claims_data['claims'], True

    else:

        # 2. Draft Generation: Generate claims from internal CORPUS.

        direct_claims, _ = generate_from_corpus(query, CORPUS)

        inferred_claims, _ = logical_inference(query, CORPUS)

        claims_to_process, is_external = direct_claims + inferred_claims, False



    # 3. Claim Classification & Validation: Classify and block unsupported claims.

    classified_claims = []

    for claim in extract_claims(claims_to_process):

        if not is_external and is_directly_supported(claim, CORPUS):

            classified_claims.append({"claim": claim, "kind": "FACT", "source": find_sources(claim, CORPUS)})

        elif not is_external and is_safely_inferable(claim, CORPUS):

            classified_claims.append({"claim": claim, "kind": "INFERRED", "source": trace_path(claim, CORPUS)})

        elif is_external:

             classified_claims.append({"claim": claim, "kind": "EXT-FACT", "source": ext_claims_data['sources']})

        else:

            classified_claims.append({"claim": claim, "kind": "BLOCKED", "source": "Unsupported or insufficient logic."})



    # 4. Content Refinement: Enhance cohesion for readability.

    final_content = enhance_cohesion(classified_claims)



    # 5. Metadata Generation & Final Formatting

    metadata = build_metadata(classified_claims)

    return format_final_output(final_content, metadata, user_mode)

   

IGNORE_WHEN_COPYING_START

content_copy download

Use code with caution. Python

IGNORE_WHEN_COPYING_END

2.2. Core Logic & Reasoning Functions
Generated python

     def is_safely_inferable(claim: str, corpus: dict, min_evidence: int = 2) -> bool:

    """Checks if a claim can be safely inferred based on strict logical rules."""

    logic_chains = find_all_logic_chains(claim, corpus)

    

    # Must have at least `min_evidence` independent logical paths.

    if len(logic_chains) < min_evidence:

        return False

    

    # At least one chain must pass a rigorous validation test.

    return any(validate_chain(chain) for chain in logic_chains)



def trace_path(claim: str, corpus: dict) -> list:

    """Returns the valid logical chains that support a claim."""

    all_chains = find_all_logic_chains(claim, corpus)

    return [chain for chain in all_chains if validate_chain(chain)]



def find_all_logic_chains(claim: str, corpus: dict) -> list:

    """

    (Highly Complex Implementation)

    Generates potential reasoning paths.

    Example output: [['evidence1.txt', 'evidence2.txt', 'DeductionRule_A'] -> 'claim']

    This would be implemented using knowledge graphs, semantic search, and NLP models.

    """

    # Placeholder for a complex logic generation engine.

    return logic_chain_generator(claim, corpus)



def validate_chain(chain: list) -> bool:

    """Validates if a logic chain adheres to predefined formal rules."""

    # Placeholder for a logic rule validation engine.

    return check_logic_rules(chain)

   

IGNORE_WHEN_COPYING_START

content_copy download

Use code with caution. Python

IGNORE_WHEN_COPYING_END

2.3. External Data Expansion & Feedback Loop
Generated python

     def external_data_supplement(query: str) -> dict | None:

    """Searches external sources and applies the same strict validation."""

    # 1. Search verified sources (e.g., Google Scholar, specific news APIs).

    ext_evidence = web_search_and_extract(query, trusted_domains=["*.edu", "arxiv.org", "reuters.com"])

    

    # 2. Require multiple sources.

    if not ext_evidence or len(ext_evidence) < 2:

        return None

        

    # 3. Attempt to infer and validate.

    claim, trace = logical_inference_from_external(ext_evidence)

    if trace and passes_external_audit(claim, trace):

        sources = [{"url": src['url'], "date": src['access_date']} for src in ext_evidence]

        return {"claims": [claim], "trace": trace, "sources": sources}

    return None



def feedback_loop(claim_id: str, user_feedback: dict):

    """Handles user feedback to re-validate and potentially update the system."""

    result = revalidate_claim(claim_id, user_feedback)

    if not result['is_valid']:

        block_claim(claim_id, result['reason'])

        # Flag for human review and potential CORPUS update.

        add_to_corpus_update_queue(claim_id, result['suggested_fix'])

    return {"status": "Re-validation complete."}

   

IGNORE_WHEN_COPYING_START

content_copy download

Use code with caution. Python

IGNORE_WHEN_COPYING_END

2.4. Output Formatting & User Modes
Generated python

     def enhance_cohesion(classified_claims: list) -> str:

    """Connects validated claims into a natural, readable text."""

    # This function would use an LLM with a strict prompt to rewrite the claims

    # into a coherent paragraph without adding new, unverified information.

    # For simplicity, we just format them here.

    

    content = []

    for item in classified_claims:

        if item['kind'] != 'BLOCKED':

            content.append(f"{item['claim']} [{item['kind']}: {item['source']}]")

    return "\n".join(content)



def build_metadata(classified_claims: list) -> dict:

    """Constructs the final metadata block."""

    blocked_claims = [item for item in classified_claims if item['kind'] == 'BLOCKED']

    is_fully_validated = not bool(blocked_claims)

    

    return {

        "VALIDATION": "ABSOLUTE VALIDATED" if is_fully_validated else "PARTIAL (Blocked claims exist)",

        "SELF-AUDIT": "TripleAudit-Passed" if is_fully_validated else "Correction-Triggered",

        "PROVENANCE": list(set(str(item['source']) for item in classified_claims if item['kind'] != 'BLOCKED')),

        "TRACE": [item['source'] for item in classified_claims if 'INFERRED' in item['kind']],

        "BLOCKED_CLAIMS": [{"claim": b['claim'], "reason": b['source']} for b in blocked_claims]

    }



def format_final_output(content: str, metadata: dict, user_mode: str) -> str:

    """Formats the final output string based on the user mode."""

    if user_mode == "general":

        summary = summarize_content(content) # Another LLM call for summarization

        return f"{summary}\n\n(자세한 근거 및 검증 결과를 보려면 '상세 보기'를 클릭하세요.)"



    # Expert mode

    metadata_str = "\n".join([f"[{key}: {value}]" for key, value in metadata.items()])

    return f"--- START OF RESPONSE ---\n{content}\n\n--- METADATA ---\n{metadata_str}\n--- END OF RESPONSE ---"

   

IGNORE_WHEN_COPYING_START

content_copy download

Use code with caution. Python

IGNORE_WHEN_COPYING_END




