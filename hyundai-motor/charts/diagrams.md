# Mermaid 차트 모음

## 1. 기업 구조

### 현대차그룹 지배구조

```mermaid
graph TB
    subgraph 총수일가
        MK[정몽구<br/>명예회장]
        ES[정의선<br/>회장]
    end
    
    subgraph 자동차
        HMC[현대자동차<br/>완성차]
        KIA[기아<br/>완성차]
        GEN[제네시스<br/>프리미엄]
    end
    
    subgraph 부품
        MOB[현대모비스<br/>부품/AS]
        WIA[현대위아<br/>파워트레인]
        TRS[현대트랜시스<br/>변속기]
    end
    
    subgraph 신사업
        BD[Boston Dynamics<br/>로보틱스]
        SUP[Supernal<br/>UAM]
        MOT[Motional<br/>자율주행]
        DOT[42dot<br/>SW]
    end
    
    MK --> ES
    ES --> HMC
    ES --> KIA
    HMC --> GEN
    HMC --> MOB
    HMC --> BD
    HMC --> SUP
    HMC --> MOT
    HMC --> DOT
```

### 브랜드 포트폴리오

```mermaid
graph LR
    subgraph 현대
        H1[아반떼<br/>소형 세단]
        H2[쏘나타<br/>중형 세단]
        H3[투싼<br/>소형 SUV]
        H4[싼타페<br/>중형 SUV]
        H5[팰리세이드<br/>대형 SUV]
    end
    
    subgraph 제네시스
        G1[G70<br/>스포츠 세단]
        G2[G80<br/>럭셔리 세단]
        G3[G90<br/>플래그십]
        G4[GV70<br/>럭셔리 SUV]
        G5[GV80<br/>대형 SUV]
    end
    
    subgraph IONIQ
        I1[IONIQ 5<br/>CUV]
        I2[IONIQ 6<br/>세단]
        I3[IONIQ 5 N<br/>고성능]
        I4[IONIQ 7<br/>대형 SUV]
    end
```

## 2. 시장 분석

### 글로벌 자동차 시장 점유율

```mermaid
pie title 2024 글로벌 판매량 점유율
    "토요타" : 12
    "폭스바겐" : 10
    "현대차그룹" : 8
    "스텔란티스" : 7
    "GM" : 7
    "포드" : 5
    "르노닛산" : 6
    "혼다" : 5
    "기타" : 40
```

### EV 판매량 추이

```mermaid
xychart-beta
    title "글로벌 BEV 판매량 추이 (만대)"
    x-axis [2020, 2021, 2022, 2023, 2024E, 2025E, 2030E]
    y-axis "판매량 (만대)" 0 --> 4500
    bar [310, 670, 1050, 1350, 1600, 2000, 4000]
```

### 지역별 전략

```mermaid
mindmap
    root((현대차 글로벌 전략))
        북미
            SUV 중심
            EV 공장 건설
            IRA 수혜
            제네시스 확대
        유럽
            IONIQ 라인업
            2035 규제 대응
            체코 생산 확대
        중국
            로컬 EV 경쟁
            JV 재정립
            프리미엄 집중
        인도
            시장 2위 유지
            소형 SUV 강화
            EV 도입
        한국
            내수 1위 수성
            프리미엄 강화
            R&D 거점
```

## 3. 재무 분석

### 실적 추이

```mermaid
xychart-beta
    title "현대차 연간 영업이익 추이 (조원)"
    x-axis [2020, 2021, 2022, 2023, 2024E]
    y-axis "영업이익 (조원)" 0 --> 18
    bar [2.8, 6.7, 9.8, 15.1, 15.5]
```

### 매출 구성

```mermaid
pie title 2024 매출 구성 (추정)
    "내연기관차" : 55
    "전기차 (BEV)" : 18
    "하이브리드" : 15
    "수소차" : 2
    "금융/기타" : 10
```

## 4. EV 전략

### E-GMP 플랫폼 구조

