# enregistrer-mon-enfant

Static GitHub Pages microsite (no build step) where a parent registers their
child by scanning a center's QR code.

- `index.html` — the whole app: markup, inline CSS and inline JS.
- Reached as `https://enregistrer-mon-enfant.speakora.ca/?company=<company-uuid>`.
- Resolves the company via public `GET /cpe/company/:uuid`, then posts to
  `POST /cpe/kidPendingRegistration` on the backend.
- French only (`<html lang="fr">`); all strings are inline.

## Form fields

Three-step form. Page 1 = child, page 2 = parent #1, page 3 = parent #2 + consent.

| Step | Field (id) | Type | Required |
|---|---|---|---|
| 1 | `childFirstName` | text | yes |
| 1 | `childLastName` | text | yes |
| 1 | `birthDate` | date | yes |
| 1 | `attendedSince` — « Fréquente depuis » | date | yes |
| 2 | `parent1FirstName` / `parent1LastName` | text | yes |
| 2 | `parent1Email` | email | yes |
| 3 | `parent2FirstName` / `parent2LastName` / `parent2Email` | text/email | no |
| 3 | `consentCheckbox` (Loi 25) | checkbox | yes |

`attendedSince` is bounded to `[birthDate, today]`: `birthDate` sets the `min`
and `validatePage` re-checks the range because native `min`/`max` are advisory.

## Payload

```json
{
  "company": "<company name>",
  "parent_email": "...",
  "kid_first_name": "...",
  "kid_last_name": "...",
  "date_of_birth": "YYYY-MM-DD",
  "attended_since": "YYYY-MM-DD",
  "parent1_first_name": "...",
  "parent1_last_name": "...",
  "consent_given": true
}
```

Optional keys are spread conditionally, so absent values are omitted rather
than sent as `""`.

> Known gap: `parent2Email` is validated in the UI but never sent — there is no
> `parent2_email` key in the payload.
