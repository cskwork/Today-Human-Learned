# ReAct 에이전트 프레임워크 — 어떤 걸 골라야 할까

> **TL;DR**
> ReAct 패턴을 지원하는 프레임워크는 여럿이지만, 성숙도와 생산 준비 여부가 제각각이다. 시작점은 LangChain, 복잡한 흐름 제어가 필요하면 LangGraph, 다중 에이전트 협업이 목적이면 CrewAI를 선택하라.

---

## ReAct 에이전트를 왜 쓰는지 감 잡기

ReAct(Reasoning + Acting)는 LLM이 "생각 → 행동 → 관찰"을 반복하며 문제를 해결하는 설계 패턴이다. 단순히 한 번 답을 생성하는 것이 아니라, 도구(Tool)를 호출하고 결과를 보면서 다음 행동을 결정한다.

예를 들어 "서울 내일 날씨를 알려줘"라는 요청이 들어오면, 에이전트는 날씨 API 도구를 호출하고, 결과를 받아 최종 답변을 구성한다. 중간에 오류가 생기면 다른 도구로 재시도한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 생각(Thought) → 행동(Action) → 관찰(Observation) → 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| ReAct | LLM이 생각하고 도구를 쓰고 결과를 보는 사이클을 반복하는 패턴 |
| Tool | 에이전트가 호출할 수 있는 외부 기능. 검색 API, 계산기, DB 조회 등 |
| AgentExecutor | 에이전트가 목표에 도달할 때까지 ReAct 사이클을 반복 실행하는 런너 |
| Checkpointer | 대화 상태를 저장해서 대화가 끊겨도 이어서 진행할 수 있게 하는 장치 |
| Crew | CrewAI에서 여러 에이전트를 하나의 팀으로 묶는 컨테이너 |

## 예를 들어 설명하면

LangChain으로 ReAct 에이전트를 만드는 최소 코드다.

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain_openai import ChatOpenAI
from langchain_community.tools import TavilySearchResults
from langchain import hub

llm = ChatOpenAI(model="gpt-4")
tools = [TavilySearchResults()]
prompt = hub.pull("hwchase17/react")  # ReAct 프롬프트 템플릿

agent = create_react_agent(llm, tools, prompt)
executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = executor.invoke({"input": "서울 내일 날씨는?"})
```

`verbose=True`를 켜두면 생각 → 행동 → 관찰 사이클을 콘솔에서 직접 볼 수 있어, 에이전트가 어떻게 추론하는지 파악하기 좋다.

## 이 단계에서 중요한 판단 기준

프레임워크를 고를 때 가장 먼저 물어야 할 것은 "이 작업에 여러 에이전트가 필요한가, 단일 에이전트로 충분한가"이다.

| 프레임워크 | ReAct 지원 | 학습 난이도 | 생산 적용 | 적합한 상황 |
|---|---|---|---|---|
| LangChain | 네이티브 | 중간 | 가능 | 범용, 성숙한 생태계 필요 시 |
| LangGraph | 커스터마이징 가능 | 높음 | 가능 | 상태 흐름을 세밀하게 제어할 때 |
| CrewAI | LangChain 기반 | 낮음 | 가능 | 역할 분담 다중 에이전트 |
| AutoGen | 직접 구현 | 높음 | 가능 | 복잡한 추론, 코드 실행 포함 |
| Swarm | 기초 수준 | 낮음 | 불가 | 학습용, 프로토타입 |

## 한 줄 요약 — 이것만 기억하면 된다

**ReAct 에이전트를 처음 만든다면 LangChain으로 시작하고, 워크플로우가 복잡해질 때 LangGraph로 이동하라.**

## 나중에 더 깊게 들어가면

- LangGraph의 상태 그래프(StateGraph)와 조건부 엣지 설계
- AutoGen의 다중 에이전트 대화와 코드 실행 샌드박스
- Pydantic 타입 안전성을 유지하며 LangChain 출력 파서 연동하기

---

**원본:** [ReAct Agent Frameworks — https://memoryhub.tistory.com/634](https://memoryhub.tistory.com/634)
