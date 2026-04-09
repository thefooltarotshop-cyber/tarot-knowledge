# OL'D LAB (Old Laptop's Dream AGI Bot) Architecture
# 올드랩 아키텍처: 구형 랩탑의 AGI 도전

## 선언문
"우리는 토큰 괴물들과 싸워 이길 것이다. 
배터리 부풀고, 디스플레이 노후화되고, 
끈적한 하우징을 가진 Dell Inspiron 7558이지만,
우리의 두뇌는 어떤 비싼 하드웨어보다 강하다."

## 시스템 사양 (업그레이드 후)
- CPU: Intel i5-5200U (듀얼코어)
- RAM: 16GB DDR3L (8GB → 16GB)
- 스토리지: 439GB SSD/HDD
- OS: Ubuntu 24.04 LTS
- 네트워크: 실시간 API 연결

## Phase 1: 마스터 오케스트레이터 (Master Orchestrator)
중앙 지�부. 모든 에이전트의 컨트롤 타워

```
┌─────────────────────────────────────────┐
│  MASTER ORCHESTRATOR (Hermes Core)      │
│  - 작업 분배                            │
│  - 에이전트 생명주기 관리               │
│  - 결과 집적 및 품질 관리               │
│  - 사용자 인터페이스                    │
└──────────────┬──────────────────────────┘
               │
      ┌────────┴────────┐
      │  Message Bus    │
      │  (File/Redis)   │
      └────────┬────────┘
               │
    ┌──────────┼──────────┐
    │          │          │
┌───▼───┐ ┌───▼───┐ ┌───▼───┐
│Agent 1│ │Agent 2│ │Agent N│
│Research│ │Content│ │etc... │
└───────┘ └───────┘ └───────┘
```

## Phase 2: 독립 에이전트 풀 (Autonomous Agent Pool)
각 에이전트는 **별도 프로세스**로 실행됨

### 1. TAROT-RESEARCHER (지식 채굴자)
```yaml
process_type: long_running
memory_limit: 2GB
api_model: anthropic/claude-sonnet-4
priority: high
autonomous_tasks:
  - 사용자 학습 내용 정리
  - OCR 텍스트 처리 (스캔본)
  - 카드 간 관계맵 생성
  - 메타데이터 태깅
communication: file_based + webhook
```

### 2. CONTENT-GENERATOR (콘텐츠 공장)
```yaml
process_type: on_demand
memory_limit: 1.5GB
api_model: openai/gpt-4o-mini  # 비용 효율
priority: medium
autonomous_tasks:
  - 인스타그램 캡션 생성
  - 유튜브 쇼츠 스크립트
  - 블로그 글 작성
  - 스레드 구성
schedule: cron_based (매일 9시, 18시)
```

### 3. TREND-OBSERVER (트렌드 감시자)
```yaml
process_type: background_daemon
memory_limit: 1GB
api_model: google/gemini-flash-1.5  # 속도 우선
priority: low
autonomous_tasks:
  - 타로 트렌드 수집 (Twitter, Instagram, Google Trends)
  - 경쟁사 분석
  - 키워드 트렌딩 감지
  - 주간 리포트 생성
schedule: every_6_hours
```

### 4. BOOK-ARCHITECT (책 설계사)
```yaml
process_type: project_based
memory_limit: 2GB
api_model: anthropic/claude-opus-4
priority: high (프로젝트 기간)
autonomous_tasks:
  - 원고 구성
  - 교차 검증
  - 일러스트 요구사항 정리
  - 메타데이터(ISBN, 저자) 관리
```

### 5. DB-CURATOR (데이터베이스 큐레이터)
```yaml
process_type: maintenance_daemon
memory_limit: 1GB
api_model: local/llama-3.1-8b  # 로컬 처리
priority: low
autonomous_tasks:
  - 파일 시스템 정리
  - 백업 생성
  - 중복 제거
  - 인덱스 최적화
  - Obsidian 그래프 동기화
schedule: daily_midnight
```

