# 32 Memory Compression

## Purpose

Memory Compression reduces the size of stored information while preserving its essential value. It defines what types of content can be compressed, which strategies to use for each type, and when compression is triggered.

## Compression Targets

1. Mission summary
2. Reusable heuristic
3. Reusable topology pattern
4. Reusable specialist definition
5. Reusable workflow

## Compression Strategies

### 1 Key Extraction

- Input: Verbose reasoning chains, long discussions, multi-step analyses
- Process: Extract the key decisions, conclusions, and rationale. Discard intermediate steps, false starts, and exploratory tangents
- Output: Bullet-point summary of decisions with rationale
- Fidelity: Lossy. Intermediate reasoning is not recoverable
- Use when: Compressing session memory to mission memory. Compressing working memory to session memory

### 2 Summarization

- Input: Long-form content such as research findings, meeting notes, or analysis documents
- Process: Produce a structured summary that preserves all factual claims, recommendations, and open questions. Reduce prose to concise statements
- Output: Structured summary with sections matching the original's structure
- Fidelity: Lossy but structurally faithful. The summary can be used in place of the original for most purposes
- Use when: Compressing mission memory for archival. Compressing research findings for cross-mission use

### 3 Pattern Extraction

- Input: Successful topology configurations, agent specs that scored High on evaluation, workflow sequences that completed efficiently
- Process: Generalize the specific instance into a reusable template. Remove mission-specific details. Preserve the structural pattern and the conditions under which it worked
- Output: A reusable template (topology pattern, agent spec template, or workflow template)
- Fidelity: Intentionally lossy. Specifics are discarded in favor of generalizability
- Use when: Promoting from Mission or Project Memory to Reusable Memory

### 4 Deduplication

- Input: Memory that has accumulated duplicate or near-duplicate information across sessions or agents
- Process: Identify duplicates by comparing content semantically (not just string matching). Keep the most complete version. Replace others with references to the canonical version
- Output: Deduplicated memory with cross-references
- Fidelity: Lossless. No information is lost, only consolidated
- Use when: Project memory has grown through multiple missions with overlapping scope

### 5 Provenance Preservation

- Input: Any memory being compressed
- Process: Before compression, extract and separately store all provenance links (which agent produced this, what evidence supported it, what decisions it informed). The provenance survives even if the content is compressed
- Output: Compressed content plus intact provenance chain
- Fidelity: Content is lossy. Provenance is lossless
- Use when: Always, when Rigor is Medium or High. When Rigor is Low, provenance preservation is optional

## Compression Triggers

| Trigger | Action |
|---|---|
| Session end | Compress session memory using Key Extraction. Promote results to Mission Memory |
| Mission completion | Compress mission memory using Summarization. Promote patterns using Pattern Extraction |
| Context pressure | The runtime signals that the context window is approaching capacity. Compress working and session memory immediately using Key Extraction |
| COMPRESS CONTEXT command | Human triggers compression (see 09). Compress the specified scope using Key Extraction |
| Memory layer size threshold | When a layer exceeds a size threshold, Deduplication runs automatically. If still over threshold, Summarization runs on the oldest entries |
| Archival | When content moves to Archived Memory, Summarization runs. Original is discarded. Provenance is preserved |

## Compression Quality Check

After compression, validate:

1. All decisions from the original are present in the compressed version
2. All open questions and unresolved uncertainties are preserved
3. All provenance links are intact (when Rigor is Medium or High)
4. The compressed version is self-contained (can be understood without the original)
5. No sensitive information was introduced during compression (no hallucinated additions)

## What Must Not Be Compressed

1. Active assumptions that influence ongoing work (see 27 Mission Graph, Assumption Nodes)
2. Locked assumptions (see 09 LOCK ASSUMPTIONS)
3. Unresolved Quality Gate failures
4. Active blockers
5. The current mission objective and constraints