```mermaid
graph TB
    subgraph E-GMP["E-GMP 플랫폼"]
        BAT[배터리 팩<br/>77.4kWh 표준]
        MOT[구동 모터<br/>PE 시스템]
        V8[800V 시스템<br/>초급속 충전]
        AWD[AWD 옵션<br/>듀얼 모터]
    end
    
    subgraph 차종
        I5[IONIQ 5]
        I6[IONIQ 6]
        I5N[IONIQ 5 N]
        I7[IONIQ 7]
        GV60[GV60]
        EG80[eG80]
    end
    
    E-GMP --> I5
    E-GMP --> I6
    E-GMP --> I5N
    E-GMP --> I7
    E-GMP --> GV60
    E-GMP --> EG80
```

### EV 로드맵

```mermaid
timeline
    title 현대차 EV 로드맵
    2021 : IONIQ 5 출시
         : E-GMP 첫 적용
    2022 : IONIQ 6 출시
         : 에어로 세단
    2024 : IONIQ 5 N 출시
         : 고성능 EV
    2025 : IONIQ 7 출시
         : 대형 SUV
         : 미국 조지아 공장 가동
    2026 : IMA 플랫폼 도입
         : 통합 모듈러 아키텍처
    2030 : 연 200만대 EV
         : 글로벌 EV 리더십
```

## 5. 경쟁 분석

### 경쟁 포지셔닝

```mermaid
quadrantChart
    title Market Position (Volume vs Premium)
    x-axis "Volume Brand" --> "Premium Brand"
    y-axis "ICE Focused" --> "EV Focused"
    quadrant-1 "Premium EV"
    quadrant-2 "Premium ICE"
    quadrant-3 "Volume ICE"
    quadrant-4 "Volume EV"
    "Hyundai": [0.35, 0.55]
    "Genesis": [0.7, 0.5]
    "Tesla": [0.65, 0.95]
    "Toyota": [0.4, 0.2]
    "VW": [0.45, 0.5]
    "BMW": [0.8, 0.45]
    "BYD": [0.3, 0.85]
```

### 밸류체인 비교

```mermaid
graph LR
    subgraph 현대차
        H_D[디자인] --> H_E[엔지니어링] --> H_P[생산] --> H_S[판매]
        H_B[배터리<br/>외주] -.-> H_P
    end
    
    subgraph 테슬라
        T_D[디자인] --> T_E[엔지니어링] --> T_P[생산] --> T_S[직판]
        T_B[배터리<br/>자체+외주] --> T_P
        T_SW[소프트웨어] --> T_P
    end
    
    subgraph BYD
        B_D[디자인] --> B_E[엔지니어링] --> B_P[생산] --> B_S[판매]
        B_B[배터리<br/>자체생산] --> B_P
        B_C[반도체<br/>자체] --> B_P
    end
```

## 6. SWOT 분석

```mermaid
quadrantChart
    title SWOT Analysis - Hyundai Motor
    x-axis "Internal" --> "External"
    y-axis "Negative" --> "Positive"
    quadrant-1 "Opportunities"
    quadrant-2 "Strengths"
    quadrant-3 "Weaknesses"
    quadrant-4 "Threats"
    "E-GMP Platform": [0.25, 0.85]
    "Genesis Brand": [0.35, 0.75]
    "Global Network": [0.3, 0.8]
    "Cost Efficiency": [0.2, 0.7]
    "China Market": [0.35, 0.25]
    "SW Capability": [0.25, 0.35]
    "Labor Issues": [0.4, 0.3]
    "EV Market Growth": [0.75, 0.9]
    "India Expansion": [0.8, 0.75]
    "IRA Benefits": [0.7, 0.8]
    "Tesla Competition": [0.7, 0.2]
    "BYD Price War": [0.75, 0.25]
    "Raw Material": [0.65, 0.35]
```

---

> 💡 **참고**: Mermaid 차트는 GitHub에서 네이티브 렌더링됩니다. 일부 복잡한 차트 타입은 렌더링이 제한될 수 있습니다.

## 출처

본 차트의 데이터는 다음 문서들을 참조합니다:
- company-profile.md: 기업 구조, 브랜드 포트폴리오, 재무 실적
- market-analysis.md: 시장 규모, EV 판매량 추이, 지역별 전략
- competitors.md: 경쟁사 비교, 포지셔닝 분석

각 데이터 포인트의 상세 출처는 해당 원본 문서의 "## 출처" 섹션을 참조하시기 바랍니다.