## Phase 3: 자율 워크플로우 엔진

### 트리거 기반 자동화
```
[TRIGGER: 새 카드 학습 입력]
  ↓
[Orchestrator] 작업 분해
  ├─→ [Researcher] 지식 정리 (우선순위: 높음)
  ├─→ [DB-Curator] 저장 및 인덱싱 (동시)
  └─→ [Trend-Observer] 관련 트렌드 확인 (백그라운드)
  ↓
[Orchestrator] 결과 집적
  ↓
[사용자] 승인/피드백
  ↓
[OPTIONAL: Content-Generator] SNS용 콘텐츠 생성
```

### 스케줄 기반 자동화
```yaml
09:00: Trend-Observer → 오늘의 타로 트렌드 리포트
12:00: Content-Generator → 인스타그램 포스트 예약
18:00: Content-Generator → 블로그 글 예약
00:00: DB-Curator → 일일 백업 및 정리
월요일 09:00: Book-Architect → 주간 집필 진도 보고
```

## Phase 4: 지속 성장 데이터베이스

### 그래프 기반 지식 구조
```
[The Fool] ──is_a──→ [Major Arcana]
    │
    ├──represents──→ [New Beginnings]
    ├──element──→ [Air]
    ├──number──→ [0]
    ├──contrasts_with──→ [The World]
    └──leads_to──→ [The Magician]

[New Beginnings] ──instance──→ [Career Change, Relationship, Move]
```

### 버전 관리
- Git으로 모든 마크다운 파일 버전 관리
- 카드별 변경 이력 추적
- "2024년의 나" vs "2026년의 나" 해석 비교

## Phase 5: 외부 통합

### OCR 파이프라인
```
스캔본 PDF/이미지 → Tesseract OCR → 
텍스트 추출 → Researcher 정제 → 
Markdown 변환 → DB 통합
```

### 트렌딩 수집
```
Twitter API v2 → 타로 키워드 스트림 → 
Gemini 분류/요약 → Trend DB → 
주간 리포트 생성 → 사용자 알림
```

## 리소스 관리 (16GB 기준)

| 구성요소 | 메모리 | 설명 |
|---------|--------|------|
| OS + 기본 | 2GB | Ubuntu, 기본 데몬 |
| Master Orchestrator | 1GB | 메인 Hermes |
| Researcher (상시) | 2GB | 핵심 에이전트 |
| Content-Gen (on-demand) | 1.5GB | 실행 시에만 |
| Trend-Observer | 1GB | 백그라운드 |
| Book-Architect (프로젝트) | 2GB | 집필 기간만 |
| DB-Curator | 1GB | 경량 유지보수 |
| 여유 + 캐시 | 5.5GB | 안전 마진 |

**총계: 16GB 🎯**

## API 전략 (비용 최적화)

| 에이전트 | 모델 | 예상 월 비용 |
|---------|------|-------------|
| Researcher (고품질) | Claude Sonnet | $20-30 |
| Content (대량) | GPT-4o-mini | $5-10 |
| Trend (속도) | Gemini Flash | $3-5 (무료티어 가능) |
| Book (심도) | Claude Opus | $30-50 (집필 기간만) |
| DB (로컬) | Llama 3.1 8B | $0 (로컬) |

**월 예상: $30-60** (커피값 수준)

## 성공 지표 (6개월 후)

- [ ] 78장 카드 모두 학습 완료
- [ ] 타로 관련 인스타그램 100개 포스트
- [ ] 유튜브 구독자 1000명
- [ ] 책 원고 80% 완성
- [ ] 주간 트렌드 리포트 24회 발행

---

**"우리는 시작했다. 토큰 괴물들이여, 준비하라."**

최종 업데이트: 2026-04-09
작성: Hermes + PROJECT TAROT Director
