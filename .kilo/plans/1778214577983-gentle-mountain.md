# BERT Attention Visualization Project - Implementation Plan

## Overview
Implement three functions in `mask.py` to enable BERT masked language model predictions and attention visualization, then analyze attention patterns to understand what BERT learns.

## Project Structure
```
/Users/pedromaia/Desktop/attention/
├── mask.py              # Main program (needs 3 functions implemented)
├── analysis.md          # Analysis document (needs completion)
├── requirements.txt     # Dependencies (pillow, tensorflow, transformers)
└── assets/fonts/        # Font files for diagram generation
```

## Tasks to Complete

### Part 1: Implement Core Functions in mask.py

#### Task 1.1: Implement `get_mask_token_index()`
**Location:** mask.py:43-49

**Requirements:**
- Accept `mask_token_id` (int) and `inputs` (transformers.BatchEncoding)
- Return 0-indexed position of mask token in input sequence
- Return `None` if mask token not present
- Assume only one mask token per sequence

**Implementation approach:**
- Access `inputs["input_ids"]` or `inputs.input_ids` to get token ID sequence
- Convert tensor to list/array if needed
- Search for `mask_token_id` in the sequence
- Return index if found, None otherwise

**Code estimate:** 3-5 lines

#### Task 1.2: Implement `get_color_for_attention_score()`
**Location:** mask.py:53-59

**Requirements:**
- Accept `attention_score` (float between 0 and 1)
- Return RGB tuple (int, int, int) where each value is 0-255
- Score 0 → (0, 0, 0) black
- Score 1 → (255, 255, 255) white
- Intermediate scores → gray shades that scale linearly
- All RGB values must be equal (grayscale)
- Can truncate or round (e.g., 0.25 → (63, 63, 63) or (64, 64, 64))

**Implementation approach:**
- Multiply attention_score by 255
- Convert to integer (using int() or round())
- Return tuple with same value three times

**Code estimate:** 2-3 lines

#### Task 1.3: Implement `visualize_attentions()`
**Location:** mask.py:63-79

**Requirements:**
- Accept `tokens` (list of strings) and `attentions` (tuple of tensors)
- Generate one diagram per attention head across all layers
- BERT base model: 12 layers × 12 heads = 144 total diagrams
- Current implementation only generates 1 diagram (layer 0, head 0)
- Must extend to all layers and heads

**Attention data structure:**
- `attentions[i][j][k]` where:
  - `i` = layer index (0-11)
  - `j` = beam number (always 0)
  - `k` = head index within layer (0-11)

**Implementation approach:**
- Nested loops: iterate through all layers (0-11) and heads (0-11)
- Call `generate_diagram()` for each combination
- Pass 1-indexed layer/head numbers (layer_number = i+1, head_number = k+1)
- Pass tokens and attention weights: `attentions[i][0][k]`

**Code estimate:** 5-7 lines

### Part 2: Analyze Attention Patterns

#### Task 2.1: Test the Implementation
**Prerequisites:** All three functions implemented

**Testing approach:**
1. Ensure virtual environment is activated (`.venv/`)
2. Run: `python mask.py`
3. Test with example sentences:
   - "Then I picked up a [MASK] from the table."
   - "The turtle moved slowly across the [MASK]."
   - Custom sentences with various grammatical structures
4. Verify:
   - Program predicts masked words correctly
   - 144 PNG files generated (Attention_Layer1_Head1.png through Attention_Layer12_Head12.png)
   - Diagrams show grayscale attention patterns

#### Task 2.2: Identify Attention Patterns
**Goal:** Find at least 2 attention heads with interpretable patterns

**Known patterns to avoid (from specification):**
- Layer 3, Head 10: tokens attend to following tokens
- Layer 4, Head 11: adverbs attend to verbs they modify

**Suggested relationships to explore:**
- Verbs and direct objects
- Prepositions and their objects
- Pronouns and their antecedents
- Adjectives and nouns they modify
- Determiners (the, a, an) and nouns
- Tokens attending to preceding tokens
- Subject-verb relationships
- Possessive relationships

**Analysis methodology:**
1. Choose a linguistic relationship to test
2. Create 2-3 test sentences with [MASK] token
3. Run mask.py and examine all 144 attention diagrams
4. Look for patterns where relevant words show high attention (lighter colors)
5. Verify pattern holds across multiple sentences
6. Document findings in analysis.md

**Note:** Attention heads can be noisy - patterns don't need to be perfect

#### Task 2.3: Complete analysis.md
**Location:** analysis.md

**Requirements:**
- Document at least 2 attention heads with identified patterns
- For each pattern:
  - Specify layer number and head number
  - Write 1-2 sentences describing what the head attends to
  - Provide at least 2 example sentences used for testing
  - Patterns must be different from the two examples in specification

**Template structure (already in file):**
```markdown
## Layer X, Head Y

[Description of attention pattern]

Example Sentences:
- [Sentence 1 with [MASK]]
- [Sentence 2 with [MASK]]
```

## Implementation Order

1. **Implement `get_mask_token_index()`** - Required for program to run
2. **Implement `get_color_for_attention_score()`** - Required for diagram generation
3. **Implement `visualize_attentions()`** - Required to generate all 144 diagrams
4. **Test implementation** - Verify program works and generates diagrams
5. **Analyze attention patterns** - Experiment with different sentences
6. **Complete analysis.md** - Document findings

## Technical Considerations

### Dependencies
- Python 3.x with virtual environment
- TensorFlow (for BERT model)
- Transformers library (Hugging Face)
- Pillow (PIL) for image generation
- Font file: assets/fonts/OpenSans-Regular.ttf

### Performance Notes
- First run will download BERT model (~400MB)
- Each run generates 144 PNG files (may take 30-60 seconds)
- Consider creating output directory for diagrams (optional enhancement)

### Potential Issues
- Tensor indexing: May need to convert TensorFlow tensors to numpy arrays
- BatchEncoding access: Use bracket notation or attribute access
- File output: 144 files will be created in current directory

## Testing Strategy

### Unit Testing Approach
1. **Test `get_mask_token_index()`:**
   - Input with mask token → returns correct index
   - Input without mask token → returns None
   - Mask token at different positions

2. **Test `get_color_for_attention_score()`:**
   - Score 0.0 → (0, 0, 0)
   - Score 1.0 → (255, 255, 255)
   - Score 0.5 → (127, 127, 127) or (128, 128, 128)
   - Verify all values are integers

3. **Test `visualize_attentions()`:**
   - Verify 144 files created
   - Check file naming convention
   - Verify diagrams are readable

### Integration Testing
- Run with various sentence structures
- Verify predictions make sense
- Check diagram quality and readability

## Success Criteria

- [ ] All three functions implemented without errors
- [ ] Program runs successfully with test sentences
- [ ] 144 attention diagrams generated per run
- [ ] Diagrams show clear grayscale patterns
- [ ] At least 2 distinct attention patterns identified
- [ ] analysis.md completed with descriptions and examples
- [ ] Patterns are different from the two provided examples

## Estimated Effort

- Function implementation: 15-20 minutes
- Testing and debugging: 10-15 minutes
- Pattern analysis: 30-45 minutes
- Documentation: 10-15 minutes
- **Total: 65-95 minutes**
