# Program Studio v2 - Database Specification

## Overview

Two data domains:

1. **Client Knowledge** - Permanent knowledge about clients
2. **Training Content** - Deliverable content (programs → chapters → sessions → content)

```
CLIENTS
├── client_knowledge (permanent: business_context, terminology, brand_voice)
└── programs
    ├── program_knowledge (per-program: brief, topic_research, path_recs, matrix)
    └── chapters
        └── sessions
            ├── articles (1:1)
            ├── videos (1:1)
            ├── quizzes (1:1)
            └── exercises (1:1)
```

## Tables

### clients
```sql
CREATE TABLE clients (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            TEXT NOT NULL,
    slug            TEXT UNIQUE NOT NULL,
    industry        TEXT,
    contact_email   TEXT,
    notes           TEXT,
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

### client_knowledge
```sql
CREATE TABLE client_knowledge (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id       UUID NOT NULL REFERENCES clients(id) ON DELETE CASCADE,
    type            TEXT NOT NULL,  -- "business_context" | "terminology" | "brand_voice"
    content         JSONB NOT NULL,
    version         INTEGER DEFAULT 1,
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    
    UNIQUE(client_id, type)
);
```

### programs
```sql
CREATE TABLE programs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id       UUID NOT NULL REFERENCES clients(id) ON DELETE CASCADE,
    name            TEXT NOT NULL,
    slug            TEXT NOT NULL,
    description     TEXT,
    language        TEXT DEFAULT 'en',
    status          TEXT DEFAULT 'draft',  -- draft | in_progress | review | published
    estimated_hours DECIMAL(4,1),
    total_sessions  INTEGER,
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    
    UNIQUE(client_id, slug)
);
```

### program_knowledge
```sql
CREATE TABLE program_knowledge (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    program_id      UUID NOT NULL REFERENCES programs(id) ON DELETE CASCADE,
    type            TEXT NOT NULL,  -- "brief" | "topic_research" | "path_recommendations" | "program_matrix"
    title           TEXT,
    content         TEXT NOT NULL,
    content_format  TEXT DEFAULT 'markdown',
    version         INTEGER DEFAULT 1,
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    
    UNIQUE(program_id, type)
);
```

### chapters
```sql
CREATE TABLE chapters (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    program_id      UUID NOT NULL REFERENCES programs(id) ON DELETE CASCADE,
    number          INTEGER NOT NULL,
    title           TEXT NOT NULL,
    subtitle        TEXT,
    description     TEXT,
    order_index     INTEGER NOT NULL,
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    
    UNIQUE(program_id, number)
);
```

### sessions
```sql
CREATE TABLE sessions (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    chapter_id          UUID NOT NULL REFERENCES chapters(id) ON DELETE CASCADE,
    number              TEXT NOT NULL,  -- "1.1", "1.2", "2.1"
    title               TEXT NOT NULL,
    hook                TEXT,
    description         TEXT,
    wiifm               TEXT,
    learning_objectives JSONB,
    estimated_minutes   INTEGER DEFAULT 10,
    order_index         INTEGER NOT NULL,
    status              TEXT DEFAULT 'draft',
    created_at          TIMESTAMPTZ DEFAULT NOW(),
    updated_at          TIMESTAMPTZ DEFAULT NOW(),
    
    UNIQUE(chapter_id, number)
);
```

### articles
```sql
CREATE TABLE articles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id      UUID UNIQUE NOT NULL REFERENCES sessions(id) ON DELETE CASCADE,
    content         TEXT NOT NULL,
    word_count      INTEGER,
    sources         JSONB,
    status          TEXT DEFAULT 'draft',
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

### videos
```sql
CREATE TABLE videos (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id          UUID UNIQUE NOT NULL REFERENCES sessions(id) ON DELETE CASCADE,
    script              TEXT NOT NULL,
    word_count          INTEGER,
    estimated_duration  TEXT,
    status              TEXT DEFAULT 'draft',
    created_at          TIMESTAMPTZ DEFAULT NOW(),
    updated_at          TIMESTAMPTZ DEFAULT NOW()
);
```

### quizzes
```sql
CREATE TABLE quizzes (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id      UUID UNIQUE NOT NULL REFERENCES sessions(id) ON DELETE CASCADE,
    questions       JSONB NOT NULL,
    metadata        JSONB,
    status          TEXT DEFAULT 'draft',
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

### exercises
```sql
CREATE TABLE exercises (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id          UUID UNIQUE NOT NULL REFERENCES sessions(id) ON DELETE CASCADE,
    exercise_type       TEXT NOT NULL,  -- "CASE_ANALYSIS" | "ROLEPLAY" | "REFLECTION"
    complexity          TEXT DEFAULT 'basic',
    title               TEXT NOT NULL,
    description         TEXT NOT NULL,
    scenario            JSONB,
    learning_anchor     JSONB,
    ai_mentor           JSONB NOT NULL,
    success_criteria    JSONB,
    evaluation          JSONB,
    estimated_minutes   INTEGER DEFAULT 15,
    status              TEXT DEFAULT 'draft',
    created_at          TIMESTAMPTZ DEFAULT NOW(),
    updated_at          TIMESTAMPTZ DEFAULT NOW()
);
```

## Indexes

```sql
CREATE INDEX idx_programs_client ON programs(client_id);
CREATE INDEX idx_programs_status ON programs(status);
CREATE INDEX idx_chapters_program ON chapters(program_id);
CREATE INDEX idx_sessions_chapter ON sessions(chapter_id);
CREATE INDEX idx_sessions_status ON sessions(status);
CREATE INDEX idx_client_knowledge_client ON client_knowledge(client_id);
CREATE INDEX idx_program_knowledge_program ON program_knowledge(program_id);
```

## RLS

```sql
ALTER TABLE clients ENABLE ROW LEVEL SECURITY;
ALTER TABLE client_knowledge ENABLE ROW LEVEL SECURITY;
ALTER TABLE programs ENABLE ROW LEVEL SECURITY;
ALTER TABLE program_knowledge ENABLE ROW LEVEL SECURITY;
ALTER TABLE chapters ENABLE ROW LEVEL SECURITY;
ALTER TABLE sessions ENABLE ROW LEVEL SECURITY;
ALTER TABLE articles ENABLE ROW LEVEL SECURITY;
ALTER TABLE videos ENABLE ROW LEVEL SECURITY;
ALTER TABLE quizzes ENABLE ROW LEVEL SECURITY;
ALTER TABLE exercises ENABLE ROW LEVEL SECURITY;
```

## Status Flows

**Program:** draft → in_progress → review → published

**Session:** draft → content_created → approved → published

**Content:** draft → review → approved
