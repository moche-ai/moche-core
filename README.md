# moche-native

**밑바닥부터 만드는 한국어+영어 소형 옴니 모델** — 첫 토큰부터 직접 설계·학습하는 프롬스크래치 프로젝트의 공개 기록.

> 목표: 0.4B→1B 한·영 모델을 프롬스크래치로 학습하고, 장기적으로 듣고·말하고·보는 엣지 옴니 에이전트로 확장한다. 완성된 모델을 빌려 쓰는 대신 토크나이저 첫 병합 규칙부터 전부 소유한다.

이 저장소는 진행 상황을 **투명하게 공개**하는 미러다. 목적은 두 가지 — ① from-scratch 소형 모델을 만드는 실제 과정을 기록으로 남기고, ② 한국어 프롬스크래치에 관심 있는 사람에게 참고가 되는 것.

## 현재 상태 (2026-07-08)

- ✅ **G0-2 토크나이저 완료** — 한·영 균형 64k BPE. 한국어 fertility가 GPT-2 대비 **5.0×** ([tokenizer/FERTILITY.md](tokenizer/FERTILITY.md))
- ✅ **G0 배관 검증 완료** — d20 캘리브레이션으로 학습 파이프라인 재현성 확인. loss 10.4→2.46 매끈하게 하강(spike 0), CORE 0.2409, MFU 45%, 재시작 0 = 조용한 버그 없음 확정
- 🔄 **G1 기준선 학습 중** — 0.49B(depth12) · 우리 64k 토크나이저 · EN(ClimbMix)+KO(fineweb-edu) 인터리브 · bf16 · Muon · WSD · **트릭 0개**. 완주 시 EN/KO 평가 = 이후 모든 비교의 원점
- ⏭️ 다음: G2 — 아키텍처·최적화 트릭 독립 ablation

## 게이트 로드맵

| 게이트 | 내용 |
|---|---|
| **G0** 배관 | 토크나이저 + 알려진 결과 재현으로 학습 파이프라인 검증 |
| **G1** 기준선 | 0.4B · bf16 · 표준 어텐션 · Muon · 트릭 0개 (이후 모든 비교의 원점) |
| **G2** 승격전 | 아키텍처·최적화 트릭을 각각 독립 ablation으로 오디션 |
| **G3** 본런 | 1B 상주 학습 (WSD + 말기 어닐링) + 포스트트레이닝 |
| **G4** 감각 편입 | 음성 풀듀플렉스 + 시각 이해 (코덱/VQ 토큰 어휘 편입) |

## 설계 원칙

- **몸과 공방**: 실시간 상호작용(대화·음성·시각이해)은 가중치 안(몸), 고품질 생성(이미지·영상)은 외부 도구 호출(공방). "모든 기능이 아니라 모든 상호작용을 넣는다."
- **모든 것이 어휘다**: 텍스트·음성(코덱 토큰)·이미지(VQ 토큰)·도구 호출(functional 토큰)을 하나의 어휘로 통일 — 단일 next-token 예측기가 전부 처리.
- **기준선 먼저**: 트릭은 프록시에서 크기 추세(0.1→0.3→0.9B)로 검증된 것만 승격. 잘 튜닝된 바닐라가 원점.
- **셀프호스팅·저비용**: 자체 하드웨어, 공개 데이터, 오픈소스 스택. 상업화 목적 아님.

## 스택

- 실험/캘리브레이션: [nanochat](https://github.com/karpathy/nanochat) 포크 ([exp/PIN.md](exp/PIN.md) — 커밋 핀 + RTX PRO 6000/Blackwell(sm_120) 패치 노트)
- 옵티마이저: Muon · 스케줄: WSD + 어닐링 · 안정성: QK-Norm/z-loss
- 데이터(공개): [ClimbMix](https://huggingface.co/datasets/nvidia/ClimbMix)(EN) · [fineweb-2-edu-korean](https://huggingface.co/datasets/minpeter/fineweb-2-edu-korean-score-2) · [HPLT 3.0](https://hplt-project.org/datasets/v3.0)(KO)

## 자산

- 🤗 토크나이저: [moche-ai/moche-tokenizer-ko-en-64k](https://huggingface.co/moche-ai/moche-tokenizer-ko-en-64k)

---

*이 저장소는 연구 산출물 공개 미러입니다. 운영 인프라 배선은 포함하지 않습니다.*
