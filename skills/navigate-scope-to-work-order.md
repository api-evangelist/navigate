---
name: scope-to-work-order
description: Build a scope of line items at a Navigate location and turn it into a completed work order assigned to a vendor.
api: NavigateAI API
base_url: https://api.navigateai.co
auth: http-bearer
operations:
  - api_external_api_location_create_location
  - api_external_api_scopes_create_scope_endpoint
  - api_external_api_scope_create_line_item_row
  - api_external_api_location_create_work_order
  - api_external_api_location_set_line_item_work_order
  - api_external_api_location_start_work_order_review
  - api_external_api_location_complete_work_order
---

# Scope to work order

Assemble a scope of work at a location and execute it as a work order. Every request
carries `Authorization: Bearer <api_key>`.

## Steps

1. **Ensure a location** — `GET /v1/location` (`api_external_api_location_list_locations`,
   filter by `reference_key` or `market_id`) or create one with
   `POST /v1/location` (`api_external_api_location_create_location`).
2. **Create a scope** — `POST /v1/scopes` (`api_external_api_scopes_create_scope_endpoint`)
   for the location, then add line item rows with
   `POST /v1/scope/{location_id}/line_item_row` (`api_external_api_scope_create_line_item_row`).
   Read the assembled scope back with `GET /v1/scopes/{scope_id}`.
3. **Pick a vendor** — `GET /v1/vendors` (`api_external_api_vendor_list_vendor_organizations`)
   or create one with `POST /v1/vendors`.
4. **Create the work order** — `POST /v1/location/{location_id}/work_order`
   (`api_external_api_location_create_work_order`) with `default_vendor_id` and
   `line_item_ids`. Assign additional line items with
   `POST /v1/location/{location_id}/line_item/{line_item_id}/work_order`
   (`api_external_api_location_set_line_item_work_order`).
5. **Review and complete** — `POST /v1/location/{location_id}/work_order/{work_order_id}/start_review`
   (`api_external_api_location_start_work_order_review`), then
   `POST /v1/location/{location_id}/work_order/{work_order_id}/complete`
   (`api_external_api_location_complete_work_order`).

## Rules

- Cursor pagination on list endpoints: `limit` + `cursor` -> `next_cursor`.
- Use `reference_key` (and `price_reference_key` on line items) to correlate with your own catalog/records.
- Attach evidence to a work-order line item via
  `POST /v1/location/{location_id}/work_order/{work_order_id}/line_item/{line_item_id}/photos`.
- Vendors can override a line-item proposal status via the `vendor_override` operation.
