---
name: Schedule and manage an interview slot
description: Create bookable calendar slots for a stage, book an applicant into one, and cancel a booking in Fountain.
api: llms/fountain-llms.txt
operations:
- POST /v2/available_slots (Create Calendar Slots)
- GET /v2/stages/{id}/available_slots (List Available Slots)
- POST /v2/available_slots/{id}/confirm (Book an Available Slot)
- POST /v2/booked_slots/{id}/cancel (Cancel a booked slot)
- GET /v2/sessions (List Calendar Slots)
---

# Schedule and manage an interview slot

Use this flow to run Fountain's scheduling surface: publish slots, book a candidate, and cancel if needed.

## Auth
- OAuth2 client-credentials bearer token (60-minute TTL) on `services.fountain.com`, or `X-ACCESS-TOKEN` on legacy tenant hosts. (see `authentication/fountain-authentication.yml`)

## Steps
1. **Create slots** — `POST /v2/available_slots` for the target stage, setting `max_attendees` (> 0).
2. **List openings** — `GET /v2/stages/{id}/available_slots` to see bookable slots for a stage.
3. **Book** — `POST /v2/available_slots/{id}/confirm` with the applicant to book them into the slot.
4. **Verify** — `GET /v2/sessions` (optionally including unbooked slots) filtered by opening/stage to confirm the booking.
5. **Cancel if needed** — `POST /v2/booked_slots/{id}/cancel` to release a booking.

## Rules
- When lowering `max_attendees` via `PATCH /v2/available_slots/{id}`, existing bookings are preserved; new bookings pause until `booked_slots_count` drops below the new cap.
- Respect the 120/min rate limit and `429` backoff. (see `conventions/fountain-conventions.yml`)
