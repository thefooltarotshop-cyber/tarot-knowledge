# OL'D LAB Harness v2.0
# 초 능동적 멀티에이전트 하네스

## 하네스란?
에이전트들의 생명주기, 통신, 작업 큐를 관리하는 중추 신경계

## 구성요소

### 1. 프로세스 스케줄러 (Process Scheduler)
```python
class AgentProcess:
    def __init__(self, agent_type, memory_limit, api_endpoint):
        self.pid = None  # 실제 OS 프로세스 ID
        self.status = "idle"  # idle | running | paused | error
        self.memory_limit = memory_limit  # 1GB, 2GB 등
        self.api_model = api_endpoint  # API 기반 = 로컬 부하 ↓
        self.task_queue = Queue()
        
    def spawn(self):
        # 실제 subprocess로 에이전트 생성
        pass
        
    def communicate(self, message):
        # 에이전트 간 메시지 브로커
        pass
```

### 2. 작업 큐 (Task Queue)
```
[Master Queue]
    ├── 우선순위: CRITICAL (사용자 요청)
    ├── 우선순위: HIGH (책 집필 마감)
    ├── 우선순위: NORMAL (일일 콘텐츠)
    └── 우선순위: LOW (백그라운드 OCR, 트렌드)
```

### 3. 상태 모니터 (Status Monitor)
- 각 에이전트의 메모리 사용량 실시간 체크
- API 토큰 사용량 추적
- 에이전트 "건강상태" 체크 (살아있는지)

## 에이전트별 하드웨어/리소스 할당 (16GB 기준)

| 에이전트 | RAM | API 모델 | 프로세스 타입 | 동시실행 |
|---------|-----|---------|--------------|---------|
| **MASTER** | 1GB | Local | 상시 | 1 |
| **RESEARCHER** | 2GB | Claude API | 상시 | 1 |
| **CONTENT** | 1.5GB | GPT-4o-mini | on-demand | 최대 3개 |
| **TREND-OBSERVER** | 1GB | Gemini Flash | 상시 | 1 |
| **BOOK-ARCHITECT** | 2GB | Claude API | on-demand | 1 |
| **OCR-PROCESSOR** | 1.5GB | Local Tesseract | background | 1 |
| **DB-CURATOR** | 1GB | Local | daemon | 1 |
| **여유** | 6GB | - | - | - |

**합계: 16GB** ✅

## 통신 프로토콜 (Agent ↔ Agent)

### 파일 기반 메시징 (File-based Messaging)
```
~/.hermes/tarot-knowledge/mailbox/
├── inbox/           # 받은 편지함
├── outbox/          # 보낼 편지함  
├── processed/       # 처리 완료
└── archive/         # 보관

메시지 포맷: {timestamp}_{from_agent}_{to_agent}_{msg_id}.json
```

### 예시 메시지
```json
{
  "from": "trend-observer",
  "to": "researcher",
  "type": "info",
  "priority": "normal",
  "content": "2024년 '타로+AI' 키워드 트렌딩 감지. 관련 카드: The Magician, The High Priestess",
  "timestamp": "2026-04-09T15:30:00Z",
  "action_required": false
}
```

## 자율 루프 (Autonomous Loop)

### 사용자가 없을 때도 돌아가는 것들:

**5분마다:**
- Trend-Observer: 타로 키워드 스캔

**30분마다:**
- OCR-Processor: 대기 중인 스캔본 처리

**1시간마다:**
- DB-Curator: 백업 체크포인트

**매일 09:00:**
- Content-Generator: 오늘의 콘텐츠 생성

**매일 18:00:**
- Trend-Observer: 일일 트렌드 리포트 작성

**매주 월요일:**
- Book-Architect: 집필 진도 보고

## 충돌 관리 (Conflict Resolution)

### 시나리오: 두 에이전트가 같은 파일 수정하려 할 때
```
1. 파일 잠금 메커니즘: .lock 파일 생성
2. 선착순: 먼저 요청한 에이전트 우선
3. 병합: Git-style diff 병합 시도
4. 에스컬레이션: Master가 최종 판단
```

## 장애 복구 (Fault Tolerance)

### 에이전트 죽었을 때:
```
[Monitor] Agent X not responding (timeout: 60s)
    ↓
[Master] 자동 재시작 시도 (max 3회)
    ↓
성공 → 작업 재개
실패 → 사용자 알림 + 다른 에이전트에 작업 이양
```

## API 비용 최적화 전략

### 토큰 캐싱 (Token Caching)
- 자주 쓰는 프롬프트 템플릿은 해싱해서 재사용
- "타로 카드 해석" 기본 프롬프트는 캐싱

### 모델 계층화 (Model Tiering)
```
고품질 필요: Claude Opus (비쌈, 하지만 정확)
일반 작업: Claude Sonnet (적당)
단순 작업: GPT-4o-mini (싸고 빠름)
트렌드 수집: Gemini Flash (거의 무료)
```

### 배치 처리 (Batching)
- OCR 결과 10개 모아서 한 번에 요약
- 인스타글 7개 한꺼번에 생성

## 성공 지표 (Metrics)

### 주간 리포트 자동 생성:
```
[OL'D LAB Weekly Report]
├── 처리한 작업: 127개
├── 생성한 콘텐츠: 21개
├── OCR 처리 페이지: 45페이지
├── 트렌드 리포트: 7개
├── API 비용: $12.34
├── 업타임: 99.7%
└── 다음 주 예정 작업: 15개
```

## 철학 (Philosophy)

> "우리는 폐기 직전의 Dell 노트북으로 
> 토큰 괴물들과 싸워 이길 것이다.
> 비용은 최소화하고,
> 능동성은 극대화하고,
> 구조는 확장 가능하게.
> 
> 배터리가 부풀어도,
> 디스플레이가 노래져도,
> 우리의 두뇌는 영원하다."

---

최종 업데이트: 2026-04-09 (리부트 완료)
버전: 2.0 - "쫄지 않는다"
