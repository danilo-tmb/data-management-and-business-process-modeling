# Sports Team Management System — Database Design & Analytics

**Course:** OPIM-5272 · Data Management and Business Process Modeling  
**Program:** MS Business Analytics, University of Connecticut  
**Team:** Damir Subkhankulov · Danilo Tambone · Vitosavero Avila Wibisono
**Database Engine:**  DBeaver

---

## Overview

A fully normalized relational database engineered to serve as a centralized,
longitudinal data repository for professional and collegiate sports organizations.
The system unifies four operational domains — player development, medical intelligence,
facility management, and competitive performance — into a single queryable analytics engine.

The architecture captures digital twin workload scores derived from wearable sensor data
(GPS telemetry, heart-rate variability, session RPE) and cross-references them with
injury records, coaching assignments, game rosters, and facility attributes —
enabling sports scientists to transition from reactive to **predictive** operational models.

---

## Schema

**13 tables · 12 FK constraints · 2 associative entities · 1 EEC hierarchy**

| Table | Category | Description |
|---|---|---|
| `team` | Core | Organization master record |
| `player` | Core | Athlete profiles with derived `[Age]` from DOB |
| `staff` | Supertype | Personnel with `Role_Type` discriminator |
| `coach` | Subtype of STAFF | License level and specialty |
| `medical_staff` | Subtype of STAFF | Medical specialization (UNIQUE: License_No) |
| `season` | Reference | Season date ranges |
| `facility` | Reference | Venues with surface type (Grass / Turf / Synthetic) |
| `game` | Transactional | Match records linked to season and facility |
| `game_roster` | Bridge (M:N) | Resolves PLAYER ↔ GAME; stores minutes played |
| `training_session` | Transactional | Sessions linked to season and facility |
| `session_performance` | Bridge (M:N) | Resolves PLAYER ↔ TRAINING_SESSION; stores workload score |
| `equipment` | Dependent | Player-assigned gear with inventory status |
| `injury_record` | Dependent | Longitudinal medical event tracking |

The `STAFF` entity implements a **disjoint supertype–subtype (EEC) hierarchy**,
routing records to `COACH` or `MEDICAL_STAFF` via the `Role_Type` discriminator
without redundant JOINs. Both many-to-many relationships (`GAME ↔ PLAYER`,
`TRAINING_SESSION ↔ PLAYER`) are resolved through dedicated associative entities
with their own primary keys.

---

## Analytical Reports

Five SQL reports were designed to demonstrate the database's capacity for
data-driven decision-making across sports science, personnel management,
and facility operations. Full queries, output screenshots, and business
value documentation are in [`Project_Deliverable_6.docx`](./Project_Deliverable_6.docx).

| # | Report | Business Question Answered |
|---|---|---|
| 1 | **Predictive Injury Risk Profiling** | Which players are at high injury risk based on digital twin workload scores? Classifies each athlete into High Risk / Optimal Load / Review Conditioning using ACWR-aligned thresholds. |
| 2 | **Medical Staff Specialization Audit** | Are we adequately staffed across all medical disciplines? Surfaces headcount per specialization and flags single-point-of-failure gaps. |
| 3 | **Player Surface Exposure Analysis** | How much playing time does each athlete accumulate on Turf vs. Grass vs. Synthetic surfaces? Produces exposure-adjusted data for injury incidence modeling and schedule optimization. |
| 4 | **Coach Session Load Analysis** | Are coaches hitting the right training intensity? Compares average player workload per coach to identify over- and under-loading patterns. |
| 5 | **Player Performance Trends by Season** | Are players improving season over season? Tracks total minutes, games played, and average training workload per player per season to support contract and roster decisions. |

## Project Deliverables

| Sprint | Deliverable | Description |
|---|---|---|
| 1 | Requirements & Scope | Business objectives, key reports, entity identification |
| 2 | Logical ER Model | Entity relationships, cardinalities, sample data |
| 3 | Physical ER Model | Full schema with data types, PKs, FKs, EEC hierarchy |
| 4 | DDL Script | `CREATE TABLE`, `ALTER TABLE`, constraints, testing evidence |
| 5 | Data Population | 30 records per table via `INSERT` scripts |
| 6 | Analytical Reports | 5 SQL reports with business value documentation |

---

## Repository Structure

```
football-management-db/
├── deliverables/
│   ├── Project_Deliverable_1.docx   ← Requirements & scope
│   ├── Project_Deliverable_2.docx   ← Logical ER Model
│   ├── Project_Deliverable_3.docx   ← Physical ER Model
│   ├── Project_Deliverable_4.docx   ← DDL script & constraints
│   ├── Project_Deliverable_5.docx   ← Data population
│   └── Project_Deliverable_6.docx   ← Analytical reports & SQL queries
└── Sports_Team_DB_Final_Presentation.pptx

```

## What I Learned

Designing this system end-to-end — from business requirements through logical and physical
modeling to live query execution — reinforced how schema decisions made at the ERD stage
have direct consequences on query complexity and analytical capability downstream.
The EEC hierarchy for `STAFF` and the decision to store `workload_score` at the
session-performance level (rather than as a player aggregate) were the two choices
that most shaped the expressiveness of the final reports.
