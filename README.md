# ClasseViva Web API - Reverse Engineering Docs

> Unofficial, community-maintained documentation of the ClasseViva (Spaggiari) REST API used by the web client at `web.spaggiari.eu`.

**Disclaimer**: This documentation is the result of independent network analysis. It is not affiliated with, endorsed by, or supported by Spaggiari. Use at your own risk and in compliance with applicable terms of service.

---

## Table of Contents

- [Authentication](#authentication)
- [Endpoints](#endpoints)
  - [User Info](#user-info)
  - [Daily Overview](#daily-overview)
  - [Student Card](#student-card)
  - [Subjects](#subjects)
  - [Grades](#grades)
  - [Average](#average)
  - [Absences](#absences)
  - [Agenda](#agenda)
  - [Lessons](#lessons)
  - [Noticeboard](#noticeboard)
  - [Didactics (Materials)](#didactics-materials)
  - [Homeworks](#homeworks)
  - [Notes](#notes)
  - [Schoolbooks](#schoolbooks)
  - [Ministerial Communications](#ministerial-communications)
- [Data Types Reference](#data-types-reference)
- [Contributing](#contributing)

---

## Base URLs

| Service | Base URL |
|---|---|
| REST API | `https://web.spaggiari.eu/rest/w1/` |
| Document CDN | `https://cdndoc.spaggiari.eu/` |
| Auth | `https://web.spaggiari.eu/auth-p7/app/default/AuthApi4.php` |

---

## Authentication

### Login

```
POST https://web.spaggiari.eu/auth-p7/app/default/AuthApi4.php?a=aLoginPwd
```

**Form fields** (application/x-www-form-urlencoded):

| Field | Description |
|---|---|
| `uid` | Username (student code, e.g. `S000000`) |
| `pwd` | Password |
| `cid` | School code |
| `pin` | PIN (if set) |
| `target` | `"genitori"` or `"studenti"` |

On success, the server sets the following cookies:

| Cookie | Domain | Description |
|---|---|---|
| `PHPSESSID` | `.spaggiari.eu` | Session token (session-scoped) |
| `webidentity` | `web.spaggiari.eu` | Student identity code (e.g. `S000000`) |
| `webrole` | `web.spaggiari.eu` | Role identifier (e.g. `gen`) |
| `i18n_redirected` | `web.spaggiari.eu` | Locale (`it`) |

All subsequent API requests must include these cookies.

---

## Endpoints

All endpoints use **GET** unless otherwise noted, and require the session cookies described above.

### User Info

```
GET /rest/w1/misc/whoami
```

Returns basic info about the currently authenticated user.

**Response:**

```json
{
  "id": "13000000",
  "account_type": "S",
  "sede_codice": "PG00001",
  "anno_scol": "2025/26",
  "cognome": "ROSSI",
  "nome": "MARIO",
  "classe_ident": "5A",
  "classe_desc": "5A LICEO SCIENTIFICO",
  "data_nascita": "2010-03-15",
  "codice_fisc": "RSSMRA10C15...",
  "login_type": null,
  "last_login_at": null,
  "email": "mario.rossi@example.com",
  "schoolpass": "PASS"
}
```

> `id` is the numeric student ID used in all other `/students/{id}/...` endpoints.

---

### Daily Overview

```
GET /rest/w1/students/{studentId}/overview/all26/{dateFrom}/{dateTo}
```

Returns lessons, homework agenda, and notes for a given day.

**Response:**

```json
{
  "virtualClassesAgenda": [],
  "lessons": [
    {"evtId": 1, "evtDate": "2026-09-15", "evtCode": "LSF0", "evtHPos": 1, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente A", "subjectId": 2, "subjectCode": null, "subjectDesc": "EDUCAZIONE CIVICA", "lessonType": "Orientamento", "lessonArg": "Attività di orientamento su tematica proposta dalla scuola."},
    {"evtId": 2, "evtDate": "2026-09-15", "evtCode": "LSF0", "evtHPos": 2, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente A", "subjectId": 4, "subjectCode": "LAT", "subjectDesc": "LINGUA E CULTURA LATINA", "lessonType": "Lezione", "lessonArg": "Ripasso argomenti grammaticali."},
    {"evtId": 3, "evtDate": "2026-09-15", "evtCode": "LSF0", "evtHPos": 3, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente B", "subjectId": 3, "subjectCode": "FIS", "subjectDesc": "FISICA", "lessonType": "Lezione", "lessonArg": "Introduzione al corso, ripasso argomenti."},
    {"evtId": 4, "evtDate": "2026-09-15", "evtCode": "LSF0", "evtHPos": 4, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente B", "subjectId": 3, "subjectCode": "FIS", "subjectDesc": "FISICA", "lessonType": "Lezione", "lessonArg": "Introduzione al corso, ripasso argomenti."},
    {"evtId": 5, "evtDate": "2026-09-15", "evtCode": "LSF0", "evtHPos": 5, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente C", "subjectId": 11, "subjectCode": "STG", "subjectDesc": "STORIA E GEOGRAFIA", "lessonType": "Lezione", "lessonArg": "Introduzione alla materia: concetti di base."},
    {"evtId": 6, "evtDate": "2026-09-15", "evtCode": "LSC0", "evtHPos": 2, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente D", "subjectId": 12, "subjectCode": "SOST", "subjectDesc": "SOSTEGNO", "lessonType": "Compresenza", "lessonArg": ""},
    {"evtId": 7, "evtDate": "2026-09-15", "evtCode": "LSC0", "evtHPos": 3, "evtDuration": 1, "classDesc": "5A LICEO SCIENTIFICO", "authorName": "Docente D", "subjectId": 12, "subjectCode": "SOST", "subjectDesc": "SOSTEGNO", "lessonType": "Compresenza", "lessonArg": "Introduzione al corso, ripasso argomenti."}
  ],
  "agenda": [
    {"evtId": 101, "evtCode": "AGHW", "evtDatetimeBegin": "2026-09-15T00:00:00+02:00", "evtDatetimeEnd": "2026-09-15T23:59:59+02:00", "isFullDay": true, "notes": "Docente A - Compiti inseriti in Didattica", "authorName": "Docente A", "classDesc": "5A LICEO SCIENTIFICO", "subjectId": 6, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "homeworkId": 3},
    {"evtId": 102, "evtCode": "AGHW", "evtDatetimeBegin": "2026-09-15T00:00:00+02:00", "evtDatetimeEnd": "2026-09-15T23:59:59+02:00", "isFullDay": true, "notes": "Docente A - Compiti inseriti in Didattica", "authorName": "Docente A", "classDesc": "5A LICEO SCIENTIFICO", "subjectId": 6, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "homeworkId": 4},
    {"evtId": 103, "evtCode": "AGNT", "evtDatetimeBegin": "2026-09-16T00:00:00+02:00", "evtDatetimeEnd": "2026-09-16T23:59:59+02:00", "isFullDay": true, "notes": "Esercizi assegnati da svolgere per la lezione successiva.", "authorName": "Docente E", "classDesc": "5A LICEO SCIENTIFICO", "subjectId": null, "subjectDesc": null, "homeworkId": null},
    {"evtId": 104, "evtCode": "AGNT", "evtDatetimeBegin": "2026-09-16T00:00:00+02:00", "evtDatetimeEnd": "2026-09-16T23:59:59+02:00", "isFullDay": true, "notes": "Materiale da portare per la lezione.", "authorName": "Docente A", "classDesc": "5A LICEO SCIENTIFICO", "subjectId": null, "subjectDesc": null, "homeworkId": null}
  ],
  "events": [],
  "grades": [],
  "notes": {"NTTE": [], "NTCL": [], "NTWN": [], "NTST": []}
}
```

### Student Card

```
GET /rest/w1/students/{studentId}/card
```

Returns detailed student and school information.

**Response:**

```json
{
  "card": {
    "ident": "S000000",
    "usrType": "S",
    "usrId": 13000000,
    "miurSchoolCode": "PG000000",
    "miurDivisionCode": "PG000000",
    "firstName": "MARIO",
    "lastName": "ROSSI",
    "birthDate": "2010-03-15",
    "fiscalCode": "RSSMRA10C15...",
    "schCode": "PG000000",
    "schName": "ISTITUTO D'ISTRUZIONE SUPERIORE",
    "schDedication": "...",
    "schCity": "PERUGIA",
    "schProv": "PG"
  }
}
```

---

### Subjects

```
GET /rest/w1/students/{studentId}/subjects
```

Returns the list of subjects and their assigned teachers.

**Response:**

```json
{
  "subjects": [
    {
      "id": 210068,
      "description": "DISEGNO E STORIA DELL'ARTE",
      "order": 50,
      "teachers": [
        {
          "teacherId": "A0000000",
          "teacherName": "SURNAME NAME"
        }
      ]
    }
  ]
}
```

---

### Grades

```
GET /rest/w1/students/{studentId}/grades26 // maybe 27 next year
```

Returns all grades for the current school year.

**Response:**

```json
{
  "grades": [
    {
      "subjectId": 210068,
      "subjectCode": "ART",
      "subjectDesc": "DISEGNO E STORIA DELL'ARTE",
      "evtId": 651664,
      "evtCode": "GRV0",
      "evtDate": "2025-10-24",
      "decimalValue": 7.0,
      "displayValue": "7",
      "displaPos": 1,
      "notesForFamily": "...",
      "color": "green",
      "canceled": false,
      "underlined": false,
      "periodPos": 1,
      "periodDesc": "1° PERIODO",
      "periodLabel": "1° PERIODO",
      "componentPos": 3,
      "componentDesc": "Pratico",
      "weightFactor": 1,
      "noAverage": false,
      "teacherName": "SURNAME NAME",
      "evtPosition": "S1_3_1"
    }
  ]
}
```

**Notes:**
- `evtPosition` format: `S{periodPos}_{componentPos}_{index}`
- `color`: `"green"` (≥6), `"yellow"` (~5-6), `"red"` (<5) - approximately
- `periodPos`: 1 = first semester, 3 = second semester (observed values) (if you you use semester)
- `decimalValue` is the raw numeric grade; `displayValue` is the formatted string (e.g. `"8-"` = 7.75)

---

### Average

Returns global average and per-subject average.

```
GET /rest/w1/students/{studentId}/avg
```


**Response:**
```json
{
  "subjectAverages": {
    "1": {"subjectId": 1, "subjectCode": null, "subjectDesc": "DISEGNO E STORIA DELL'ARTE", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "2": {"subjectId": 2, "subjectCode": null, "subjectDesc": "EDUCAZIONE CIVICA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "3": {"subjectId": 3, "subjectCode": null, "subjectDesc": "FISICA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "4": {"subjectId": 4, "subjectCode": null, "subjectDesc": "LINGUA E CULTURA LATINA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "5": {"subjectId": 5, "subjectCode": null, "subjectDesc": "LINGUA E CULTURA STRANIERA INGLESE", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "6": {"subjectId": 6, "subjectCode": null, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "7": {"subjectId": 7, "subjectCode": null, "subjectDesc": "MATEMATICA E INFORMATICA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "8": {"subjectId": 8, "subjectCode": null, "subjectDesc": "RELIGIONE CATTOLICA/ATTIVITA' ALTERNATIVA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "9": {"subjectId": 9, "subjectCode": null, "subjectDesc": "SCIENZE MOTORIE E SPORTIVE", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "10": {"subjectId": 10, "subjectCode": null, "subjectDesc": "SCIENZE NATURALI (BIOLOGIA, CHIMICA, SCIENZE DELLA TERRA)", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "11": {"subjectId": 11, "subjectCode": null, "subjectDesc": "STORIA E GEOGRAFIA", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}},
    "12": {"subjectId": 12, "subjectCode": null, "subjectDesc": "SOSTEGNO", "periodAverages": [], "globalAverage": {"periodPos": 0, "periodDesc": "Generale", "periodLabel": "", "average": null, "color": "orange", "gradeCount": 0}}
  },
  "generalAverages": []
}
```

### Absences

```
GET /rest/w1/students/{studentId}/absences/details/
```

Returns all absence and delay events.

**Response:**

```json
{
  "events": [
    {
      "evtId": 543814,
      "evtCode": "ABA0",
      "evtDate": "2025-09-22",
      "evtHPos": null,
      "evtValue": null,
      "isJustified": true,
      "justifReasonCode": "B",
      "justifReasonDesc": "Motivi di famiglia",
      "hoursAbsence": [],
      "webJustifStatus": 1
    }
  ]
}
```

**Event codes:**

| Code | Description |
|---|---|
| `ABA0` | Full-day absence |
| `ABU0` | Late entry / early exit (see `evtHPos` for lesson slot) |

**Justification reason codes (observed):**

| Code | Description |
|---|---|
| `A` | Motivi di salute |
| `B` | Motivi di famiglia |

---

### Agenda

```
GET /rest/w1/students/{studentId}/agendav2/all/{dateFrom}/{dateTo}
```

Returns agenda events (homework assignments, tests, reminders) in the given date range.

**Date format:** `YYYYMMDD`

**Example:**
```
GET /rest/w1/students/{studentId}/agendav2/all/20250901/20260630
```

**Response:**

```json
{
  "agenda": [
    {
      "evtId": 630205,
      "evtCode": "AGNT",
      "evtDatetimeBegin": "2025-11-19T10:00:00+01:00",
      "evtDatetimeEnd": "2025-11-19T11:00:00+01:00",
      "isFullDay": false,
      "notes": "Verifica scritta di fisica",
      "authorName": "SURNAME NAME",
      "classDesc": "5A LICEO SCIENTIFICO",
      "subjectId": null,
      "subjectDesc": null,
      "homeworkId": null
    }
  ]
}
```

---

### Lessons

```
GET /rest/w1/students/{studentId}/lessons/{dateFrom}/{dateTo}
```

Returns lesson log entries in the given date range. Same date format as Agenda (`YYYYMMDD`).

**Example:**
```
GET /rest/w1/students/{studentId}/lessons/20250901/20260630
```

**Response:**

```json
{
  "lessons": [
    {
      "evtId": 26623458,
      "evtDate": "2026-06-04",
      "evtCode": "LSF0",
      "evtHPos": 1,
      "evtDuration": 1,
      "classDesc": "5A LICEO SCIENTIFICO",
      "authorName": "SURNAME NAME",
      "subjectId": 210068,
      "subjectCode": "ART",
      "subjectDesc": "DISEGNO E STORIA DELL'ARTE",
      "lessonType": "Lezione",
      "lessonArg": "Argomento della lezione"
    }
  ]
}
```

**Event codes:**

| Code | Description |
|---|---|
| `LSF0` | Regular lesson |
| `LSS0` | Co-presence (compresenza, e.g. support teacher) |

**`evtHPos`**: lesson slot number within the day (1 = first hour, etc.)

---

### Noticeboard

#### New frontend: communications and attachments

The `/fe/fam/bacheca` frontend also uses a separate noticeboard API. If the student-scoped endpoint below returns an empty `items` array while the website shows notices, check this endpoint instead.

##### List communications

```http
GET /rest/w1/noticeboarduser/{studentId}/communications
```

The browser sends its authenticated web-session cookies (`credentials: "include"`). A mobile API token is not interchangeable with this cookie session: a request with that token to `/rest/w1/...` returned HTTP 401 in the verification below.

The same route under **`/rest/v1/`** was verified with the existing mobile-app authentication:

```http
GET /rest/v1/noticeboarduser/{studentId}/communications
Z-Auth-Token: <mobile-login-token>
Z-Dev-Apikey: <app-api-key>
User-Agent: <mobile-app-user-agent>
```

Use the authentication result's numeric student identity, rather than hardcoding an ID. Verification used `CVVS/std/4.2.3 Android/12` and the existing app API-key configuration. Header compatibility may change; these observations do not establish support for every account or school.

**Sanitized response example** (selected fields; IDs, title and file name are synthetic):

```json
{
  "communications": [
    {
      "id": 1001,
      "titolo": "Avviso: Orario definitivo",
      "sottotitolo": "",
      "testo": "<p>Consultare l'allegato.</p>",
      "numerazione": 1,
      "tipologia_desc": "News",
      "pubblicazione": "2026-10-09 16:04:00",
      "scadenza": "2027-01-15 13:49:00",
      "anno_scol": 2026,
      "n_allegati": 1,
      "attachment": [
        {
          "id": 2001,
          "descrizione": "Orario definitivo.pdf",
          "extension": "",
          "firma": {
            "da_firmare": 0,
            "tipo_firma": null,
            "id_doc_sdg": null
          }
        }
      ]
    }
  ]
}
```

`testo` can contain HTML and HTML entities. `attachment` is singular in the response key, but its value is an array. Additional observed communication keys include `id_tipologia`, `tipologia_colore`, `evidenza`, `stato_notifica`, `autore_id`, `autore_cognome`, `autore_nome`, `testo_link`, `pubblicazione_it`, `pubblicazione_hm`, `scadenza_it`, `scadenza_hm`, `risposta_adesione`, `risposta_conferma`, `risposta_testo`, `risposta_file`, `num_file_risposta`, `risposta_questionari`, `cassetto_documentale`, `editable_response` and `response`. Their full semantics have not been verified.

##### Download a communication attachment

```http
GET /rest/w1/noticeboarduser/{studentId}/attachments/{schoolYear}/{communicationId}/{attachmentId}
```

For mobile-token authentication, use the verified equivalent:

```http
GET /rest/v1/noticeboarduser/{studentId}/attachments/{schoolYear}/{communicationId}/{attachmentId}
```

| Path parameter | Source |
|---|---|
| `studentId` | Numeric authenticated student identity |
| `schoolYear` | Communication's `anno_scol` (e.g. `2026`); use the value on each record |
| `communicationId` | Communication's `id` |
| `attachmentId` | Element's `attachment[].id`, **not** its array index |

The response is the file body, not necessarily JSON. The browser request used `Accept: application/json`, but a tested PDF returned HTTP 200, `Content-Type: application/pdf` and a valid `%PDF` signature. `attachment[].descrizione` provided the file name; `extension` was empty for this PDF.

##### Verification and observed limitations

Verified on **2026-10-11** for one student account:

- `/rest/v1/students/{studentId}/noticeboard` returned HTTP 200 with `{"items": []}`, including after a fresh mobile login.
- `/rest/v1/noticeboarduser/{studentId}/communications` returned **270 communications** and **279 attachment entries**. The response contained expired notices and records with both `anno_scol: 2025` and `anno_scol: 2026`; no date or read-status filter was supplied. Do not substitute the current year for `anno_scol` when downloading older files. Server pagination or limits for larger datasets have not been verified.
- **278 attachments** were downloaded. One listed JPG repeatedly returned HTTP 200, `Content-Type: application/x-empty` and a **zero-byte body**. Check both the HTTP status and the body before treating a download as successful; retain failures for a later retry.
- Only list and attachment GET requests were used. No read, reply, join or signature endpoint was called; whether these GET requests themselves change read status was not independently verified.

All examples above are sanitized; no real student IDs, tokens, cookies, names, or file contents are included.

#### Student-scoped noticeboard: list notices

```
GET /rest/w1/students/{studentId}/noticeboard
```

Returns the list of circular notices (circolari) and other board items.

**Response:**

```json
{
  "items": [
    {
      "pubId": 34992568,
      "pubDT": "2026-06-03T11:57:09+02:00",
      "readStatus": true,
      "evtCode": "CF",
      "cntId": 14339567,
      "cntValidFrom": "2026-06-03",
      "cntValidTo": "2026-06-06",
      "cntValidInRange": true,
      "cntStatus": "active",
      "cntTitle": "CIRC. N. 518 - Evento OTTANTA ANNI REPUBBLICA",
      "cntNum": "740",
      "cntCategory": "Circolare",
      "cntHasChanged": false,
      "cntHasAttach": true,
      "needJoin": false,
      "needReply": false,
      "needFile": false,
      "needSign": false,
      "attachments": [
        {
          "fileName": "CIRC. N. 518 - Evento OTTANTA ANNI REPUBBLICA.pdf",
          "attachNum": 1
        }
      ]
    }
  ]
}
```

#### Read a notice (mark as read + get content)

```
GET /rest/w1/students/{studentId}/noticeboard/readmulti/{evtCode}/{pubId}/101
```

**Path params taken from the list response:**
- `evtCode`: e.g. `CF`
- `pubId`: e.g. `34992568`
- `101`: don't know what is this for

**Response:**

```json
{
  "item": {
    "title": "740 - CIRC. N. 518 - Evento OTTANTA ANNI REPUBBLICA",
    "text": "CIRC. N. 518 - Evento OTTANTA ANNI REPUBBLICA"
  },
  "reply": {
    "replJoin": false,
    "replText": null,
    "replFile": null,
    "replSign": null
  }
}
```

#### Download attachment
 
```
/rest/w1/students/{studentId}/noticeboard/attach/{evtCode}/{pubId}/{attachNum}
```

returns a binary file.

---

### Didactics (Materials)

```
GET /rest/w1/students/{studentId}/didactics
```

Returns teaching materials shared by teachers, organized in folders.

**Response:**

```json
{
  "didacticts": [
    {
      "teacherId": "A0000000",
      "teacherName": "SURNAME NAME",
      "teacherFirstName": "NAME",
      "teacherLastName": "SURNAME",
      "folders": [
        {
          "folderId": 10218873,
          "folderName": "Nome cartella",
          "lastShareDT": "2025-11-21T09:46:29+01:00",
          "contents": [
            {
              "contentId": 10218874,
              "contentName": "Nome contenuto",
              "objectId": 5844429,
              "objectType": "link",
              "shareDT": "2025-11-21T09:46:29+01:00"
            },
            {
              "contentId": 10218877,
              "contentName": "Nome file",
              "objectId": 5844431,
              "objectType": "file",
              "shareDT": "2025-11-21T09:46:29+01:00"
            }
          ]
        }
      ]
    }
  ]
}
```

> **Note:** The response key is `"didacticts"` (typo in the original API).

**`objectType` values (observed):**

| Value | Description |
|---|---|
| `file` | Downloadable file |
| `link` | External URL |

---

### Homeworks

```
GET /rest/w1/students/{studentId}/homeworks/index
```

**Response:**

```json
{
  "items": [
    {"evtId": 1, "evtCode": "NEWDC", "teacherId": 1, "teacherName": "Docente A", "homeworkDesc": "Attività 1: materiale da portare per esercitazione in classe.", "homeworkDone": false, "assignmentDate": "2026-09-10", "expiryDate": "2026-09-14", "subjectId": 6, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "lastStudentMsg": null, "lastTeacherMsg": null, "newMessages": false, "teacherFiles": [], "teacherLinks": [], "studentFiles": [], "correctedFiles": []},
    {"evtId": 2, "evtCode": "NEWDC", "teacherId": 1, "teacherName": "Docente A", "homeworkDesc": "Attività 2: ripasso argomenti svolti ed esercizi assegnati.", "homeworkDone": false, "assignmentDate": "2026-09-10", "expiryDate": "2026-09-14", "subjectId": 4, "subjectDesc": "LINGUA E CULTURA LATINA", "lastStudentMsg": null, "lastTeacherMsg": null, "newMessages": false, "teacherFiles": [], "teacherLinks": [], "studentFiles": [], "correctedFiles": []},
    {"evtId": 3, "evtCode": "NEWDC", "teacherId": 1, "teacherName": "Docente A", "homeworkDesc": "Attività 3: ripasso argomenti ed esercizi assegnati.", "homeworkDone": false, "assignmentDate": "2026-09-14", "expiryDate": "2026-09-15", "subjectId": 6, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "lastStudentMsg": null, "lastTeacherMsg": null, "newMessages": false, "teacherFiles": [], "teacherLinks": [], "studentFiles": [], "correctedFiles": []},
    {"evtId": 4, "evtCode": "NEWDC", "teacherId": 1, "teacherName": "Docente A", "homeworkDesc": "Attività 4: scegliere un brano da un libro e motivare la scelta.", "homeworkDone": false, "assignmentDate": "2026-09-14", "expiryDate": "2026-09-15", "subjectId": 6, "subjectDesc": "LINGUA E LETTERATURA ITALIANA", "lastStudentMsg": null, "lastTeacherMsg": null, "newMessages": false, "teacherFiles": [], "teacherLinks": [], "studentFiles": [], "correctedFiles": []}
  ]
}
```

---

### Notes

```
GET /rest/w1/students/{studentId}/notes/all/
```

Returns disciplinary notes by category.

**Response:**

```json
{
  "NTTE": [],
  "NTCL": [],
  "NTWN": [],
  "NTST": []
}
```

**Note type keys (observed):**

| Key | Likely meaning |
|---|---|
| `NTTE` | Teacher notes |
| `NTCL` | Class notes |
| `NTWN` | Warnings |
| `NTST` | ? |

---

### Schoolbooks

```
GET /rest/w1/students/{studentId}/schoolbooks/index
```

Returns the list of adopted textbooks for the student's course.

**Response:**

```json
{
  "schoolbooks": [
    {
      "courseId": 104,
      "courseDesc": "LICEO SCIENTIFICO",
      "books": [
        {
          "bookId": 181249,
          "isbnCode": "9788808564177",
          "title": "CHIMICA: CONCETTI E MODELLI",
          "subheading": "DALLA MATERIA ALL'ATOMO",
          "volume": "U",
          "author": "VALITUTTI GIUSEPPE",
          "publisher": "ZANICHELLI EDITORE",
          "subjectDesc": "CHIMICA 1^ E 2^ ANNO",
          "price": 14.9,
          "toBuy": true,
          "newAdoption": false,
          "alreadyOwned": false,
          "alreadyInUse": true,
          "recommended": false,
          "recommendedFor": null,
          "coverUrl": null,
          "publisherUnlockCode": ""
        }
      ]
    }
  ]
}
```

---

### Ministerial Communications

```
GET /rest/w1/noticeboarduser/{studentId}/communications_minister
```

Returns ministerial communications directed to the student.

**Response:**

```json
{
  "communications": []
}
```

---

## Data Types Reference

### Date / DateTime formats

| Format | Example | Used in |
|---|---|---|
| `YYYY-MM-DD` | `2025-10-24` | grades, absences, lessons |
| `YYYY-MM-DDTHH:MM:SS+HH:MM` | `2026-06-03T11:57:09+02:00` | agenda, noticeboard, didactics |
| `YYYYMMDD` | `20250901` | URL path params (agenda, lessons) |

### Common ID types

| Field | Type | Example |
|---|---|---|
| Student numeric ID | integer | `13000000` |
| Student identity code | string | `S000000` |
| Teacher ID | string | `A0000000` |
| Subject ID | integer | `210068` |
| Event ID | integer | `630205` |

---

## Contributing

Found a new endpoint? Spotted an error? PRs and issues welcome.

When adding a new endpoint, please include:
- Full URL pattern
- Required cookies / auth
- At least one sanitized example response
- Notes on any observed edge cases or undocumented fields
