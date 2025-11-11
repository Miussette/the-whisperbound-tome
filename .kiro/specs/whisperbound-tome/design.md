# Design Document: The Whisperbound Tome

## Overview

The Whisperbound Tome is a ceremonial interface for generating deterministic, atmospheric spells from user whispers. The system transforms text input through a multi-stage generative pipeline that produces structured mystical responses. Every component prioritizes restraint, silence, and intimacy over drama or playfulness.

The architecture separates concerns into three layers:
- **Presentation Layer**: Dark, minimal UI with slow, deliberate animations
- **Generation Layer**: Deterministic pipeline for spell creation
- **Data Layer**: Structured spell components and curated lexicons

## Architecture

### System Flow

```
User Whisper Input
    ↓
Input Validation & Sanitization
    ↓
Intent Classification
    ↓
Deterministic Seed Generation
    ↓
Parallel Generation:
    ├─ Sigil Creation
    ├─ Ritual Sequence Selection
    └─ Incantation Composition
    ↓
Spell Assembly
    ↓
Sequential Reveal (Sigil → Ritual → Incantation)
```

### Core Principles

1. **Determinism**: Identical inputs produce identical outputs through seeded randomization
2. **Restraint**: All generated content avoids excess, drama, and modern language
3. **Ceremony**: Pacing and presentation emphasize ritual over efficiency
4. **Silence**: Whitespace, pauses, and minimal text create contemplative space

## Components and Interfaces

### 1. Input Handler

**Responsibility**: Receive, validate, and sanitize user whispers

**Interface**:
```typescript
interface InputHandler {
  validateWhisper(input: string): ValidationResult
  sanitizeWhisper(input: string): string
  extractIntent(whisper: string): string
}

type ValidationResult = {
  isValid: boolean
  sanitized: string
  errorMessage?: string
}
```

