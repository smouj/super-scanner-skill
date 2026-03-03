---
name: "super-scanner"
version: "2.1.0"
maintainer: "OpenClaw Team <dev@openclaw.ai>"
description: "AI-powered document scanner and analyzer for writing tasks"
tags: ["writing", "ai", "automation", "document-analysis"]
type: "scanner"
platform: ["linux", "macos"]
dependencies:
  - "openclaw-core>=2.0.0"
  - "python>=3.9"
  - "tiktoken>=0.5.0"
  - "transformers>=4.35.0"
  - "torch>=2.0.0"
  - "scikit-learn>=1.3.0"
  - "spacy>=3.7.0"
  - "nltk>=3.8.0"
  - "language-tool-python>=2.7.0"
required_env:
  - "OPENCLOUD_API_KEY"
  - "SCANNER_MODEL"
optional_env:
  - "SCANNER_CACHE_DIR"
  - "MAX_TOKENS"
  - "TEMPERATURE"
  - "SCAN_BATCH_SIZE"
capabilities:
  - "grammar-check"
  - "style-analysis"
  - "readability-score"
  - "sentiment-analysis"
  - "plagiarism-detection"
  - "keyword-extraction"
  - "structure-analysis"
  - "custom-prompt-scan"
---

# Super Scanner

AI-powered document scanner and analyzer specifically designed for writing tasks. Processes text documents through multiple AI models to provide comprehensive analysis including grammar correction, style evaluation, readability metrics, plagiarism detection, and custom AI-powered scans.

## Purpose

Super Scanner addresses real-world writing workflows:
- Pre-publish document validation for blogs, whitepapers, and documentation
- Academic paper quality checks before journal submission
- Code documentation quality assessment
- Email and marketing copy optimization
- Legal document clarity and consistency analysis
- Technical writing readability improvement
- Batch processing of multiple documents in a repository
- Compliance checking against自定义 style guides

## Scope

### Primary Commands

```
super-scanner scan <file> [options]
super-scanner batch <directory> [options]
super-scanner config [subcommand]
super-scanner models [list|info|test]
super-scanner cache [clear|stats]
```

### Command Details

**`super-scanner scan <file>`**
- `--level <basic|standard|deep>`: Analysis depth (default: standard)
- `--fix`: Auto-apply grammar fixes
- `--output <format>`: json, yaml, html, markdown, terminal
- `--checks <comma-separated>`: Specific checks to run (grammar,style,readability,plagiarism,sentiment,keywords,structure)
- `--custom-prompt <string>`: Custom AI analysis prompt
- `--threshold <0.0-1.0>`: Confidence threshold for warnings
- `--exclude <patterns>`: Patterns to exclude from analysis
- `--word-count`: Include detailed word frequency analysis
- `--compare <reference-file>`: Compare against reference document
- `--no-cache`: Bypass cache for fresh analysis

**`super-scanner batch <directory>`**
- `--pattern <glob>`: File pattern (default: `**/*.{md,txt,tex,rst,html}`)
- `--recursive / --no-recursive`: Directory traversal
- `--output-dir <path>`: Save individual reports
- `--summary-file <path>`: Generate batch summary report
- `--fail-on <severity>`: Threshold for batch failure (warning,error,critical)
- `--parallel <n>`: Parallel processing workers
- `--exclude-dirs <comma-separated>`: Directories to skip
- `--config <path>`: Use config file for batch settings

**`super-scanner config`**
- `config set <key> <value>`: Set configuration
- `config get <key>`: Get configuration value
- `config list`: Show all configs
- `config import <file>`: Import config from JSON
- `config export <file>`: Export config to JSON
- `config reset`: Reset to defaults

**`super-scanner models`**
- `models list`: Show available AI models
- `models info <model>`: Detailed model information
- `models test <model>`: Test model responsiveness
- `models set <model>`: Set default model

**`super-scanner cache`**
- `cache clear`: Clear all cached results
- `cache stats`: Show cache statistics
- `cache prune <days>`: Remove entries older than N days

## Work Process

