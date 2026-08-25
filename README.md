[Usage Efficiency — Claude Custom Skill.md](https://github.com/user-attachments/files/31412013/Usage.Efficiency.Claude.Custom.Skill.md)
# Usage Efficiency — Claude Custom Skill

> **Claude의 사용량 제한을 우회하지 않고**, 불필요한 대화 왕복·중복 컨텍스트·반복 첨부를 줄여 작업을 더 효율적으로 처리하도록 돕는 Claude 사용자 정의 스킬입니다.

`usage-efficiency`는 Claude에 보낼 요청을 완결된 작업 단위로 구성하고, 같은 목적의 하위 과제를 적절히 묶으며, 현재 대화와 프로젝트 지식을 재사용하도록 안내합니다. 또한 한도 경고가 발생했을 때에는 우회가 아닌 필수 작업 우선순위화와 공식 사용량 확인을 적용합니다.

| 항목 | 내용 |
|---|---|
| 대상 | Claude.ai 사용자 정의 스킬 |
| 스킬 이름 | `usage-efficiency` |
| 업로드 파일 | `usage-efficiency-claude-ai.zip` |
| 필수 기능 | Claude의 **Code execution and file creation** 활성화 |
| 주요 적용 상황 | Claude 사용량 절약, 프롬프트 구조화, 반복 파일·맥락 관리, 한도 경고 대응 |
| 실행 코드·외부 호출 | 없음 |

## 핵심 기능

Anthropic은 메시지 길이, 첨부 파일 크기, 대화 길이, 도구 사용, 모델, 노력 수준, 아티팩트 사용 등이 Claude 사용량에 영향을 줄 수 있다고 안내합니다.[1] 이 스킬은 품질에 필요한 검증을 생략하지 않으면서 아래 원칙을 Claude 작업에 적용합니다.

| 원칙 | 적용 방식 |
|---|---|
| 완결된 요청 | 목표, 배경, 입력, 수행 항목, 제약, 출력 형식, 완료 기준을 한 번에 정리합니다. |
| 적절한 일괄 처리 | 같은 자료·목적·출력 형식을 공유하는 작업만 하나의 요청으로 묶습니다. |
| 기존 맥락 재사용 | 이미 제공된 대화 내용, 프로젝트 지식, 업로드 파일을 참조해 중복 입력을 줄입니다. |
| 전송 전 점검 | 모호한 지시, 누락된 핵심 자료, 상충된 제약을 먼저 확인합니다. |
| 긴 작업 관리 | 계획 → 승인 필요한 결정 → 제작 → 검증·수정 순으로 진행합니다. |
| 한도 안전 대응 | 잔여량을 추정하거나 제한을 우회하지 않고, 필수 산출물 우선순위화와 공식 사용량 화면 확인을 안내합니다. |

## Claude에 설치하기

Claude 사용자 정의 스킬은 Free, Pro, Max, Team, Enterprise 플랜에서 사용할 수 있으며, 코드 실행 기능을 활성화해야 합니다.[2]

먼저 Claude에서 **Settings → Capabilities → Code execution and file creation**을 켭니다. Team 또는 Enterprise 환경에서는 조직 설정에서 해당 기능과 Skills가 허용되어 있어야 할 수 있습니다.[2]

그 다음 아래 순서로 업로드합니다.

1. 이 저장소의 [Releases] 탭에서 `usage-efficiency-claude-ai.zip`을 다운로드합니다.
2. Claude의 [Customize → Skills](https://claude.ai/customize/skills)로 이동합니다.
3. **+** 버튼을 누른 뒤 **Create skill**을 선택합니다.
4. **Upload a skill**을 선택하고 ZIP 파일을 업로드합니다.
5. 목록에서 `usage-efficiency`를 켭니다.

Claude는 요청 내용이 스킬 설명과 일치할 때 스킬을 자동 적용합니다. 적용이 안 되면 스킬이 켜져 있는지 확인하고, 예를 들어 “사용량 효율화 스킬을 사용해 이 Claude 요청을 정리해줘”처럼 명시적으로 요청해 보세요.[2]

## 저장소 구조

```text
.
├── README.md
├── usage-efficiency-claude-ai.zip     # Claude에 업로드할 완성 패키지
└── usage-efficiency/
    └── skill.md                       # 스킬의 원본 지침 파일
```

Claude.ai의 공식 안내에 따르면 ZIP 파일의 루트에는 스킬 폴더가 있어야 하며, 스킬 폴더 안에는 최소한 `skill.md`와 YAML 메타데이터가 포함되어야 합니다.[3] 이 저장소의 배포용 ZIP은 해당 구조로 만들었습니다.

```text
usage-efficiency-claude-ai.zip
└── usage-efficiency/
    └── skill.md
```

> GitHub의 **Code → Download ZIP**으로 내려받은 소스 아카이브는 업로드하지 마세요. 저장소 전체가 한 단계 더 들어가 있어 Claude가 기대하는 패키지 구조와 다를 수 있습니다. 반드시 릴리스에 첨부한 `usage-efficiency-claude-ai.zip`을 사용하거나, 아래 명령으로 스킬 폴더만 압축하세요.

```bash
zip -r usage-efficiency-claude-ai.zip usage-efficiency/
```

## 사용 예시

### 코딩 요청

```text
사용량 효율화 스킬을 적용해 아래 디버깅 작업을 처리해줘.

환경: React 18, TypeScript, Vite
목표: 첨부한 오류를 해결하고 수정 파일별 전체 코드를 제시해줘.
입력: 오류 전문과 관련 파일은 위 대화에 있음.
제약: 기존 API 인터페이스를 변경하지 말 것.
출력: 원인 요약, 수정 코드, 검증 절차.
```

### 글쓰기 요청

```text
아래 보도자료를 B2B SaaS 잠재 고객용으로 수정해줘.

목적: 무료 체험 전환 유도
독자: IT 팀장과 구매 담당자
톤: 신뢰감 있고 간결하게
필수 요점: 보안 인증, 도입 기간, 고객 지원
출력: 제목 3개와 400~500자 본문
원문: [전체 원문]
```

### 리서치·분석 요청

```text
다음 리서치 요청을 사용량 효율화 원칙에 따라 처리해줘.

연구 질문: 2024~2026년 국내 생성형 AI 도입 동향은 무엇인가?
범위: 기업 도입 사례와 규제 변화만 포함.
근거 기준: 1차 출처 또는 신뢰할 수 있는 언론·연구기관 우선.
출력: 핵심 발견 5개, 출처 링크, 기업 실무자 시사점 표.
```

## 동작 원칙과 제한 사항

이 스킬은 계정에 로그인하거나 사용량을 읽고 변경하지 않습니다. 사용량 수치, 재설정 시점, 플랜별 제한은 사용자가 Claude의 **Settings → Usage**에서 직접 확인해야 합니다.[1]

또한 이 스킬은 제한 우회, 다중 계정 활용, 비정상 자동화, 승인 없는 대화·파일 삭제를 지원하지 않습니다. 프로젝트 지식 또는 캐싱의 효과를 절대적으로 보장하지 않으며, 사용량을 절감한다는 이유로 정확성·보안·개인정보 보호·필수 검증을 낮추지 않습니다.

## 보안

이 패키지에는 실행 스크립트, 의존성, 외부 네트워크 호출, API 키가 포함되어 있지 않습니다. `skill.md`에는 Claude가 따라야 할 텍스트 지침만 들어 있습니다. Anthropic은 사용자 정의 스킬을 활성화하기 전 모든 파일을 검토하고, 신뢰할 수 있는 출처의 스킬만 사용하라고 권장합니다.[2]

## 기여

개선 제안은 Issue 또는 Pull Request로 남겨 주세요. 변경 시에는 다음 원칙을 유지해 주세요.

- 사용자의 실제 작업 품질을 우선할 것
- 이미 제공된 맥락과 자료를 재사용할 것
- 불필요한 질문과 반복 첨부를 줄일 것
- 사용량 제한을 우회하지 않을 것
- 외부 코드·네트워크 호출을 추가할 때는 보안 검토를 명시할 것

## 참고 자료

[1]: https://support.claude.com/en/articles/9797557-usage-limit-best-practices "Usage limit best practices | Anthropic Help Center"
[2]: https://support.claude.com/en/articles/12512180-use-skills-in-claude "Use skills in Claude | Anthropic Help Center"
[3]: https://support.claude.com/en/articles/12512198-how-to-create-custom-skills "How to create custom skills | Anthropic Help Center"
