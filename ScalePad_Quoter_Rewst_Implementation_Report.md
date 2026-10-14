# ScalePad Quoter → Rewst implementation report

Prepared October 14, 2026 for Advanced Business Solutions.

## Result and source

- **59 operations across 30 paths**, with **94 reusable schemas**. All 59 Quoter endpoint pages were downloaded successfully. Coverage was independently reconciled with the Quoter API section in both the official documentation index and rendered portal sidebar.
- **OpenAPI 3.0.3** file: `ScalePad_Quoter_Rewst_OpenAPI.yaml`.
- **Base URL:** `https://api.scalepad.com/quoter`; operation paths begin with `/v1/`. Do not append a second `/v1` to the configured base URL. ScalePad documents Quoter as US-only.
- **Authentication:** header API key (`x-api-key`), represented by `ApiKeyAuth`. No separate token request, OAuth flow, or bearer prefix is documented for these operations.
- **Official specification found:** the portal registers `quoter-api.json` and exposes official OpenAPI 3.0.0 endpoint definitions in its Markdown exports. This deliverable merges those official definitions; it was not manually reconstructed from guesses. A public standalone download of the complete registered file was not obtained; its internal portal URI returned 404. The individual official exports supply all 59 indexed operations and their referenced components.
- All documented request parameters, request bodies, responses, required/optional markers, pagination, and referenced component definitions are preserved. Five Supplier operations lacked operationIds; descriptive unique IDs were added. Missing operation summaries were filled from the official page titles. The version marker is 3.0.3. The globally documented 429 response and Retry-After header were added to each operation. The vendor `x-sunset` field was removed; its sunset date remains in the deprecated operation description. No other API contract fields were changed.
- Only public documentation examples are included. No tenant credentials or live customer responses were used.

Primary sources:

