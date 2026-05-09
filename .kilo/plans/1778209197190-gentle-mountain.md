# PageRank Implementation Plan

## Overview
Implement three functions in `pagerank.py` to calculate PageRank using two methods:
1. **Random Surfer Model** (sampling-based)
2. **Iterative Algorithm** (formula-based)

## Current State
- File: `/Users/pedromaia/Desktop/pagerank/pagerank.py`
- Three functions need implementation:
  - `transition_model()` - lines 51-60
  - `sample_pagerank()` - lines 63-72
  - `iterate_pagerank()` - lines 75-84
- Constants defined: `DAMPING = 0.85`, `SAMPLES = 10000`
- Helper function `crawl()` already implemented

## Implementation Tasks

### 1. Implement `transition_model(corpus, page, damping_factor)`
**Purpose**: Return probability distribution for next page visit

**Logic**:
- Get total number of pages `N` from corpus
- Check if current `page` has outgoing links
  - **If no links**: Return uniform distribution (1/N for all pages)
  - **If has links**: Calculate probabilities:
    - Base probability for all pages: `(1 - damping_factor) / N`
    - Additional probability for linked pages: `damping_factor / num_links`
- Return dictionary with all pages as keys, probabilities as values

**Edge Cases**:
- Page with no outgoing links → treat as linking to all pages equally
- Probabilities must sum to 1.0

### 2. Implement `sample_pagerank(corpus, damping_factor, n)`
**Purpose**: Estimate PageRank by sampling n pages using random surfer model

**Logic**:
- Initialize visit counter dictionary (all pages → 0)
- **First sample**: Choose random page from all pages
- **Remaining n-1 samples**:
  - Get transition model for current page
  - Use `random.choices()` with probabilities to select next page
  - Increment visit counter for selected page
- Convert counts to proportions (divide each by n)
- Return dictionary with PageRank estimates

**Key Details**:
- Use `random.choice()` for first sample
- Use `random.choices(population, weights)` for subsequent samples
- Track all n samples (including first one)

### 3. Implement `iterate_pagerank(corpus, damping_factor)`
**Purpose**: Calculate PageRank using iterative formula until convergence

**Formula**: 
```
PR(p) = (1-d)/N + d * Σ(PR(i)/NumLinks(i))
```
where i ranges over all pages linking to p

**Logic**:
- Initialize all PageRanks to `1/N`
- Create reverse link mapping (which pages link to each page)
- **Iteration loop**:
  - For each page, calculate new PageRank:
    - Start with `(1 - damping_factor) / N`
    - Add contribution from each incoming link: `damping_factor * PR(i) / NumLinks(i)`
  - Check convergence: max change < 0.001
  - If not converged, update PageRanks and repeat
- Return final PageRank dictionary

**Edge Cases**:
- Page with no outgoing links → treat as linking to all pages (including itself)
- Must handle this when calculating NumLinks(i)

**Convergence**: Stop when no PageRank changes by more than 0.001

## Implementation Notes

### Data Structures
- `corpus`: `dict[str, set[str]]` - page → set of linked pages
- Return values: `dict[str, float]` - page → probability/PageRank
- All return dictionaries must have values summing to 1.0

### Python Modules
- `random.choice(sequence)` - select random element
- `random.choices(population, weights)` - weighted random selection
- No additional imports needed beyond what's already in file

### Testing Strategy
- Test with provided corpus directories (corpus0, corpus1, corpus2)
- Verify both methods produce similar results
- Check all probabilities sum to 1.0
- Verify convergence in iterative method

## Constraints
- Only modify the three specified functions
- No additional imports beyond standard library
- Values must sum to 1.0
- Iterative method: converge within 0.001 threshold
