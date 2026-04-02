# Visual Parity Plan: the-global-voice vs apostolicfaith.org

> 작성일: 2026-04-01 | 검토자: Teddy

## 현재 상태 요약

**구현 완료 (9개 라우트):**
- `/` 홈페이지 (hero, devotional section, quick links)
- `/our-faith` (hero, who we are, pray/preach/publish, 17-doctrine carousel, leadership)
- `/portland` (welcome, about, services, pastor bio, facilities)
- `/contact` (HQ info, phone, form link)
- `/subscriptions` (magazine, daybreak, daily devotional cards)
- `/daily-devotional` (리스트 + `/[slug]` 상세)
- `/daybreak-devotional` (리스트 + `/[slug]` 상세)

**원본 apostolicfaith.org에는 있지만 우리에게 없는 페이지:**
- `/events` (이벤트 목록)
- `/camp-meeting` (캠프 미팅 상세)
- `/live-webcast` (실시간 방송 페이지)
- `/video-archive` (비디오 아카이브)
- `/podcasts` (팟캐스트)
- `/world-report` (세계 보고 + 기사들)
- `/our-churches-map-of-locations` (교회 지도)
- `/apostolic-faith-magazine` (잡지 아카이브)
- `/library-resources` (도서관 허브 — curriculum, doctrinal, historical, foreign languages, music)
- `/minister-resources` (목회자 리소스)
- `/tithings-offerings` (십일조/헌금)
- `/app` (모바일 앱 안내)

---

## Phase 1: Look & Feel 개선 (공수: 중)

### 1-A. 교리 카드 이미지 + 네비게이션

**현재:** 텍스트만 있는 캐러셀 (prev/next/dot 네비게이션)
**원본:** 각 교리에 아이콘/이미지, 좌우 스크롤, 숫자 번호 표시
**계획:**
- 교리별 대표 아이콘 (SVG) 또는 심볼 이미지 추가
- 번호 뱃지 (1-17) 시각적으로 강조
- 그리드 뷰 옵션 추가 (모바일: 캐러셀, 데스크탑: 3열 그리드)
- 교리 클릭 시 expand/collapse (아코디언) 고려

### 1-B. 히어로 섹션 이미지

**현재:** 단색 dark 배경 + 텍스트
**원본:** 풀 배경 이미지 + 오버레이 + 텍스트
**계획:**
- 각 페이지별 히어로 배경 이미지 (이미 /public/images/hero/ 에 5개 있음)
- CSS background-image + gradient overlay
- 대상 페이지: home, our-faith, portland, contact

### 1-C. 네비게이션 개선

**현재:** 기본 드롭다운 메뉴 (3 그룹)
**원본:** 풀 메가메뉴, 호버 시 부드러운 드롭다운
**계획:**
- 메가메뉴 스타일 적용 (2-3열 레이아웃)
- 활성 페이지 하이라이트
- 모바일: 아코디언 서브메뉴

### 1-D. Footer 보강

**현재:** 기본 링크 + social icons
**원본:** 뉴스레터 구독, 빠른 링크 그리드, 앱 다운로드 배너
**계획:**
- 이메일 구독 CTA 추가
- 앱 다운로드 링크 (App Store / Google Play)
- 최근 잡지 표지 이미지

---

## Phase 2: 누락 페이지 마이그레이션 (공수: 대)

### 우선순위 A (자체 콘텐츠 가능)

| 페이지 | 설명 | 구현 방식 |
|--------|------|-----------|
| `/live-webcast` | 실시간 방송 임베드 | YouTube/Vimeo iframe + 스케줄 표시 |
| `/video-archive` | 비디오 아카이브 | 외부 링크 카드 or YouTube playlist 임베드 |
| `/camp-meeting` | 캠프 미팅 안내 | 정적 콘텐츠 + 달력 링크 |

### 우선순위 B (외부 링크로 충분)

| 페이지 | 현재 | 계획 |
|--------|------|------|
| `/events` | 없음 | Google Calendar 임베드 or apostolicfaith.org/calendar 링크 |
| `/world-report` | 없음 | apostolicfaith.org/world-report 링크 or 요약 카드 |
| `/podcasts` | 없음 | 팟캐스트 플랫폼 링크 카드 |

### 우선순위 C (장기)

- `/our-churches-map-of-locations` — Google Maps API 필요
- `/apostolic-faith-magazine` — 잡지 아카이브 전체 마이그레이션
- `/library-resources` — 도서관 허브 (하위 페이지 다수)

---

## Phase 3: 이미지 리소스 확보 (공수: 소-중)

### 이미 확보된 이미지 (25개)
- `logo/` — AFC 로고 4종
- `hero/` — 히어로 이미지 5종
- `icons/` — SVG 아이콘 8종
- `magazine/` — 잡지 커버 5종

### 추가 필요 이미지

| 카테고리 | 내용 | 소스 |
|----------|------|------|
| 교리 아이콘 | 17개 교리별 대표 아이콘/심볼 | 직접 제작 (SVG) or stock |
| 페이지 히어로 | portland, contact, subscriptions 전용 배경 | apostolicfaith.org CDN |
| 목사 프로필 | Rev. Dave Lambert 사진 | apostolicfaith.org/portland |
| 교회 사진 | 건물 외관, 내부, 시설 | apostolicfaith.org |
| 캠프 미팅 | 행사 사진 2-3장 | apostolicfaith.org |
| 총감독 | Sola Adesope 사진 | apostolicfaith.org/our-faith |

---

## Phase 4: Google Calendar 통합 (공수: 소)

**현재:** 네비게이션에 외부 링크 (`apostolicfaith.org/calendar`)
**계획:**
- Google Calendar 공개 캘린더 iframe 임베드
- 또는 Google Calendar API로 이벤트 목록 fetch → 카드 표시
- 최소 구현: iframe 임베드 페이지 하나

---

## 실행 순서 권장

```
Phase 1-B (히어로 이미지)     ← 즉시 적용 가능, 임팩트 큼
Phase 1-A (교리 카드 개선)    ← look & feel 핵심
Phase 3   (이미지 확보)       ← 1-A, 1-B 전제 조건
Phase 1-C (네비게이션)        ← UX 개선
Phase 2-A (webcast/video)    ← 콘텐츠 추가
Phase 1-D (Footer)           ← 마무리
Phase 4   (Calendar)         ← 선택 사항
Phase 2-B, 2-C               ← 장기 로드맵
```

---

## 기존 이미지 CDN 소스 (참고)

apostolicfaith.org 이미지는 Webflow CDN에서 호스팅:
```
https://cdn.prod.website-files.com/60f82ad12067d1d904335cc9/
```

데모용으로 직접 다운로드해서 `/public/images/`에 저장하면 됨.
