name: "super-scanner"
version: "2.1.0"
maintainer: "OpenClaw Team <dev@openclaw.ai>"
description: "Escáner y analizador de documentos impulsado por IA para tareas de escritura"
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
```

# Super Scanner

Escáner y analizador de documentos impulsado por IA diseñado específicamente para tareas de escritura. Procesa documentos de texto a través de múltiples modelos de IA para proporcionar un análisis completo que incluye corrección gramatical, evaluación de estilo, métricas de legibilidad, detección de plagio y escaneos personalizados con IA.

## Propósito

Super Scanner aborda flujos de trabajo de escritura del mundo real:
- Validación de documentos previa a publicación para blogs, documentos técnicos y documentación
- Verificación de calidad de artículos académicos antes de enviar a revistas
- Evaluación de calidad de documentación de código
- Optimización de correos electrónicos y copy de marketing
- Análisis de claridad y consistencia en documentos legales
- Mejora de legibilidad en escritura técnica
- Procesamiento por lotes de múltiples documentos en un repositorio
- Verificación de cumplimiento contra guías de estilo personalizadas

## Alcance

### Comandos Principales

```
super-scanner scan <file> [options]
super-scanner batch <directory> [options]
super-scanner config [subcommand]
super-scanner models [list|info|test]
super-scanner cache [clear|stats]
```

### Detalles de Comandos

**`super-scanner scan <file>`**
- `--level <basic|standard|deep>`: Profundidad de análisis (predeterminado: standard)
- `--fix`: Aplicar correcciones gramaticales automáticamente
- `--output <format>`: json, yaml, html, markdown, terminal
- `--checks <comma-separated>`: Análisis específicos a ejecutar (grammar,style,readability,plagiarism,sentiment,keywords,structure)
- `--custom-prompt <string>`: Prompt de análisis con IA personalizado
- `--threshold <0.0-1.0>`: Umbral de confianza para advertencias
- `--exclude <patterns>`: Patrones a excluir del análisis
- `--word-count`: Incluir análisis detallado de frecuencia de palabras
- `--compare <reference-file>`: Comparar contra documento de referencia
- `--no-cache`: Omitir caché para análisis fresco

**`super-scanner batch <directory>`**
- `--pattern <glob>`: Patrón de archivos (predeterminado: `**/*.{md,txt,tex,rst,html}`)
- `--recursive / --no-recursive`: Recorrido de directorios
- `--output-dir <path>`: Guardar reportes individuales
- `--summary-file <path>`: Generar reporte resumen de lote
- `--fail-on <severity>`: Umbral para fallo de lote (warning,error,critical)
- `--parallel <n>`: Workers de procesamiento paralelo
- `--exclude-dirs <comma-separated>`: Directorios a omitir
- `--config <path>`: Usar archivo de configuración para ajustes de lote

**`super-scanner config`**
- `config set <key> <value>`: Establecer configuración
- `config get <key>`: Obtener valor de configuración
- `config list`: Mostrar todas las configuraciones
- `config import <file>`: Importar configuración desde JSON
- `config export <file>`: Exportar configuración a JSON
- `config reset`: Restablecer a valores predeterminados

**`super-scanner models`**
- `models list`: Mostrar modelos de IA disponibles
- `models info <model>`: Información detallada del modelo
- `models test <model>`: Probar capacidad de respuesta del modelo
- `models set <model>`: Establecer modelo predeterminado

**`super-scanner cache`**
- `cache clear`: Limpiar todos los resultados en caché
- `cache stats`: Mostrar estadísticas de caché
- `cache prune <days>`: Eliminar entradas más antiguas que N días

## Proceso de Trabajo

### Intake de Documento

1. Validación de archivo y detección de codificación (UTF-8, UTF-16, ISO-8859-1)
2. Eliminación de formato si es necesario (Markdown, HTML, LaTeX, reStructuredText)
3. Extracción de texto con preservación estructural
4. Conteo inicial de tokens para estimación de tamaño de entrada del modelo
5. Fragmentación para documentos grandes (>4000 tokens)

### Pipeline de Análisis

#### Etapa 1: Gramática y Ortografía
- Usa LanguageTool + modelos de ML personalizados
- Verifica: ortografía, puntuación, mayúsculas/minúsculas, reglas gramaticales
- Correcciones conscientes del contexto (homófonos, consistencia de tiempos)
- Salida: Lista de problemas con posición, severidad, sugerencia de corrección

#### Etapa 2: Análisis de Estilo
- Analiza: variación de longitud de oraciones, voz pasiva, densidad de adverbios
- Detección de clichés (diccionario configurable)
- Detección de sobreec hackería
- Estimación de nivel de lectura (Flesch-Kincaid, SMOG, Coleman-Liau)
- Verificación de consistencia en mayúsculas, formatos de fecha, formato de números

#### Etapa 3: Legibilidad y Estructura
- Análisis de longitud de párrafos
- Validación de jerarquía de encabezados (para Markdown/HTML)
- Consistencia en formato de listas y viñetas
- Uso de palabras de transición
- Métricas de balance de secciones

#### Etapa 4: Sentimiento y Tono
- Puntuación general de sentimiento (-1.0 a 1.0)
- Clasificación de emociones (alegría, tristeza, ira, miedo, sorpresa, neutral)
- Detección de nivel de formalidad
- Verificación de audiencia objetivo

#### Etapa 5: Extracción de Palabras Clave
- Análisis TF-IDF
- Reconocimiento de entidades con nombre (personas, organizaciones, ubicaciones)
- Extracción de frases clave via RAKE o YAKE
- Generación de datos para nubes de palabras

#### Etapa 6: Detección de Plagio (opcional)
- Compara contra:
  - Corpus local (directorio configurable)
  - Búsqueda web si API disponible (requiere PLAGIARISM_API_KEY)
  - Huellas digitales de frases internas
- Genera puntuaciones de similitud y coincidencias de fuente

#### Etapa 7: Análisis Personalizado con IA
- Prompt del usuario enviado a LLM configurado (OpenAI, Anthropic, local)
- Temperatura y max_tokens configurables
- Prompt del sistema desde configuración (`scanner.custom_system_prompt`)
- Recopilación de respuesta en streaming o por lotes

### Agregación y Reportes

- Sistema de puntuación ponderada (pesos configurables)
- Grado general (A-F) o puntuación (0-100)
- Grados específicos por categoría
- Problemas principales priorizados por impacto
- Sugerencias con valor de mejora estimado
- Exportación en múltiples formatos con estilos

## Reglas de Oro

1. **Nunca modificar archivos fuente a menos que se proporcione flag `--fix` explícitamente**
2. **Siempre preservar codificación original del archivo; detectar antes de procesar**
3. **Clave de caché debe incluir: hash de archivo, hash de configuración del scanner, versión del modelo**
4. **Respetar límites de tasa de API; implementar retroceso exponencial**
5. **Para documentos >100k tokens, forzar fragmentación y advertir al usuario**
6. **Nunca almacenar documentos enviados en logs; usar referencias con hash**
7. **Todas las llamadas a modelos de IA deben tener timeout (predeterminado: 30s)**
8. **Operaciones por lotes se recuperan de fallos; continuar con siguiente archivo**
9. **Claves de configuración son minúsculas con notación de punto (ej., `scanner.default_level`)**
10. **Códigos de salida: 0=éxito, 1=análisis-fallido, 2=error-config, 3=dependencia-faltante**

## Ejemplos

### 1. Escaneo básico de archivo Markdown
```bash
super-scanner scan ./docs/article.md --level standard --output markdown
```
**Salida:**
```
# Reporte de Escaneo: article.md

## Grado General: B+ (85/100)

### ✅ Gramática y Ortografía (A)
- 0 críticos, 2 advertencias, 5 sugerencias
- Problemas: inconsistentOxfordComma, vozPasiva (2x)

### ⚠️ Estilo (B)
- Densidad de adverbios: 1.2% (recomendado: <0.5%)
- Varianza de longitud de oraciones: baja (prom: 18 palabras, σ: 3)
- 3 oraciones en voz pasiva

### 📖 Legibilidad (A-)
- Facilidad de Lectura Flesch: 65 (objetivo: 60-70)
- Nivel de grado: 10º grado
- Párrafo promedio: 4 oraciones

### 🔍 Palabras Clave (Top 10)
1. "API" (24)
2. "integration" (18)
3. "authentication" (15)
...

## Recomendaciones Principales
1. [ALTA] Reemplazar voz pasiva: "The request was sent..." → "The system sent..."
2. [MEDIA] Reducir uso de adverbios: "very quickly" → "rapidly"
3. [BAJA] Agregar palabras de transición entre párrafos 3 y 4
```

### 2. Análisis profundo con prompt personalizado para blog técnico
```bash
super-scanner scan ./blog/kubernetes-deploy.md \
  --level deep \
  --checks grammar,style,keywords \
  --custom-prompt "Evaluate Kubernetes terminology accuracy, check if concepts are explained for intermediate audience, flag any outdated API versions" \
  --output json > report.json
```
**Salida de Prompt Personalizado (extracto):**
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

### 3. Procesamiento por lotes de directorio docs completo
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
**Fragmento de Reporte Resumen:**
```
Resumen de Lote: 47 documentos procesados (45 éxitos, 2 fallos)

### Distribución de Calidad
- A (90-100): 12 docs (25.5%)
- B (80-89): 22 docs (46.8%)
- C (70-79): 9 docs (19.1%)
- D (60-69): 3 docs (6.4%)
- F (<60): 1 docs (2.1%)

### Problemas Comunes
1. Voz pasiva: detectada en 18 documentos
2. Oraciones largas (>30 palabras): det
3. Texto alternativo faltante: 14 imágenes
4. Encabezados huérfanos: 3 documentos

### Documentos Fallidos
1. src/api-spec.md: Error de codificación (caracteres no-UTF8)
2. tutorials/legacy.md: Archivo muy grande (250k tokens, excede límite)
```

### 4. Comparar documento contra guía de estilo
```bash
super-scanner scan ./proposal.docx \
  --compare ./style-guide.pdf \
  --checks style,structure \
  --output html \
  > comparison.html
```
**Aspectos destacados de comparación:**
```
### Diferencias respecto a Guía de Estilo

❌ DESCOINCIDENCIA: Mayúsculas en encabezados
- Guía: "Sentence case for H2-H6"
- Documento: "Title Case Found" (17 instancias)

❌ DESCOINCIDENCIA: Puntuación en listas
- Guía: "No punto para fragmentos"
- Documento: "Puntos presentes" (8 fragmentos)

✅ COINCIDENCIA: Estándares de fuente y espaciado

⚠️  VARIACIÓN: Formato de fecha
- Guía: "YYYY-MM-DD"
- Documento: "MM/DD/YYYY" (3 instancias)
```

### 5. Corrección gramatical y formato de salida
```bash
super-scanner scan ./email.txt --fix --output markdown > fixes.md
```
**Original:**
```
I cant beleive there going to cancel the meeting. Their are to many important topics we need to discuss.
```
**Salida corregida:**
```
I can't believe they're going to cancel the meeting. There are too many important topics we need to discuss.

## Correcciones Aplicadas
- "cant" → "can't" (contracción)
- "beleive" → "believe" (ortografía)
- "there" → "they're" (homófono)
- "Their" → "There" (homófono)
- "to" → "too" (homófono)
- Punto faltante agregado (final de oración)
```

### 6. Configurar guía de estilo personalizada
```bash
super-scanner config set scanner.style.max_sentence_length 25
super-scanner config set scanner.style.min_sentence_variance 0.4
super-scanner config set scanner.style.allowed_passive_ratio 0.15
super-scanner config set scanner.custom_system_prompt "You are a senior editor for a technical publication. Focus on clarity for junior engineers."
super-scanner config export ./my-style-config.json
```

### 7. Probar conexión del modelo
```bash
$ super-scanner models test gpt-4-turbo-preview
Model: gpt-4-turbo-preview
✓ Conexión API exitosa
✓ Modelo accesible
✓ Límite de tokens: 128000
✓ Tiempo de respuesta: 1.2s
✓ Estimación de costo: $0.01 / 1k tokens
```

### 8. Verificación de plagio con corpus local
```bash
super-scanner scan ./paper.txt \
  --checks plagiarism \
  --plagiarism-mode thorough \
  --local-corpus ./reference-papers/ \
  --output json
```
**Resultado:**
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

## Variables de Entorno

- `OPENCLOUD_API_KEY`: Requerida para análisis basado en LLM (OpenAI o compatible)
- `SCANNER_MODEL`: Modelo predeterminado (gpt-4-turbo-preview, claude-3-opus, etc.)
- `SCANNER_CACHE_DIR`: Anular ubicación de caché predeterminada (~/.openclaw/cache/scanner)
- `MAX_TOKENS`: Tokens máximos para llamada LLM (predeterminado: 4000)
- `TEMPERATURE`: Temperatura LLM 0.0-2.0 (predeterminado: 0.1 para análisis, 0.7 para creativo)
- `SCAN_BATCH_SIZE`: Archivos por lote (predeterminado: 10)
- `LANGUAGE_TOOL_PATH`: Instalación personalizada de LanguageTool
- `SPACY_MODEL`: Modelo spaCy (en_core_web_sm, en_core_web_lg, etc.)
- `NO_CACHE`: Establecer a "1" para deshabilitar caché globalmente

## Requisitos

### Sistema
- Python 3.9+ con pip
- 4GB RAM mínimo (8GB recomendado para escaneos profundos)
- 2GB espacio en disco para modelos y caché
- Conexión a internet para modelos en la nube (opcional para modo solo local)

### Paquetes Python
Instalados automáticamente vía `super-scanner --install-deps` o manualmente:
```bash
pip install tiktoken transformers torch scikit-learn spacy nltk language-tool-python
python -m spacy download en_core_web_sm
python -m nltk.downloader punkt averaged_perceptron_tagger stopwords
```

### Integraciones Opcionales
- Servidor LanguageTool (local): `java -jar languagetool.jar`
- LLM local via Ollama: Establecer `SCANNER_MODEL=ollama/llama2`
- Anthropic Claude: Establecer `ANTHROPIC_API_KEY`

## Verificación

### Verificar instalación
```bash
super-scanner --version
super-scanner models list | grep -q "gpt-4-turbo"
```

### Probar verificación gramatical
```bash
echo "She dont know nothing about it" > /tmp/test.txt
super-scanner scan /tmp/test.txt --checks grammar --output json | grep -q "don't"
```

### Probar análisis de estilo
```bash
super-scanner scan ./README.md --level basic --checks style > /dev/null
echo $?  # Debería ser 0
```

### Verificar configuración
```bash
super-scanner config list | grep -E "(default_level|enable_cache)"
```

### Verificar funcionalidad de caché
```bash
super-scanner cache stats
super-scanner scan ./test.md
super-scanner scan ./test.md  # Segunda ejecución debería impactar caché
super-scanner cache stats | grep "hits"
```

### Prueba de modo por lotes
```bash
mkdir -p /tmp/scan_test/{doc1,doc2}
echo "Test document 1" > /tmp/scan_test/doc1.txt
echo "Test document 2" > /tmp/scan_test/doc2.txt
super-scanner batch /tmp/scan_test --output-dir /tmp/reports
ls /tmp/reports/  # Debería tener dos archivos de reporte
```

## Solución de Problemas

### "ModuleNotFoundError: No module named 'xxx'"
**Solución:** Ejecutar `super-scanner --install-deps` o instalar manualmente el paquete faltante con pip.

### "API rate limit exceeded"
**Solución:** Establecer `SCANNER_CACHE_DIR` y asegurar que el caché esté habilitado. Agregar retroceso exponencial: `export SCANNER_RETRY_COUNT=3`.

### "Document exceeds maximum token limit"
**Solución:** Usar `--level basic` o dividir documento. Para >100k tokens, contactar admin para aumentar `MAX_TOKENS` o usar modelo local.

### "Plagiarism check failed: No local corpus found"
**Solución:** Ya sea instalar corpus local con `super-scanner corpus download` o usar API web con `PLAGIARISM_API_KEY`.

### "LanguageTool server not found"
**Solución:** Instalar LanguageTool o usar la nube: `super-scanner config set scanner.grammar.use_cloud true`.

### "Encoding error: 'utf-8' codec can't decode byte..."
**Solución:** Convertir archivo a UTF-8: `iconv -f ISO-8859-1 -t UTF-8 file.txt > file-utf8.txt` o establecer codificación manualmente en config.

### "Cache corruption detected"
**Solución:** Limpiar caché: `super-scanner cache clear`

### "Model 'xxx' not found"
**Solución:** Listar disponibles: `super-scanner models list`. Verificar permisos o API key para modelos en la nube.

### "Batch failed: No files matched pattern"
**Solución:** Verificar patrón: `echo ./docs/*.md` o usar ruta absoluta. Verificar que `--exclude-dirs` no esté bloqueando archivos.

### "Out of memory"
**Solución:** Reducir workers `--parallel` a 1, o usar `--level basic` que usa menos memoria.

## Comandos de Rollback

### Deshacer un escaneo que modificó archivos (si se usó `--fix`)
```bash
# Si no se creó backup, restaurar desde git
git checkout HEAD -- <file>

# Si existe backup específico
cp <file>.backup <file>
rm <file>.backup
```

### Limpiar toda la caché del scanner
```bash
super-scanner cache clear
# Verificar
super-scanner cache stats  # Debería mostrar 0 entradas
```

### Revertir cambios de configuración
```bash
# Clave única
super-scanner config reset scanner.style.max_sentence_length

# Todo a valores predeterminados
super-scanner config reset
# O importar backup
super-scanner config import ~/.openclaw/backups/scanner-config-20240101.json
```

### Eliminar salidas de reportes de lote
```bash
rm -rf ./reports/
rm ./docs-quality-summary.html
# Verificar eliminados
ls ./reports/ 2>/dev/null || echo "Limpiado"
```

### Desinstalar dependencias
```bash
pip uninstall -y tiktoken transformers torch scikit-learn spacy nltk language-tool-python
# Eliminar modelos en caché
rm -rf ~/.cache/torch ~/.cache/huggingface ~/.local/share/spacy
```

### Deshabilitar integración del scanner
```bash
# Si se agregó a PATH o perfil de shell
sed -i '/super-scanner/d' ~/.bashrc ~/.zshrc 2>/dev/null
source ~/.bashrc  # o ~/.zshrc
# Verificar que no esté en PATH
which super-scanner || echo "Removido del PATH"
```

### Restaurar desde backup antes de operación por lotes
```bash
# Si creaste backup antes del lote:
rsync -av --delete ~/backup/docs/ ./docs/
# Verificar que el conteo coincida
find ./docs -type f | wc -l
```

## Uso Avanzado

### Definiciones de reglas personalizadas (archivo de configuración)
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
Cargar con: `super-scanner config import custom-rules.json`

### Pipeline con herramientas externas
```bash
# Generar reporte, luego enviar a Slack
super-scanner scan ./draft.md --output json | \
  jq '{text, grade: .overall.grade}' | \
  curl -X POST -H "Content-type: application/json" \
    -d @- https://hooks.slack.com/services/...
```

### Integración CI/CD
```yaml
# .github/workflows/scan.yml
- name: Scan documentation
  run: |
    super-scanner batch ./docs/ --fail-on error
    # Subir artefacto en caso de fallo
    if [ $? -ne 0 ]; then
      super-scanner batch ./docs/ --output-dir ./ci-reports/
      tar -czf scan-failure-reports.tar.gz ./ci-reports/
    fi
```

### Optimización de procesamiento paralelo
```bash
# Detectar automáticamente núcleos de CPU y usar 75%
CORES=$(python3 -c "import os; print(int(os.cpu_count()*0.75))")
super-scanner batch ./large-docs/ --parallel $CORES --batch-size 5
```
```