# ScalePad Quoter for Rewst

OpenAPI 3.0.3 integration definition containing all 59 Quoter operations documented in the ScalePad developer portal when collected on October 14, 2026.

## Download the files

**[Download the complete ZIP](https://github.com/jwharner1/Quoter/raw/refs/heads/main/ScalePad_Quoter_Rewst_Package.zip)** — includes the YAML, SVG icon, implementation report, and validation results.

Individual files:

- [ScalePad_Quoter_Rewst_OpenAPI.yaml](ScalePad_Quoter_Rewst_OpenAPI.yaml)
- [Quoter_Rewst_Icon.svg](Quoter_Rewst_Icon.svg)
- [Implementation report and complete endpoint inventory](ScalePad_Quoter_Rewst_Implementation_Report.md)
- [Validation results](ScalePad_Quoter_Validation.json)

For an individual file, open it on GitHub and click **Download raw file** in the file toolbar. You can also download the repository using **Code → Download ZIP**. Sign in to GitHub if the repository is private.

## Import into Rewst

1. Import `ScalePad_Quoter_Rewst_OpenAPI.yaml` into Rewst's custom integration builder.
2. Configure the server as `https://api.scalepad.com/quoter`.
3. Securely bind your ScalePad API key to the `x-api-key` request header, without a Bearer prefix.
4. Upload `Quoter_Rewst_Icon.svg` as the integration icon. This is a custom quote-document design, not an official Quoter logo.
5. Review the report for cursor pagination, nested JSON and array bodies, PATCH omission versus null, throttling, and deprecated operations.

The definition includes 59 operations, 30 paths, and 94 reusable schemas. YAML parsing, OpenAPI validation, 433 reference occurrences, unique operation IDs, and coverage against official documentation exports and the portal sidebar passed validation.

An actual Rewst import and authenticated tenant API testing have not been performed. The deprecated Create Line Item endpoint remains included; its documented sunset is October 31, 2027. Use Create Quote Line Items for new workflows.