### Document Intake

1. File validation and encoding detection (UTF-8, UTF-16, ISO-8859-1)
2. Format stripping if needed (Markdown, HTML, LaTeX, reStructuredText)
3. Text extraction with structural preservation
4. Initial token counting for model input size estimation
5. Chunking for large documents (>4000 tokens)

### Analysis Pipeline

#### Stage 1: Grammar & Spelling
- Uses LanguageTool + custom ML models
- Checks: spelling, punctuation, capitalization, grammar rules
- Context-aware corrections (homophones, tense consistency)
- Output: List of issues with position, severity, suggested fix

#### Stage 2: Style Analysis
- Analyzes: sentence length variation, passive voice, adverb density
- Cliché detection (configurable dictionary)
- Jargon overuse detection
- Reading level estimation (Flesch-Kincaid, SMOG, Coleman-Liau)
- Consistency checking for capitalization, date formats, number formatting

#### Stage 3: Readability & Structure
- Paragraph length analysis
- Heading hierarchy validation (for Markdown/HTML)
- List and bullet formatting consistency
- Transition word usage
- Section balance metrics

#### Stage 4: Sentiment & Tone
- Overall sentiment score (-1.0 to 1.0)
- Emotion classification (joy, sadness, anger, fear, surprise, neutral)
- Formality level detection
- Audience appropriateness check

#### Stage 5: Keyword Extraction
- TF-IDF analysis
- Named entity recognition (people, orgs, locations)
- Keyphrase extraction via RAKE or YAKE
- Word cloud data generation

#### Stage 6: Plagiarism Detection (optional)
- Compares against:
  - Local corpus (configurable directory)
  - Web search if API available (requires PLAGIARISM_API_KEY)
  - Internal phrase fingerprinting
- Generates similarity scores and source matches

#### Stage 7: Custom AI Analysis
- User-provided prompt sent to configured LLM (OpenAI, Anthropic, local)
- Configurable temperature and max_tokens
- System prompt from config (`scanner.custom_system_prompt`)
- Streaming or batch response collection

### Aggregation & Reporting

- Weighted scoring system (configurable weights)
- Overall grade (A-F) or score (0-100)
- Category-specific grades
- Top issues prioritized by impact
- Suggestions with estimated improvement value
- Export in multiple formats with styling

## Golden Rules

1. **Never modify source files unless `--fix` flag explicitly provided**
2. **Always preserve original file encoding; detect before processing**
3. **Cache key must include: file hash, scanner config hash, model version**
4. **Respect API rate limits; implement exponential backoff**
5. **For documents >100k tokens, force chunking and warn user**
6. **Never store sent documents in logs; use hashed references**
7. **All AI model calls must have timeout (default: 30s)**
8. **Batch operations recover from failures; continue with next file**
9. **Configuration keys are lowercase with dot notation (e.g., `scanner.default_level`)**
10. **Exit codes: 0=success, 1=analysis-failed, 2=config-error, 3=dependency-missing**

## Examples

### 1. Basic scan of a Markdown file
```bash
super-scanner scan ./docs/article.md --level standard --output markdown
```
**Output:**
```
# Scan Report: article.md

## Overall Grade: B+ (85/100)

### ✅ Grammar & Spelling (A)
- 0 critical, 2 warnings, 5 suggestions
- Issues: inconsistentOxfordComma, passiveVoice (2x)

### ⚠️ Style (B)
- Adverb density: 1.2% (recommended: <0.5%)
- Sentence length variance: low (avg: 18 words, σ: 3)
- 3 sentences in passive voice

### 📖 Readability (A-)
- Flesch Reading Ease: 65 (target: 60-70)
- Grade level: 10th grade
- Average paragraph: 4 sentences

### 🔍 Keywords (Top 10)
1. "API" (24)
2. "integration" (18)
3. "authentication" (15)
...

## Top Recommendations
1. [HIGH] Replace passive voice: "The request was sent..." → "The system sent..."
2. [MEDIUM] Reduce adverb usage: "very quickly" → "rapidly"
3. [LOW] Add transition words between paragraphs 3 and 4
```