- [ScalePad documentation index](https://developer.scalepad.com/llms.txt)
- [API authentication, pagination, errors, and rate limits](https://developer.scalepad.com/docs/getting-started)
- [Subscription requirements](https://developer.scalepad.com/docs/product-apis-and-subscriptions)
- [Regional availability](https://developer.scalepad.com/docs/regional-endpoints-and-compliance)
- Endpoint-specific sources are linked in the inventory below.

## Coverage, exclusions, and documentation limitations

No documented Quoter operation was excluded. **Create Line Item** (`POST /v1/line-items`) remains included with `deprecated: true`; it stops working on **2027-10-31**. Use Create Quote Line Items for new automations.

All non-Quoter product operations were excluded, including Core, Lifecycle Manager, ControlMap, and Backup Radar. The initially supplied List Clients reference belongs to the Core API and is intentionally absent. There is no documented Quoter List Clients operation in this inventory. No generic quote PATCH or DELETE operation was added. The older Quoter.com documentation was not used.

The official schemas leave some runtime rules in descriptions rather than structural validation. Examples include requiring at least one of contact.client_id/contact.client_name when creating a quote, positive decimal quantities, state-dependent publishing, matching recurring intervals, and rejecting empty nested objects in line-item patches. Those documented rules remain in descriptions; they have not been replaced with inferred fields or schemas.

One source inconsistency is preserved and needs attention if Rewst validates input strictly: CreateQuoteContact.client_name has schema maxLength 255, while its description says any length is accepted when client_id is provided and client_name is ignored. Prefer supplying client_id alone, or keeping client_name within 255 characters. Per-operation response lists are not uniformly exhaustive for authentication/server errors; no undocumented product-specific error shape was invented. The common rate-limit response is explicitly documented for all endpoints.

## Validation

Passed YAML parsing with duplicate-key rejection; OpenAPI 3.0.3 validation using openapi-spec-validator 0.7.2; all 433 local $ref occurrences resolved; 59 operationIds were unique; all path placeholders had corresponding required path parameters; Quoter-only source checks; full index/sidebar coverage; and comparison of preserved operation/component contract fields against their official exports.

Validation evidence is in `ScalePad_Quoter_Validation.json`. No authenticated live API calls or actual Rewst import were performed.

## Import into Rewst and configure authentication

1. Open Rewst's custom integration builder, create an integration for **ScalePad Quoter**, and use its OpenAPI import option to upload `ScalePad_Quoter_Rewst_OpenAPI.yaml`.
2. Confirm the server is `https://api.scalepad.com/quoter` and review the 59 actions and their HTTP methods. The deprecated action may be hidden or marked by the importer; retain its definition while selecting Create Quote Line Items for new workflows.
3. Configure an API-key credential securely in Rewst and map it to the **x-api-key** request header. Use the raw key as the header value, without a Bearer prefix. Check that Rewst applies it to every action and does not send it as a query parameter. Do not put its value in this YAML.
4. Generate the key through ScalePad's personal API key settings. ScalePad requires an active Administrator user and a qualifying paid product subscription. Deactivation or loss of Administrator permission makes the user's keys stop working. Manage expiry/rotation through ScalePad and Rewst's credential settings.
5. Run a read-only action, such as List Quote Templates, to verify access. Review its documented response and then test List Items with pagination before building quote-creation workflows.
6. Configure pagination: list actions use page_size (1–200) and cursor; feed the response next_cursor into the next request until it is absent/empty. Do not assume OpenAPI import creates a pagination loop automatically. SupplierSync data-feed lists document a default page_size of 100; ordinary list parameters do not declare that same default.
7. Apply the documented default rate limit of **50 requests per 5 seconds**, and honor **Retry-After** in a 429 response. Configure throttling/retry handling in Rewst as needed.

## Rewst compatibility and manual action configuration

The file uses standard OpenAPI 3.0.3 features, local references, and header API-key authentication. No vendor extensions remain. Actual compatibility with this Rewst instance is untested.

- Nested request/response objects use standard allOf plus nullable declarations. If the importer does not generate complete input controls, configure the action's JSON body manually. Do not flatten or alter the API payload shape to suit a form.
- Create Quote Sections and Create Quote Line Items accept **top-level JSON arrays**, not objects wrapping an array. Preserve these raw body shapes.
- PATCH distinguishes omitted fields from explicit null for documented nullable fields. Make sure Rewst does not automatically send null or empty objects for unused inputs.
- Preserve bracketed query parameter names (for example filter[name]) and use the filter/sort syntax described for each parameter. Do not assume all list endpoints expose identical filters.
- Decimal prices and quantities are documented as strings in relevant schemas; avoid converting them to floating-point numbers in generated action inputs.
- Configure cursor pagination, throttling, retry handling, and secure credential binding after import. OpenAPI describes the API contract, not those workflow behaviors.
- Quote creation requires a template and an existing contact, identified by email plus client_id or client_name. Then add sections, add their line items, and publish using the documented operations. The fetched quote identifier, section identifiers, and line-item identifiers must be mapped between actions.

## Complete endpoint inventory

Paths below are relative to `https://api.scalepad.com/quoter`. Source names are retained; SupplierSync operations are distinguished from ordinary supplier operations by their paths and IDs.

| Action | Method | Path | operationId | Official source |
|---|---|---|---|---|
| Create Quote | POST | `/v1/quotes` | `createQuote` | [Reference](https://developer.scalepad.com/reference/createquote.md) |
| List Quotes | GET | `/v1/quotes` | `listQuotes` | [Reference](https://developer.scalepad.com/reference/listquotes.md) |
| Fetch Quote | GET | `/v1/quotes/{quote_id}` | `fetchQuote` | [Reference](https://developer.scalepad.com/reference/fetchquote.md) |
| Publish Quote | POST | `/v1/quotes/{quote_id}/publish` | `publishQuote` | [Reference](https://developer.scalepad.com/reference/publishquote.md) |
| Create Quote Sections | POST | `/v1/quotes/{quote_id}/sections` | `createQuoteSections` | [Reference](https://developer.scalepad.com/reference/createquotesections.md) |
| Patch Quote Line Item | PATCH | `/v1/quotes/{quote_id}/sections/{section_id}/line-items/{line_item_id}` | `patchQuoteSectionLineItem` | [Reference](https://developer.scalepad.com/reference/patchquotesectionlineitem.md) |
| Create Quote Line Items | POST | `/v1/quotes/{quote_id}/sections/{section_id}/line-items` | `createQuoteLineItems` | [Reference](https://developer.scalepad.com/reference/createquotelineitems.md) |
| Create Line Item (deprecated) | POST | `/v1/line-items` | `createLineItem` | [Reference](https://developer.scalepad.com/reference/createlineitem.md) |
| List Quote Templates | GET | `/v1/quote-templates` | `listQuoteTemplates` | [Reference](https://developer.scalepad.com/reference/listquotetemplates.md) |
| Create Category | POST | `/v1/categories` | `createCategory` | [Reference](https://developer.scalepad.com/reference/createcategory.md) |
| List Categories | GET | `/v1/categories` | `listCategories` | [Reference](https://developer.scalepad.com/reference/listcategories.md) |
| Delete Category | DELETE | `/v1/categories/{id}` | `deleteCategory` | [Reference](https://developer.scalepad.com/reference/deletecategory.md) |
| Fetch Category | GET | `/v1/categories/{id}` | `fetchCategory` | [Reference](https://developer.scalepad.com/reference/fetchcategory.md) |
| Update Category | PATCH | `/v1/categories/{id}` | `updateCategory` | [Reference](https://developer.scalepad.com/reference/updatecategory.md) |
| Create Item | POST | `/v1/items` | `createItem` | [Reference](https://developer.scalepad.com/reference/createitem.md) |
| List Items | GET | `/v1/items` | `listItems` | [Reference](https://developer.scalepad.com/reference/listitems.md) |
| Delete Item | DELETE | `/v1/items/{id}` | `deleteItem` | [Reference](https://developer.scalepad.com/reference/deleteitem.md) |
| Fetch Item | GET | `/v1/items/{id}` | `fetchItem` | [Reference](https://developer.scalepad.com/reference/fetchitem.md) |
| Update Item | PATCH | `/v1/items/{id}` | `updateItem` | [Reference](https://developer.scalepad.com/reference/updateitem.md) |
| Create Item Group Assignment | POST | `/v1/item-group-item-assignments` | `createItemGroupItemAssignment` | [Reference](https://developer.scalepad.com/reference/createitemgroupitemassignment.md) |
| List Item Group Assignments | GET | `/v1/item-group-item-assignments` | `listItemGroupItemAssignments` | [Reference](https://developer.scalepad.com/reference/listitemgroupitemassignments.md) |
| Delete Item Group Assignment | DELETE | `/v1/item-group-item-assignments/{id}` | `deleteItemGroupItemAssignment` | [Reference](https://developer.scalepad.com/reference/deleteitemgroupitemassignment.md) |
| Fetch Item Group Assignment | GET | `/v1/item-group-item-assignments/{id}` | `fetchItemGroupItemAssignment` | [Reference](https://developer.scalepad.com/reference/fetchitemgroupitemassignment.md) |
| Create Item Group | POST | `/v1/item-groups` | `createItemGroup` | [Reference](https://developer.scalepad.com/reference/createitemgroup.md) |
| List Item Groups | GET | `/v1/item-groups` | `listItemGroups` | [Reference](https://developer.scalepad.com/reference/listitemgroups.md) |
| Delete Item Group | DELETE | `/v1/item-groups/{id}` | `deleteItemGroup` | [Reference](https://developer.scalepad.com/reference/deleteitemgroup.md) |
| Fetch Item Group | GET | `/v1/item-groups/{id}` | `fetchItemGroup` | [Reference](https://developer.scalepad.com/reference/fetchitemgroup.md) |
| Update Item Group | PATCH | `/v1/item-groups/{id}` | `updateItemGroup` | [Reference](https://developer.scalepad.com/reference/updateitemgroup.md) |
| Create Item Option Value | POST | `/v1/item-option-values` | `createItemOptionValue` | [Reference](https://developer.scalepad.com/reference/createitemoptionvalue.md) |
| List Item Option Values | GET | `/v1/item-option-values` | `listItemOptionValues` | [Reference](https://developer.scalepad.com/reference/listitemoptionvalues.md) |
| Delete Item Option Value | DELETE | `/v1/item-option-values/{id}` | `deleteItemOptionValue` | [Reference](https://developer.scalepad.com/reference/deleteitemoptionvalue.md) |
| Fetch Item Option Value | GET | `/v1/item-option-values/{id}` | `fetchItemOptionValue` | [Reference](https://developer.scalepad.com/reference/fetchitemoptionvalue.md) |
| Update Item Option Value | PATCH | `/v1/item-option-values/{id}` | `updateItemOptionValue` | [Reference](https://developer.scalepad.com/reference/updateitemoptionvalue.md) |
| Create Item Option | POST | `/v1/item-options` | `createItemOption` | [Reference](https://developer.scalepad.com/reference/createitemoption.md) |
| List Item Options | GET | `/v1/item-options` | `listItemOptions` | [Reference](https://developer.scalepad.com/reference/listitemoptions.md) |
| Delete Item Option | DELETE | `/v1/item-options/{id}` | `deleteItemOption` | [Reference](https://developer.scalepad.com/reference/deleteitemoption.md) |
| Fetch Item Option | GET | `/v1/item-options/{id}` | `fetchItemOption` | [Reference](https://developer.scalepad.com/reference/fetchitemoption.md) |
| Update Item Option | PATCH | `/v1/item-options/{id}` | `updateItemOption` | [Reference](https://developer.scalepad.com/reference/updateitemoption.md) |
| Create Item Tier | POST | `/v1/item-tiers` | `createItemTier` | [Reference](https://developer.scalepad.com/reference/createitemtier.md) |
| List Item Tiers | GET | `/v1/item-tiers` | `listItemTiers` | [Reference](https://developer.scalepad.com/reference/listitemtiers.md) |
| Delete Item Tier | DELETE | `/v1/item-tiers/{id}` | `deleteItemTier` | [Reference](https://developer.scalepad.com/reference/deleteitemtier.md) |
| Fetch Item Tier | GET | `/v1/item-tiers/{id}` | `fetchItemTier` | [Reference](https://developer.scalepad.com/reference/fetchitemtier.md) |
| Update Item Tier | PATCH | `/v1/item-tiers/{id}` | `updateItemTier` | [Reference](https://developer.scalepad.com/reference/updateitemtier.md) |
| Create Manufacturer | POST | `/v1/manufacturers` | `createManufacturer` | [Reference](https://developer.scalepad.com/reference/createmanufacturer.md) |
| List Manufacturers | GET | `/v1/manufacturers` | `listManufacturers` | [Reference](https://developer.scalepad.com/reference/listmanufacturers.md) |
| Delete Manufacturer | DELETE | `/v1/manufacturers/{id}` | `deleteManufacturer` | [Reference](https://developer.scalepad.com/reference/deletemanufacturer.md) |
| Fetch Manufacturer | GET | `/v1/manufacturers/{id}` | `fetchManufacturer` | [Reference](https://developer.scalepad.com/reference/fetchmanufacturer.md) |
| Update Manufacturer | PATCH | `/v1/manufacturers/{id}` | `updateManufacturer` | [Reference](https://developer.scalepad.com/reference/updatemanufacturer.md) |
| Create Supplier | POST | `/v1/suppliers` | `createSupplier` | [Reference](https://developer.scalepad.com/reference/post_v1-suppliers-1.md) |
| List Suppliers | GET | `/v1/suppliers` | `listSuppliers` | [Reference](https://developer.scalepad.com/reference/get_v1-suppliers-1.md) |
| Delete Supplier | DELETE | `/v1/suppliers/{id}` | `deleteSupplier` | [Reference](https://developer.scalepad.com/reference/delete_v1-suppliers-id-1.md) |
| Fetch Supplier | GET | `/v1/suppliers/{id}` | `fetchSupplier` | [Reference](https://developer.scalepad.com/reference/get_v1-suppliers-id-1.md) |
| Update Supplier | PATCH | `/v1/suppliers/{id}` | `updateSupplier` | [Reference](https://developer.scalepad.com/reference/patch_v1-suppliers-id-1.md) |
| List Supplier Items | GET | `/v1/datafeeds/supplier-items` | `listDatafeedSupplierItems` | [Reference](https://developer.scalepad.com/reference/listdatafeedsupplieritems.md) |
| List Suppliers | GET | `/v1/datafeeds/suppliers` | `listDatafeedSuppliers` | [Reference](https://developer.scalepad.com/reference/listdatafeedsuppliers.md) |
| Create Contact | POST | `/v1/contacts` | `createContact` | [Reference](https://developer.scalepad.com/reference/createcontact.md) |
| List Contacts | GET | `/v1/contacts` | `listContacts` | [Reference](https://developer.scalepad.com/reference/listcontacts.md) |
| Fetch Contact | GET | `/v1/contacts/{id}` | `fetchContact` | [Reference](https://developer.scalepad.com/reference/fetchcontact.md) |
| Update Contact | PATCH | `/v1/contacts/{id}` | `updateContact` | [Reference](https://developer.scalepad.com/reference/updatecontact.md) |

YAML SHA-256: `e302565ffb8a6fe4a852b2dca8b235d83154c95af1d5beed9ae46adb1f7cd9dc`.
