# ✈️ AI 기반 국내 여행지 추천 & 일정 플래너 (Travel Planner)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![PyQt5](https://img.shields.io/badge/GUI-PyQt5-green.svg)](https://pypi.org/project/PyQt5/)
[![Google GenAI](https://img.shields.io/badge/AI-Google_GenAI-orange.svg)](https://pypi.org/project/google-genai/)
[![Kakao API](https://img.shields.io/badge/API-Kakao_Local-yellow.svg)](https://developers.kakao.com/)

Google Gemini AI 및 카카오 지도 REST API를 연동하여 맞춤형 여행 일정표를 생성하는 파이프라인 프로그램입니다.  
**CLI(명령줄 인터페이스)** 및 **PyQt5 GUI(그래픽 사용자 인터페이스)** 모드를 모두 지원합니다.

---

## 📌 1. 프로그램 개요 (Overview)

본 프로그램은 사용자가 지정한 출발 날짜, 목적지, 여행 기간, 동행인 및 여행 테마를 기반으로 AI가 정교한 맞춤 여행 일정을 수립해 줍니다.

* **CLI 모드**: 파이프라인 연동 (1차 LLM JSON 분석 → 카카오 API 맛집 및 좌표(`x`, `y`) 수집 → 2차 LLM 마크다운 합성 → `results/` 저장)
* **GUI 모드**: PyQt5 기반 맞춤 UI, 카카오 지도 API 맛집 연동, Microsoft Edge TTS 신경망 음성 설명 재생, Google Maps 경로 자동 조회 지원

---

## 🚀 2. 주요 기능 및 변경사항 (Key Features)

1. **CLI 입력값 날짜 검증 (`main.py`)**
   * `-date` 인자의 `YYYY-MM-DD` 정규식 및 실존 날짜 유효성을 엄격히 검증합니다.
   * 형식이 올바르지 않은 경우 사용법 안내 메시지를 출력하고 종료합니다.

2. **1차 LLM JSON 규격 준수 (`cli_pipeline.py`)**
   * 필수 스키마 키: `recommended_city`, `weather`, `events` (배열 타입), `reason`
   * LLM 응답을 자동 파싱하고 `events`가 배열(list) 타입임을 확실하게 보장합니다.

3. **맛집 좌표(`x`, `y`) 데이터 확보 (`cli_pipeline.py` & `gui.py`)**
   * 카카오 지도 REST API 키워드 검색을 통해 추천 맛집의 상세 정보뿐만 아니라 **위도/경도 좌표 (`x`, `y`)**를 100% 확보하여 JSON에 저장합니다.

4. **API 쿼터 안전 모델 폴백 (Model Fallback System)**
   * Free Tier 429 `RESOURCE_EXHAUSTED` 오류 방지를 위해, 경량/낮은 버전 엔진(`gemini-flash-lite-latest` 등)부터 고성능 버전까지 순차적으로 호출합니다.

5. **결과물 자동 파일 저장 (`results/` 폴더)**
   * CLI 실행 시 타임스탬프 기반 원본 데이터(`travel_data_YYYYMMDD_HHMMSS.json`)와 최종 마크다운 일정표(`travel_itinerary_YYYYMMDD_HHMMSS.md`)를 자동 저장합니다.

---

## 🔑 3. API 키 설정 방법 (API Key Setup)

프로젝트 루트 디렉터리에 `.env` 파일을 생성하고 아래와 같이 API 키를 입력합니다.

```env
# Google Gemini LLM API Key (둘 중 하나 등록)
GEMINI_API_KEY=your_gemini_api_key_here
LLM_API_KEY=your_gemini_api_key_here

# Kakao Local REST API Key
KAKAO_REST_API_KEY=your_kakao_rest_api_key_here
```

> **💡 GUI에서 설정하기**:  
> GUI 실행 후 좌측 **"🔑 1. API 키 설정 (.env)"** 입력란에 API 키를 입력하고 **`💾 API 키 .env에 저장`** 버튼을 누르면 `.env` 파일이 자동으로 업데이트됩니다.

---

## 💻 4. 실행 방법 (Execution Guide)

### 4.1 의존성 패키지 설치

```bash
pip install PyQt5 google-genai edge-tts python-dotenv
```

### 4.2 CLI 모드 실행 (날짜 지정 파이프라인)

`-date` 인자에 `YYYY-MM-DD` 형식으로 여행 출발일을 입력합니다.

```bash
# 정상 실행 예시
python main.py -date 2026-08-15

# 날짜 형식이 잘못된 경우 (오류 출력 및 안내 메시지 표시)
python main.py -date 2026-13-45
```

### 4.3 GUI 모드 실행

인자를 생략하거나 `--gui` 플래그를 붙여 실행하면 PyQt5 GUI 환경이 켜집니다.

```bash
python main.py
# 또는
python main.py --gui
```

---

## 📁 5. 결과물 확인 방법 (Output Verification)

### CLI 실행 결과물 (`results/` 폴더)
CLI 실행 완료 시 `results/` 폴더에 아래 2개의 파일이 생성됩니다.

1. **`travel_data_YYYYMMDD_HHMMSS.json`**:
   ```json
   {
       "recommended_city": "서울 종로구",
       "weather": "최고 32℃, 최저 24℃, 구름 조금",
       "events": [
           "서울 문화의 밤 야간개장",
           "경복궁 별빛야행"
       ],
       "reason": "서울 종로구는 조선 시대의 궁궐과 전통 한옥이 밀집해 있어...",
       "restaurants": [
           {
               "name": "산모퉁이",
               "category": "음식점 > 카페",
               "address": "서울 종로구 백석동길 153",
               "url": "http://place.map.kakao.com/7912367",
               "x": "126.967923107901",
               "y": "37.5954247204695"
           }
       ]
   }
   ```
2. **`travel_itinerary_YYYYMMDD_HHMMSS.md`**:  
   마크다운 형태의 완성된 일자별/시간대별 종합 여행 일정표

### GUI 실행 결과물
* 우측 상단 **여행 일정표 뷰어**에서 마크다운 리포트 확인
* **`💾 마크다운 파일 저장 (.md)`** 버튼을 통한 로컬 저장
* **`🔊 음성으로 설명`** 버튼 클릭 시 `Narration_Sound/` 폴더에 MP3 파일이 생성되며 자동 음성 설명 재생

---

## ⚠️ 6. API 키 유출 주의 사항 (Security Notice)

1. **`.env` 파일 Git 추적 제외**:
   * API 키가 포함된 `.env` 파일은 절대로 GitHub 등 공공/공유 저장소에 업로드(Push)하면 안 됩니다.
   * `.gitignore` 파일에 `.env` 및 `results/` 항목이 등록되어 있는지 반드시 확인하세요.
2. **키 유출 시 대처 방안**:
   * 실수로 API 키가 누락/공개된 경우, [Google AI Studio](https://aistudio.google.com/) 및 [Kakao Developers Console](https://developers.kakao.com/)에서 기존 키를 즉시 삭제/폐기하고 새 키를 재발급받으세요.
