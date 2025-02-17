## Outreach Events Calendar Product Outline (v.1.0)

The VA.gov [Outreach Events calendar](https://www.va.gov/outreach-and-events/events/) was quickly built and released as an "at parity" MVP more product -- with very basic funcitonality -- more than <two> years ago.  Since that time, the popularity of the product has grown (to more than 200,000 page views on average per month [Google Analytics report](https://analytics.google.com/analytics/web/?authuser=1#/report/content-pages/a50123418w177519031p176188361/_u.date00=20190701&_u.date01=20210722&explorer-table.filter=outreach-and-events~2Fevents~2F&explorer-table.plotKeys=%5B%5D&explorer-graphOptions.selected=analytics.nthMonth)) and so have the needs and expectations of the VA business stakeholders.   Indeed, as of the spring of 2021, the Veterans Experience Office has broaded the [user base for the national events calendar to digital comms teams at VHA, VBA, OPIA and NCA](https://github.com/department-of-veterans-affairs/va.gov-team/blob/master/products/outreach-events/business-users.md).
  
For this initiative, we will implement a series of functional, UX and CMS-based authoring enhancements that will more fully mature the Outreach Hub events calendar product, making it more personalized and useful for VA.gov users and easier to curate-manage by VA business stakeholders.


## The Problem to be Solved

How might we make the [Outreach Hub events calendar](https://www.va.gov/outreach-and-events/events/) more personalized for users (e.g. find events by date, time, event type, etc.) and easier to manage/curate for VA business users.


## Proposed Solution(s)

**High Level User Story/ies**

- As a Veteran user, I need to quickly find VA events of most interest based on a variety of search goals: date, time, location, event type, etc.
- As a VACO digital comms content editor, I need to curate events (including create, modify, duplicate, categorize for filtering) in the VA.gov CMS in the most efficient way possible.


## User Stories

**As a user:**
  
- I want to search for events by date (month, day) and time so that I know what's happening based on my schedule/availability. 
- I want to search for events by location so that I know what's happening in my area (e.g. at my VAMC).
- I want to search for events by type so that I know what's happening in-person vs. online/virtual
- I want to search for events by interest/need so that I know if there events of special interest to me (e.g. about VA benefits, about VA education, about VA Community Care etc.)
- I want to search for events based on my status (Veteran, beneficiary, care-giver, etc.) so that I can find events of special interest to me.

**As a national VACO digital comms content editor:**

- I want the capability to provide additional categorical data about events (e.g.,event category -- health, education, etc.) to help power "filtering" features for users.
- I want an [improved CMS authoring experience](https://github.com/department-of-veterans-affairs/va.gov-cms/issues/1605) so that I can be more efficient when updating/managing the events hub.
  - I want the capability for [auto-archiving past events](https://github.com/department-of-veterans-affairs/va.gov-cms/issues/4214) so that the event hub does not display old, non-relevant content.
  - I want the capability to edit/resize images more easily so that I can use photos more confidently and efficiently.
  - I want the removal of the "Cost" field for events as all VA events are no-cost.
- I (may) want the ability to review and approve local events generated by the field (VAMCs) so that those events can be featured on the national event calendar and I don't have to duplicate-create them.  

  
**As a local VAMC/Vet Center content editor:**
  
- I want < > so that I can < >.


**As the Outreach Events product owner:**

- I want an intuitive UX which allows users to narrow their search for events in an intutive way -- on desktop and mobile -- so their experience is efficient and more personalizable.  (Note: Is this a map interface, for example?)
  

## What Needs to Be Done

**In Short-term (by Aug. 6)**

- [ ] Review this outline with Dave/Kev for "fleshing out"
- [ ] Identify next iteration of product enhancement work -- CMS AX experience, front-end user experience (e.g. how will filtering work from a user/UX perspective?)
  

*Take into consideration Accessibility/QA needs as well as Product, Technical, and Design requirements.*

## Relevant Links

  - [Events Hub Super Epic from March 2021](https://github.com/department-of-veterans-affairs/va.gov-team/issues/22241)

## Additional Background

  - VEO, the business "owner" of Outreach and Events Hub, has extended content edit capability and rights to leads in other VACO digital comms teams [as outlined here.](https://github.com/department-of-veterans-affairs/va.gov-team/blob/master/products/outreach-events/business-users.md)

## Screenshots of Representative Events Calendars
  
**AARP**
  
  <img width="969" alt="Screen Shot 2021-07-28 at 12 50 18 PM" src="https://user-images.githubusercontent.com/63107147/127363797-d39057d4-171a-42d8-977e-4a2787d44384.png">

**TheEventsCalendar.com (WordPress Plug In)** - https://demo.theeventscalendar.com/
  
<img width="859" alt="Screen Shot 2021-07-28 at 12 54 39 PM" src="https://user-images.githubusercontent.com/63107147/127364295-652dcc77-e239-4ed8-8535-6f6b037b9cc6.png">


## Status 


*Take into consideration Accessibility/QA needs as well as Product, Technical, and Design requirements.*

## How to configure this issue
- [ ] **Labeled with Team** (`product support`, `analytics-insights`, `operations`, `tools-be`, `tools-fe`,`content-ia`, `service-design`, `vsa-*`)
- [ ] **Labeled with Practice Area** (`backend`, `frontend`, `devops`, `design`, `research`, `product`, `ia`, `qa`, `analytics`, `contact center`, `research`, `accessibility`, `content`)