**Behavior**:
- Accept 1-500 character strings
- Remove dangerous characters while preserving mystical punctuation (', -, …)
- Reject empty or whitespace-only input with atmospheric feedback
- Normalize whitespace and convert to lowercase for processing

### 2. Intent Classifier

**Responsibility**: Map user whispers to mystical intent categories

**Interface**:
```typescript
interface IntentClassifier {
  classify(whisper: string): IntentCategory
  getIntentSignature(category: IntentCategory): string
}

enum IntentCategory {
  PROTECTION = "protection",
  REVELATION = "revelation",
  BINDING = "binding",
  TRANSFORMATION = "transformation",
  SUMMONING = "summoning",
  BANISHMENT = "banishment",
  PRESERVATION = "preservation",
  PASSAGE = "passage"
}
```

**Behavior**:
- Use keyword matching against curated intent lexicons
- Weight keywords by mystical significance
- Default to REVELATION for ambiguous whispers
- Generate intent signature for deterministic seeding

### 3. Seed Generator

**Responsibility**: Create deterministic seeds from whisper and intent

**Interface**:
```typescript
interface SeedGenerator {
  generateSeed(whisper: string, intent: IntentCategory): number
  createSubSeed(baseSeed: number, component: string): number
}
```

**Behavior**:
- Hash whisper text using consistent algorithm (e.g., FNV-1a)
- Combine with intent category hash
- Generate component-specific sub-seeds for sigil, ritual, incantation
- Ensure same whisper always produces same seed

### 4. Sigil Generator

**Responsibility**: Create symbolic ASCII/Unicode patterns

**Interface**:
```typescript
interface SigilGenerator {
  generate(seed: number, intent: IntentCategory): Sigil
}

type Sigil = {
  lines: string[]
  symbolSet: string[]
  pattern: string
}
```

**Behavior**:
- Select 3-7 symbols from intent-specific symbol sets
- Arrange in geometric patterns (circle, triangle, line, cross)
- Use characters: ◯ ◆ ▽ △ ═ ║ ╱ ╲ ∴ ⋮ · — ∞ ⊙
- Center-align all lines
- Maintain visual balance and symmetry

**Symbol Sets by Intent**:
- Protection: ◯ ═ ║ ⊙ (circles, barriers)
- Revelation: △ ∴ · (eyes, points, illumination)
- Binding: ╱ ╲ ∞ (knots, infinity)
- Transformation: ◆ ▽ △ (flux, change)
- Summoning: ⋮ ∴ ◯ (calling, gathering)
- Banishment: — ║ ═ (severance, walls)
- Preservation: ⊙ ◯ ═ (containment, stasis)
- Passage: · — ╱ (thresholds, paths)

### 5. Ritual Generator

**Responsibility**: Select and order ceremonial steps

**Interface**:
```typescript
interface RitualGenerator {
  generate(seed: number, intent: IntentCategory): Ritual
}

type Ritual = {
  steps: RitualStep[]
  duration: string
}

type RitualStep = {
  action: string
  material?: string
  timing?: string
}
```

**Behavior**:
- Select 3-5 steps from intent-specific corpus (30-50 steps per intent)
- Use seed to deterministically choose and order steps
- Ensure thematic coherence across selected steps
- Format as numbered list with minimal punctuation

**Ritual Corpus Structure**:
Each intent category maintains a corpus of ritual steps involving:
- Natural materials (ash, salt, water, bone, stone, root, leaf)
- Elemental actions (burn, bury, submerge, scatter, bind)
- Temporal markers (dusk, dawn, moonless night, threshold hour)
- Spatial directions (north, beneath, within circle, at crossroads)

### 6. Incantation Generator

**Responsibility**: Compose poetic spell text

**Interface**:
```typescript
interface IncantationGenerator {
  generate(seed: number, intent: IntentCategory): Incantation
}

type Incantation = {
  lines: string[]
  syllableCount: number[]
  metaphor: string
}
```

**Behavior**:
- Generate 2-4 lines with 8-12 syllables each
- Use template-based composition with variable slots
- Select words from curated lexicon (500-800 words)
- Enforce passive voice and indirect phrasing
- Incorporate silence markers (…, —, line breaks)
- Limit adjectives to one per noun phrase

**Template Structure**:
```
[temporal_marker] [verb_passive] [noun_mystical] [preposition] [noun_natural]
[verb_passive] the [noun_abstract] [preposition] [noun_elemental] and [noun_elemental]
[noun_mystical] [verb_passive] … [noun_abstract] [verb_passive]
```

**Lexicon Categories**:
- Temporal: dusk, threshold, silence, breath, moment
- Verbs (passive): whispered, bound, woven, scattered, drawn
- Nouns (mystical): veil, thread, shadow, echo, mark
- Nouns (natural): ash, root, stone, water, bone
- Nouns (abstract): passage, memory, name, boundary, oath
- Nouns (elemental): flame, earth, tide, wind, void
- Prepositions: beneath, within, through, beyond, between

### 7. Spell Assembler

**Responsibility**: Combine components into final spell structure

**Interface**:
```typescript
interface SpellAssembler {
  assemble(sigil: Sigil, ritual: Ritual, incantation: Incantation): Spell
}

type Spell = {
  sigil: Sigil
  ritual: Ritual
  incantation: Incantation
  metadata: SpellMetadata
}

type SpellMetadata = {
  intent: IntentCategory
  generatedAt: number
  whisperHash: string
}
```

**Behavior**:
- Validate all components are present
- Attach metadata for consistency verification
- Format for sequential reveal
- Return immutable spell object

### 8. Presentation Controller

**Responsibility**: Orchestrate UI reveal sequence

**Interface**:
```typescript
interface PresentationController {
  revealSpell(spell: Spell): Promise<void>
  getRevealTiming(): RevealTiming
}

type RevealTiming = {
  transitionDelay: number // 2000-4000ms
  sigilReveal: number // 1000ms
  ritualReveal: number // 1500ms
  incantationReveal: number // 2000ms
  characterDelay: number // 40-60ms
}
```

**Behavior**:
- Display transition state (2-4 seconds)
- Reveal sigil with fade-in (1 second)
- Reveal ritual steps sequentially (1.5 seconds total)
- Reveal incantation character-by-character (40-60ms per character)
- Prevent new input during reveal
- Maintain dark theme throughout

## Data Models

### Complete Spell Structure

```typescript
type Spell = {
  sigil: {
    lines: string[]        // 3-7 lines of symbols
    symbolSet: string[]    // Symbols used
    pattern: string        // Pattern type: "circle" | "triangle" | "line" | "cross"
  }
  ritual: {
    steps: Array<{
      action: string       // e.g., "place ash in circle"
      material?: string    // e.g., "ash", "salt", "bone"
      timing?: string      // e.g., "at dusk", "beneath moon"
    }>
    duration: string       // e.g., "until dawn", "three breaths"
  }
  incantation: {
    lines: string[]        // 2-4 lines
    syllableCount: number[] // 8-12 per line
    metaphor: string       // Primary metaphor used
  }
  metadata: {
    intent: IntentCategory
    generatedAt: number
    whisperHash: string
  }
}
```

### Lexicon Data Structure

```typescript
type Lexicon = {
  temporal: string[]
  verbs: {
    passive: string[]
    active: string[]
  }
  nouns: {
    mystical: string[]
    natural: string[]
    abstract: string[]
    elemental: string[]
  }
  prepositions: string[]
  adjectives: {
    restrained: string[]  // Only subtle, atmospheric adjectives
  }
}
```

### Intent Corpus Structure

```typescript
type IntentCorpus = {
  [key in IntentCategory]: {
    keywords: string[]
    symbolSet: string[]
    ritualSteps: string[]
    metaphors: string[]
    templates: string[]
  }
}
```

## Error Handling

### Input Validation Errors

**Empty Input**:
- Display: "the tome awaits your whisper…"
- No spell generation attempted
- Maintain dark, quiet UI state

**Excessive Length**:
- Silently truncate to 500 characters
- Process truncated input
- No error message shown

**Invalid Characters**:
- Display: "speak clearly… the tome cannot hear"
- Return to input state
- Maintain atmospheric tone

### Generation Errors

**Classification Failure**:
- Default to REVELATION intent
- Log error silently
- Continue generation

**Component Generation Failure**:
- Use fallback templates
- Ensure spell structure remains valid
- Never expose technical errors to user

### Presentation Errors

**Animation Failure**:
- Fall back to instant reveal
- Maintain spell content integrity
- Log error for debugging

## Testing Strategy

### Determinism Testing

**Objective**: Verify identical inputs produce identical outputs

**Approach**:
- Test suite with 50 diverse whisper samples
- Generate each whisper 10 times
- Assert all outputs are byte-identical
- Test across different sessions and restarts

### Tone Consistency Testing

**Objective**: Ensure all generated content maintains atmospheric restraint

**Approach**:
- Automated lexicon validation (no modern words, no exclamations)
- Syllable counting verification (8-12 per line)
- Adjective density checking (max 1 per noun phrase)
- Manual review of 100 generated spells for tone

### Intent Classification Testing

**Objective**: Validate whispers map to appropriate intents

**Approach**:
- Curated test set with expected intent labels
- Measure classification accuracy
- Review edge cases and ambiguous whispers
- Ensure fallback to REVELATION works correctly

### UI Timing Testing

**Objective**: Verify ceremonial pacing is maintained

**Approach**:
- Measure animation durations
- Verify character reveal timing
- Test transition delays
- Ensure no input accepted during reveal

### Integration Testing

**Objective**: Validate complete whisper-to-spell flow

**Approach**:
- End-to-end tests for each intent category
- Verify spell structure completeness
- Test error handling paths
- Validate metadata accuracy

## Constraints and Rules

### Absolute Constraints

These rules must NEVER be violated:

1. **No Modern Language**: Exclude contemporary slang, technical jargon, casual speech
2. **No Theatrical Tone**: Avoid exclamation marks, all-caps, emphatic punctuation
3. **No Playfulness**: Exclude humor, irony, whimsy, lightheartedness
4. **Deterministic Output**: Same whisper must always produce same spell
5. **Structural Consistency**: Every spell must have sigil, ritual, incantation
6. **Syllable Bounds**: Incantation lines must be 8-12 syllables
7. **Ritual Length**: 3-5 steps only
8. **Sigil Length**: 3-7 lines only
9. **Dark Theme**: Background luminance below 15%, foreground below 70%
10. **Slow Pacing**: All animations 0.5-1.5 seconds minimum

### Tone Guidelines

**Preferred**:
- Archaic verb forms (whispered, bound, woven)
- Passive voice (is drawn, was spoken, be scattered)
- Natural imagery (ash, root, stone, water)
- Silence markers (…, —, line breaks)
- Indirect phrasing (the veil parts, shadows gather)
- Restraint in description (one adjective maximum)

**Forbidden**:
- Exclamation marks
- Question marks in incantations
- Contractions (don't, can't, won't)
- Modern words (technology, internet, phone)
- Casual language (stuff, things, whatever)
- Emphatic repetition (very, really, so)
- Direct commands (do this!, go there!)

### Visual Constraints

**Typography**:
- Letter-spacing: 0.05-0.15em
- Line-height: 1.6-2.0
- Font: Serif or monospace only
- Size: 14-18px for body, 24-32px for sigils

**Color Palette**:
- Background: #0a0a0a to #1a1a1a
- Foreground: #8a8a8a to #b0b0b0
- Accent: #6a6a6a to #7a7a7a
- No bright colors, no saturation above 10%

**Animation**:
- Easing: ease-in-out or cubic-bezier
- Duration: 500-1500ms
- Delay: 300-500ms for hover states
- Character reveal: 40-60ms per character

### Lexicon Constraints

**Word Selection**:
- Curated list of 500-800 approved words
- No words coined after 1900
- Prefer Old English, Latin, or nature-based terms
- Maximum 3 syllables per word (exceptions for mystical terms)

**Metaphor Constraints**:
- Draw from nature, elements, time, space
- Avoid body parts except hands, eyes, breath
- Avoid violence or aggression
- Prefer transformation over destruction

## Example Spell

```
      ∴
    ◯   ◯
      ⊙
    ◯   ◯
      ∴

1. place salt in circle at dusk
2. burn root until ash remains
3. speak the name into ash
4. bury ash beneath stone
5. wait three breaths in silence

the veil is drawn through ash and stone…
what was bound in silence — now is known
beneath the threshold, shadows part
the hidden name is whispered to the dark
```
