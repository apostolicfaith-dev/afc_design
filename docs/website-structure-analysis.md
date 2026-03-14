# apostolicfaith.org — Website Structure Analysis

> Analyzed: 2026-03-14
> Platform: Webflow CMS
> Domain: https://apostolicfaith.org/

## Site Overview

The Apostolic Faith Church (HQ Portland, OR) website serves as the primary digital presence for the denomination. It covers church information, devotional content, educational resources, media, event management, and global reporting across 60+ countries and 3000+ churches.

---

## Navigation Structure

### Top Bar
- Home `/`
- Daybreak Devotional `/daybreak-devotional`
- Daily Devotional `/daily-devotional`
- Live Webcast `/live-webcast`
- Contact Us `/contact`
- Minister Log In `/minister-resources`

### Main Nav: Our Churches
- Our Faith `/our-faith`
- Map of Locations `/our-churches-map-of-locations`
- Portland Church `/portland`
- Camp Meeting `/camp-meeting`
- Calendar `/calendar`
- World Report `/world-report`
- Media Center `/video-archive`

### Main Nav: Library Resources
- Apostolic Faith Magazine `/apostolic-faith-magazine`
- Curriculum `/library/curriculum`
- Daily Devotional `/daily-devotional`
- Daybreak Devotional `/daybreak-devotional`
- Doctrinal Resources `/library/doctrinal-resources`
- Foreign Languages `/library/foreign-languages`
- Historical Materials `/library/historical-materials`
- Music Resources `/music-resources`
- This Week's Lessons `/library/this-weeks-lessons`

### Main Nav: Accounts & Services
- Ministers `/minister-resources`
- Rentals `/rentals`
- Safety `/safety`
- Tithes & Offerings `/tithings-offerings`
- Subscriptions `/subscriptions-hq`
- Download App `/app`

### Footer
- Quick links: Library, World Report, Magazine, Live Webcast, Daybreak Devotional
- Social: Instagram, Facebook, Vimeo, YouTube
- Newsletter signup (email)

---

## Content Sections — Detail

### 1. Devotionals

**Daily Devotional** `/daily-devotional`
- Short scripture passage + reflective commentary
- Audio player available (TTS or recorded)
- Archive back to November 2022
- Supplementary study materials: Bible Book, Introduction, Outline, Maps, Timeline

**Daybreak Devotional** `/daybreak-devotional`
- More in-depth than Daily Devotional
- Sections: Overview, Background, Amplified Outline, Closer Look questions, Conclusion
- Audio player available
- Archive at `/library/daybreak-devotional-archive`

### 2. Magazine (The Apostolic Faith)

- Quarterly publication, since 1906
- Current: Volume 119-1 (Jan–Mar 2026)
- Sections: Viewpoint, From the Word, Witness, World Report, Our Classics
- URL pattern: `/the-apostolic-faith/[article-slug]`
- Volume URLs: `/magazine-volumes/volume-[num]-[quarter]-[year]`
- Archive: `/library/the-apostolic-faith-archive`
- Subscription: `/subscriptions-pdx`

### 3. Curriculum (Sunday School & Bible Study)

- **Daybreak & Discovery** — 3-year chronological Bible study (daily personal + weekly group)
- **Sunday School Series** by age group:
  - Primary Pals (ages 6–8)
  - Answer (ages 9–13)
  - Search (ages 14+)
  - Student and teacher materials for each
- **Bible Studies** (topical, multi-week):
  - Discipleship, Heritage, Living Holy, Quest, Revival, Strength for the Inner Man
- Current week's lessons at `/library/this-weeks-lessons`

### 4. Foreign Languages

**9 languages supported:**
1. Arabic (العربية)
2. Spanish (Español)
3. French (Français)
4. Indian languages (Nepali, Tamil, Telugu, Hindi, Malayalam)
5. Italian (Italiano)
6. Portuguese (Português)
7. Russian (Русский)
8. Romanian (Română)
9. Vietnamese (Tiếng Việt)

Content types per language: articles, doctrinal materials, curriculum, magazine content, testimonies, sermons.
Note: The foreign languages section was marked "under construction" for some content.

### 5. World Report

- News from global AFC churches
- Organized by region:
  - Africa
  - Asia & Australia
  - Europe
  - North America
  - South America & the Caribbean
- Each report: headline, date, country tag, optional PDF attachment
- Covers: camp meetings, dedications, ordinations, humanitarian efforts, milestones

### 6. Media

**Video Archive** `/video-archive`
- Hosted on Vimeo (`@apostolicfaith`)
- Uses Plyr video player
- Includes sermons, curriculum videos, webcast recordings

**Podcasts** `/podcasts`
- Sermons, magazine articles, daily devotional in audio
- Distributed on external platforms (not specified which)

**Live Webcast** `/live-webcast`
- Sunday morning (11am), Sunday evening (6pm), Tue & Fri (7:30pm)

### 7. Camp Meeting

- Annual event: June 28 – July 12 (2026)
- Sections: Welcome, Registration, Schedule, Media Center, Photo Gallery, Christmas on the Campground
- Practical info: Dining ($9–14/meal), Music programs, Guardian policies, RV/cabin info
- Registration system available

### 8. Portland Church

- Location: 5601 SE Duke Street, Portland, OR
- Pastor: Rev. Dave Lambert
- Services: Sun 9:30am (SS), 11am (morning), 6pm (evening); Tue & Fri 7:30pm
- Ministries: Music, Youth, Outreach
- Facilities rental (currently unavailable)

### 9. Accounts & Services

**Minister Resources** `/minister-resources`
- Login via email verification (confirmation code) or Google SSO
- Minister Dashboard (gated content)
- New minister application: requires name, email, role, church location, pastor name
- Approval takes up to 24 hours

**Subscriptions** `/subscriptions-hq`
- Email-based subscriptions:
  - Daily devotional
  - Global efforts / World Report updates
  - Portland newsletters and event updates

**Tithes & Offerings** `/tithings-offerings`
- Online giving (details not crawled)

**App** `/app`
- Study materials, media, current events
- Platform details not specified on website

---

## Key Observations

### Strengths
- Comprehensive content library (devotionals, curriculum, magazine, reports)
- Multilingual support (9 languages)
- Audio available for devotionals
- Minister-specific gated resources
- Camp meeting registration system

### Limitations & Opportunities
1. **Manual CMS management** — Webflow requires manual page creation for each article/devotional/report
2. **Translation workflow** — 9 languages with articles, devotionals, curriculum = significant manual effort; no apparent automated translation pipeline
3. **Audio production** — TTS/recorded audio for devotionals exists but unclear how scalable across languages
4. **No sitemap.xml** — SEO and discoverability limitation
5. **App details sparse** — Mobile app exists but platform/feature info unclear on website
6. **Foreign languages "under construction"** — Multilingual content appears incomplete
7. **Subscription limited to email** — No push notifications, no in-app delivery
8. **Event management** — Camp meeting registration exists, but no visible system for local church events globally
9. **No apparent API layer** — Content is Webflow-managed, not accessible via API for other services
10. **Social media fragmented** — Presence on Instagram, Facebook, Vimeo, YouTube but unclear content strategy across platforms
