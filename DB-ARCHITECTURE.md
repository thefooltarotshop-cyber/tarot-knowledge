# 타로 DB 아키텍처: 3레이어 시스템

## Layer 1: Obsidian ( markdown 파일 ) = Primary Interface
- **역할**: 인간이 보는 위키피디아
- **형태**: `[[00-the-fool]]` 같은 위키링크로 연결된 마크다운
- **강점**: 그래프 뷰(시각화), 즉시 수정, 직관적
- **관리**: Git으로 버전 관리

## Layer 2: SQLite (Structured Data) = Backend Engine
- **역할**: 에이전트가 쿼리하는 구조화된 데이터
- **테이블 예시**:
  ```sql
  
CREATE TABLE cards (
      id TEXT PRIMARY KEY,  -- "00-the-fool"
      name TEXT,            -- "The Fool"
      arcana TEXT,          -- "major" | "minor"
      element TEXT,         -- "air" | "fire" | "water" | "earth"
      number INTEGER,       -- 0 ~ 21 (major), 1~10 + 11~14 (courts)
      status TEXT,          -- "draft" | "review" | "published"
      last_updated DATE,
      word_count INTEGER    -- 책 집필 진도 추적
  );
  
CREATE TABLE relationships (
      card_a TEXT,
      card_b TEXT,
      relation_type TEXT,   -- "contrasts_with" | "complements" | "leads_to"
      strength FLOAT        -- 0.0 ~ 1.0 (연관 강도)
  );
  ```

## Layer 3: ChromaDB (Vector DB) = Semantic Search (선택)
- **역할**: "새로운 시작과 관련된 카드 찾아줘" 같은 의미 검색
- **사용 시점**: 78장 모두 채우고 나서 (초기엔 불필요)
- **예시**: 
  - Query: "new beginnings"
  - Result: [The Fool (0.95), Ace of Wands (0.88), The Sun (0.82)]

## 왜 이렇게 나눴나?
- **Obsidian**: 당신이 직접 보고 수정하기 쉬움 ( human-friendly )
- **SQLite**: 에이전트가 빠르게 쿼리 ( machine-friendly )
- **Chroma**: 나중에 AI 리딩 서비스 할 때 필요 ( similarity search )

## 싱크 전략
```
Obsidian (수정) 
   ↓ 자동 파싱
SQLite (업데이트)
   ↓ 주간 배치
Chroma (임베딩 재생성)
```
