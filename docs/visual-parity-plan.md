# Visual Parity Plan: the-global-voice vs apostolicfaith.org

> 작성일: 2026-04-01 | 검토자: Teddy
> 상세 실행 태스크 포함

---

## 현재 상태

**구현 완료 페이지 (9개):** home, our-faith, portland, contact, subscriptions, daily-devotional (list+detail), daybreak-devotional (list+detail)

**확보된 이미지 (25개):** logo 4, hero 5, icons 9, magazine 6, 교리 이미지 0

**원본에만 있는 페이지 (12개):** events, camp-meeting, live-webcast, video-archive, podcasts, world-report, church-map, magazine-archive, library-resources, minister-resources, tithings-offerings, app

---

## 실행 태스크 (삽질 없는 순서)

### Sprint 1: 이미지 확보 + 히어로 개선 (공수: 2-3시간)

이게 가장 임팩트 크고 빨리 끝남.

**Task 1-1. 교리 이미지 17개 다운로드**
- 소스: apostolicfaith.org/our-faith CDN (webp)
- 이미 확인된 이미지들:
  - `the-divine-trinity.webp`
  - `repentace.webp` (원본 오타 그대로)
  - `Salvation.webp`
  - `Sanctification.webp`
  - `Divine Healing Summary.webp`
  - `The Second Coming of Jesus.webp`
  - `The Great Tribulation.webp`
  - `Christ's Millennial Reign.webp`
  - `The Great White Throne Judgment.webp`
  - `The New Heaven and the New Earth.webp`
- 나머지 7개 (Baptism of Holy Ghost, Marriage, Water Baptism, Restitution, Lord's Supper, Foot Washing, Eternal Heaven/Hell) — /our-faith 하단에서 추가 스크레이핑 필요
- 저장: `public/images/doctrines/`
- 작업: `curl -o` 로 다운로드, webp→jpg 변환은 불필요 (Next.js Image 컴포넌트 지원)

**Task 1-2. 페이지별 히어로 배경 이미지 적용**
- 대상: our-faith, portland, contact, home
- 이미 있는 이미지: `hero/og-image.jpg`, `hero/contact-hero.jpg`, etc.
- 추가 필요: portland 히어로 (교회 건물 사진 — `2021.04726-min-edited.jpg` from CDN)
- 구현: 각 페이지 히어로 섹션에 `style={{ backgroundImage }}` + gradient overlay
- 파일 수정: `our-faith/page.tsx`, `portland/page.tsx`, `contact/page.tsx`, homepage hero component

**Task 1-3. 교리 캐러셀에 이미지 추가**
- `doctrine-carousel.tsx` 수정
- 각 교리 카드에 배경 이미지 또는 썸네일 추가
- 데이터: locale JSON의 doctrines 배열에 `image` 필드 추가 (파일명만, 경로는 코드에서)
- UI: 카드 상단에 이미지, 하단에 텍스트 (원본 사이트 스타일)

### Sprint 2: 네비게이션 + 레이아웃 개선 (공수: 2-3시간)

**Task 2-1. 메가메뉴 스타일 드롭다운**
- 현재: 기본 세로 드롭다운
- 변경: 2-3열 그리드 레이아웃, 아이콘 포함
- 파일: `public-header.tsx`, `nav-data.ts`
- 각 메뉴 그룹에 description 추가 (로케일 키 필요)
- 활성 페이지 하이라이트 (usePathname으로 현재 경로 매칭)

**Task 2-2. 모바일 네비게이션 아코디언 서브메뉴**
- 현재: 플랫 리스트
- 변경: 그룹별 접기/펼치기 (Our Churches, Library Resources, Accounts & Services)
- 파일: `public-header.tsx` 모바일 메뉴 섹션

**Task 2-3. Footer 보강**
- 뉴스레터 이메일 입력 CTA (UI만, 백엔드 없이)
- 앱 다운로드 배지 (App Store / Google Play 이미지 링크)
- 잡지 최근 표지 이미지 (이미 `magazine/` 에 있음)
- 파일: `public-footer.tsx`

### Sprint 3: 누락 페이지 추가 (공수: 3-4시간)

우선순위 A만. 외부 링크로 충분한 건 건너뜀.

**Task 3-1. `/live-webcast` 페이지**
- YouTube iframe 임베드 (채널 URL)
- 예배 스케줄 표시 (portland 페이지 재사용)
- "현재 방송 중" 상태 표시 (선택)
- 파일: `src/app/(public)/[locale]/live-webcast/page.tsx` 신규

**Task 3-2. `/camp-meeting` 페이지**
- 원본 사이트에서 curl로 콘텐츠 스크레이핑
- 히어로 + 일정 + 위치 정보
- 사진 2-3장 (CDN에서 다운로드)
- 5개 언어 번역
- 파일: `src/app/(public)/[locale]/camp-meeting/page.tsx` 신규

**Task 3-3. `/calendar` 페이지 (Google Calendar 임베드)**
- Google Calendar 공개 URL iframe
- 또는: Google Calendar API로 다가오는 이벤트 5-10개 카드 표시
- 최소 구현: iframe 한 줄
- 파일: `src/app/(public)/[locale]/calendar/page.tsx` 신규

**Task 3-4. `/world-report` 페이지**
- 원본 사이트에서 최근 기사 3-5개 타이틀 + 썸네일 스크레이핑
- 카드 그리드 레이아웃
- 각 카드 클릭 시 apostolicfaith.org 원문 링크
- 파일: `src/app/(public)/[locale]/world-report/page.tsx` 신규

**Task 3-5. `/video-archive` 페이지**
- YouTube playlist 임베드 또는 비디오 카드 그리드
- 원본 사이트에서 비디오 목록 스크레이핑
- 파일: `src/app/(public)/[locale]/video-archive/page.tsx` 신규

### Sprint 4: 마무리 + 품질 (공수: 1-2시간)

**Task 4-1. 네비게이션 링크 내부화**
- 현재 외부 링크인 것 중 Sprint 3에서 만든 페이지들을 내부 링크로 변경
- `nav-data.ts` 수정: live-webcast, camp-meeting, calendar, world-report, video-archive

**Task 4-2. SEO 메타데이터**
- 각 신규 페이지에 generateMetadata 함수 추가
- OG image 설정 (히어로 이미지 재사용)

**Task 4-3. 전체 빌드 + 번역 검증**
- `pnpm build` 통과 확인
- 5개 언어 전체 로케일 키 누락 없는지 검증
- 브라우저에서 각 페이지 / 각 언어 스팟 체크

---

## 봇 분업 (권장)

| Sprint | 담당 | 검토 |
|--------|------|------|
| Sprint 1 (이미지 + 히어로) | 1명 (이미지 다운로드 → 코드 수정 순차) | 나머지 2명 |
| Sprint 2 (네비게이션) | 1명 | 나머지 2명 |
| Sprint 3 (신규 페이지) | 병렬 가능 — 페이지별 분배 | 상호 검토 |
| Sprint 4 (마무리) | 전원 | - |

---

## 예상 총 공수: 8-12시간 (봇 3대 병렬 시 3-4시간)

Sprint 1+2가 look & feel 핵심. Sprint 3는 콘텐츠 확장. Sprint 4는 마무리.