### 2. Deep analysis with custom prompt for technical blog
```bash
super-scanner scan ./blog/kubernetes-deploy.md \
  --level deep \
  --checks grammar,style,keywords \
  --custom-prompt "Evaluate Kubernetes terminology accuracy, check if concepts are explained for intermediate audience, flag any outdated API versions" \
  --output json > report.json
```
**Custom Prompt Output (excerpt):**
```json
{
  "custom_analysis": {
    "kubernetes_accuracy": {
      "score": 0.92,
      "notes": [
        "Correct use of 'Deployment' vs 'ReplicaSet'",
        "API version v1 used for Pods (current stable)",
        "Consider mentioning 'kubectl apply -f' instead of 'create' (deprecated)"
      ]
    },
    "audience_fit": {
      "score": 0.78,
      "notes": [
        "Assumes knowledge of containers but not orchestration",
        "Good use of analogies (restaurant metaphor)",
        "Expand acronym 'CRD' on first occurrence"
      ]
    }
  }
}
```

### 3. Batch processing entire docs directory
```bash
super-scanner batch ./docs/ \
  --pattern "**/*.md" \
  --recursive \
  --output-dir ./reports/ \
  --summary-file ./docs-quality-summary.html \
  --parallel 4 \
  --exclude-dirs vendor,node_modules \
  --fail-on warning
```
**Summary Report snippet:**
```
Batch Summary: 47 documents processed (45 success, 2 failures)

### Quality Distribution
- A (90-100): 12 docs (25.5%)
- B (80-89): 22 docs (46.8%)
- C (70-79): 9 docs (19.1%)
- D (60-69): 3 docs (6.4%)
- F (<60): 1 docs (2.1%)

### Common Issues
1. Passive voice: detected in 18 documents
2. Long sentences (>30 words): det
3. Missing alt text: 14 images
4. Orphaned headings: 3 documents

### Failed Documents
1. src/api-spec.md: Encoding error (non-UTF8 chars)
2. tutorials/legacy.md: File too large (250k tokens, exceeds limit)
```

### 4. Compare document against style guide
```bash
super-scanner scan ./proposal.docx \
  --compare ./style-guide.pdf \
  --checks style,structure \
  --output html \
  > comparison.html
```
**Comparison highlights:**
```
### Differences from Style Guide

❌ MISMATCH: Heading capitalization
- Guide: "Sentence case for H2-H6"
- Document: "Title Case Found" (17 instances)

❌ MISMATCH: List punctuation
- Guide: "No period for fragments"
- Document: "Periods present" (8 fragments)

✅ MATCH: Font and spacing standards

⚠️  VARIATION: Date format
- Guide: "YYYY-MM-DD"
- Document: "MM/DD/YYYY" (3 instances)
```

### 5. Grammar fix and format output
```bash
super-scanner scan ./email.txt --fix --output markdown > fixes.md
```
**Original:**
```
I cant beleive there going to cancel the meeting. Their are to many important topics we need to discuss.
```
**Fixed output:**
```
I can't believe they're going to cancel the meeting. There are too many important topics we need to discuss.

## Applied Fixes
- "cant" → "can't" (contraction)
- "beleive" → "believe" (spelling)
- "there" → "they're" (homophone)
- "Their" → "There" (homophone)
- "to" → "too" (homophone)
- Added missing period (end of sentence)
```

### 6. Configure custom style guide
```bash
super-scanner config set scanner.style.max_sentence_length 25
super-scanner config set scanner.style.min_sentence_variance 0.4
super-scanner config set scanner.style.allowed_passive_ratio 0.15
super-scanner config set scanner.custom_system_prompt "You are a senior editor for a technical publication. Focus on clarity for junior engineers."
super-scanner config export ./my-style-config.json
```

### 7. Test model connection
```bash
$ super-scanner models test gpt-4-turbo-preview
Model: gpt-4-turbo-preview
✓ API connection successful
✓ Model accessible
✓ Token limit: 128000
✓ Response time: 1.2s
✓ Cost estimate: $0.01 / 1k tokens
```

