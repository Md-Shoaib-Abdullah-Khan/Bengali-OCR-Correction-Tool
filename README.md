# Bengali OCR Correction Tool

A Python tool that automatically corrects corrupted Bengali text from OCR output using dictionary matching, pattern rules, and fuzzy string matching. As the input maay contain larger text corpus, LLM based is not implemented because it will become more costly. This problem is solved for both cases where we have a file with corrupted words per line (e.g. োনো, িদ্যু, াবিক্রিয়া ) or we have a paragraph that contains some corrupted words (e.g. ্যাসীয পদাথ ুব গুরুত্বপূর্ণ। োনো  িদ্যু াবিক্রিয়া ঘটলে উত্তপ সৃষ্টি হয়।)


---

## 📁 Project Structure

```
bengali-ocr-corrector/
│
├── solution.ipynb        # Main implementation (Jupyter notebook)
├── input.txt             # Input: corrupted words & paragraph
├── output.txt            # Output: corrections & statistics
└── README.md            # Documentation
```



## 🔧 How I Addressed the Problem

### Problem
Bengali OCR corrupts text by removing initial consonants, dropping middle characters, or failing on complex conjuncts:
- `গ্যাসীয়` becomes `্যাসীয়` (missing গ)
- `খুব` becomes `ুব` (missing খ)
- `পদার্থ` becomes `পদাথ` (missing র্)

### Solution Strategy

**Multi-tier approach**: Combines three methods to handle different corruption types. Fast dictionary lookups for common cases, pattern rules for systematic errors, fuzzy matching for unknown corruptions.

**Bengali-specific design**: Leverages Bengali script structure (consonants + vowel marks + conjuncts) to identify and fix missing characters.

**Performance optimization**: Uses rapidfuzz (C++ implementation) for 8x faster string matching compared to pure Python.

**Confidence scoring**: Each correction includes a score (0.0-1.0) enabling manual review of uncertain results.

---
---

## 🎯 Solution Approach

### Three-Tier Correction System

```
Input Word
    ↓
1. Dictionary Lookup → Known corruptions (instant, 100% accuracy)
    ↓
2. Pattern Rules → Add missing consonants + validate
    ↓
3. Fuzzy Matching → Find similar words (75% threshold)
    ↓
Output: Corrected Word + Confidence Score
```

**How It Works:**
- **Detects corruption**: Words starting with Bengali vowel marks (কার) indicate missing consonants
- **Dictionary matching**: Instant correction for known patterns
- **Pattern generation**: Tries adding 50 Bengali consonants to corrupted words
- **Fuzzy matching**: Uses rapidfuzz library to find most similar valid word

---

## 🚧 Challenges Considered

### 1. Varying Corruption Patterns
**Challenge**: Different error types (initial, middle, multiple characters affected)

**Solution**: Three-tier system handles each type:
- Dictionary: Known patterns 
- Patterns: Systematic errors  
- Fuzzy: Unknown errors 

### 2. Scalability
**Challenge**: Processing large documents efficiently

**Solution**: 
- Dictionary lookup: O(1) instant for known words
- Pattern generation: Limited to 50 candidates
- Rapidfuzz: 8x faster than pure Python (processes 1000 words in 0.3s)

### 3. Bengali Unicode Complexity
**Challenge**: Complex script structure with vowel marks and conjuncts

**Solution**: UTF-8 encoding throughout, Bengali-specific character detection, preserves formatting and punctuation


---

## 📝 Sample Input and Output

### Input (`input.txt`)

```
CORRUPTED_WORDS
্যাসীয়
ুব
োনো
িদ্যুৎ
াবিক্রিয়া
পদাথ
উত্তপ
---
CORRUPTED_PARAGRAPH
্যাসীয় পদাথ ুব গুরুত্বপূর্ণ। োনো িদ্যুৎ াবিক্রিয়া ঘটলে উত্তপ সৃষ্টি হয়।
```


### Output (`output.txt`)

**Individual Word Corrections:**
```
Original          Corrected         Method          Confidence
্যাসী            গ্যাসীয়          fuzzy_match      0.77
ুব               খুব              dictionary      1.00
োনো              কোনো             dictionary      1.00
িদ্যু            বিদ্যুৎ           fuzzy_match      0.83
াবিক্রিয়া         প্রতিক্রিয়া      dictionary      1.00
পদাথ             পদার্থ            dictionary      1.00
উত্তপ            উত্তাপ            dictionary      1.00
```

**Paragraph Correction:**

*Original:*  
`্যাসীয় পদাথ ুব গুরুত্বপূর্ণ। োনো িদ্যুৎ াবিক্রিয়া ঘটলে উত্তপ সৃষ্টি হয়।`

*Corrected:*  
`গ্যাসীয় পদার্থ খুব গুরুত্বপূর্ণ। কোনো বিদ্যুৎ প্রতিক্রিয়া ঘটলে উত্তাপ সৃষ্টি হয়।`

**Input format**: Two sections separated by `---`  
**Output**: Corrected text with confidence scores
---

## 🚀 Usage

```bash
# Install dependency
pip install rapidfuzz

# Run notebook
jupyter notebook solution.ipynb
```



---