### 8. Plagiarism check with local corpus
```bash
super-scanner scan ./paper.txt \
  --checks plagiarism \
  --plagiarism-mode thorough \
  --local-corpus ./reference-papers/ \
  --output json
```
**Result:**
```json
{
  "plagiarism": {
    "overall_similarity": 0.12,
    "flagged_segments": [
      {
        "text": "The quick brown fox jumps over the lazy dog",
        "source": "./reference-papers/common-phrases.txt:45",
        "similarity": 0.96,
        "type": "common_phrase"
      },
      {
        "text": "In this paper we propose a novel approach...",
        "source": "https://arxiv.org/abs/2301.xxxxx",
        "similarity": 0.34,
        "type": "web_match"
      }
    ]
  }
}
```

## Environment Variables

- `OPENCLOUD_API_KEY`: Required for LLM-based analysis (OpenAI or compatible)
- `SCANNER_MODEL`: Default model (gpt-4-turbo-preview, claude-3-opus, etc.)
- `SCANNER_CACHE_DIR`: Override default cache location (~/.openclaw/cache/scanner)
- `MAX_TOKENS`: Maximum tokens for LLM call (default: 4000)
- `TEMPERATURE`: LLM temperature 0.0-2.0 (default: 0.1 for analysis, 0.7 for creative)
- `SCAN_BATCH_SIZE`: Files per batch (default: 10)
- `LANGUAGE_TOOL_PATH`: Custom LanguageTool installation
- `SPACY_MODEL`: spaCy model (en_core_web_sm, en_core_web_lg, etc.)
- `NO_CACHE`: Set to "1" to disable caching globally

## Dependencies & Requirements

### System
- Python 3.9+ with pip
- 4GB RAM minimum (8GB recommended for deep scans)
- 2GB disk space for models and cache
- Internet connection for cloud models (optional for local-only mode)

### Python Packages
Installed automatically via `super-scanner --install-deps` or manually:
```bash
pip install tiktoken transformers torch scikit-learn spacy nltk language-tool-python
python -m spacy download en_core_web_sm
python -m nltk.downloader punkt averaged_perceptron_tagger stopwords
```

### Optional Integrations
- LanguageTool server (local): `java -jar languagetool.jar`
- Local LLM via Ollama: Set `SCANNER_MODEL=ollama/llama2`
- Anthropic Claude: Set `ANTHROPIC_API_KEY`

## Verification

### Verify installation
```bash
super-scanner --version
super-scanner models list | grep -q "gpt-4-turbo"
```

### Test grammar check
```bash
echo "She dont know nothing about it" > /tmp/test.txt
super-scanner scan /tmp/test.txt --checks grammar --output json | grep -q "don't"
```

### Test style analysis
```bash
super-scanner scan ./README.md --level basic --checks style > /dev/null
echo $?  # Should be 0
```

### Verify configuration
```bash
super-scanner config list | grep -E "(default_level|enable_cache)"
```

### Check cache functionality
```bash
super-scanner cache stats
super-scanner scan ./test.md
super-scanner scan ./test.md  # Second run should hit cache
super-scanner cache stats | grep "hits"
```

### Batch mode test
```bash
mkdir -p /tmp/scan_test/{doc1,doc2}
echo "Test document 1" > /tmp/scan_test/doc1.txt
echo "Test document 2" > /tmp/scan_test/doc2.txt
super-scanner batch /tmp/scan_test --output-dir /tmp/reports
ls /tmp/reports/  # Should have two report files
```

## Troubleshooting

### "ModuleNotFoundError: No module named 'xxx'"
**Solution:** Run `super-scanner --install-deps` or manually install missing package with pip.

### "API rate limit exceeded"
**Solution:** Set `SCANNER_CACHE_DIR` and ensure caching enabled. Add exponential backoff: `export SCANNER_RETRY_COUNT=3`.

### "Document exceeds maximum token limit"
**Solution:** Use `--level basic` or split document. For >100k tokens, contact admin to increase `MAX_TOKENS` or use local model.

### "Plagiarism check failed: No local corpus found"
**Solution:** Either install local corpus with `super-scanner corpus download` or use web API with `PLAGIARISM_API_KEY`.

### "LanguageTool server not found"
**Solution:** Install LanguageTool or use cloud: `super-scanner config set scanner.grammar.use_cloud true`.

### "Encoding error: 'utf-8' codec can't decode byte..."
**Solution:** Convert file to UTF-8: `iconv -f ISO-8859-1 -t UTF-8 file.txt > file-utf8.txt` or set encoding manually in config.

### "Cache corruption detected"
**Solution:** Clear cache: `super-scanner cache clear`

### "Model 'xxx' not found"
**Solution:** List available: `super-scanner models list`. Check permissions or API key for cloud models.

### "Batch failed: No files matched pattern"
**Solution:** Verify pattern: `echo ./docs/*.md` or use absolute path. Check `--exclude-dirs` not blocking files.

### "Out of memory"
**Solution:** Reduce `--parallel` workers to 1, or use `--level basic` which uses less memory.

## Rollback Commands

### Undo a scan that modified files (if `--fix` was used)
```bash
# If backup wasn't created, restore from git
git checkout HEAD -- <file>

# If specific backup exists
cp <file>.backup <file>
rm <file>.backup
```

### Clear all scanner cache
```bash
super-scanner cache clear
# Verify
super-scanner cache stats  # Should show 0 entries
```

### Revert configuration changes
```bash
# Single key
super-scanner config reset scanner.style.max_sentence_length

# All to defaults
super-scanner config reset
# Or import backup
super-scanner config import ~/.openclaw/backups/scanner-config-20240101.json
```

### Remove batch report outputs
```bash
rm -rf ./reports/
rm ./docs-quality-summary.html
# Verify removed
ls ./reports/ 2>/dev/null || echo "Cleaned"
```

### Uninstall dependencies
```bash
pip uninstall -y tiktoken transformers torch scikit-learn spacy nltk language-tool-python
# Remove cached models
rm -rf ~/.cache/torch ~/.cache/huggingface ~/.local/share/spacy
```

### Disable scanner integration
```bash
# If added to PATH or shell profile
sed -i '/super-scanner/d' ~/.bashrc ~/.zshrc 2>/dev/null
source ~/.bashrc  # or ~/.zshrc
# Verify not in PATH
which super-scanner || echo "Removed from PATH"
```

### Restore from backup before batch operation
```bash
# If you created backup before batch:
rsync -av --delete ~/backup/docs/ ./docs/
# Verify count matches
find ./docs -type f | wc -l
```

## Advanced Usage

### Custom rule definitions (config file)
```json
{
  "scanner": {
    "rules": {
      "passive_voice": {
        "enabled": true,
        "threshold": 0.2,
        "patterns": ["was ...ed", "were ...ed", "is ...ed"]
      },
      "adverb_density": {
        "enabled": true,
        "max_ratio": 0.01
      }
    }
  }
}
```
Load with: `super-scanner config import custom-rules.json`

### Pipeline with external tools
```bash
# Generate report, then send to Slack
super-scanner scan ./draft.md --output json | \
  jq '{text, grade: .overall.grade}' | \
  curl -X POST -H "Content-type: application/json" \
    -d @- https://hooks.slack.com/services/...
```

### CI/CD Integration
```yaml
# .github/workflows/scan.yml
- name: Scan documentation
  run: |
    super-scanner batch ./docs/ --fail-on error
    # Upload artifact on failure
    if [ $? -ne 0 ]; then
      super-scanner batch ./docs/ --output-dir ./ci-reports/
      tar -czf scan-failure-reports.tar.gz ./ci-reports/
    fi
```

### Parallel processing optimization
```bash
# Auto-detect CPU cores and use 75%
CORES=$(python3 -c "import os; print(int(os.cpu_count()*0.75))")
super-scanner batch ./large-docs/ --parallel $CORES --batch-size 5
```

```