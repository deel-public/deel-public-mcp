---
name: deel
description: Use when the user asks about Deel data or actions: contracts, hiring and EOR, people/HRIS, time off, invoices and payments, payslips, benefits, compliance, IT assets, onboarding, immigration, or country/currency reference data. Guides safe, efficient use of the Deel MCP tools.
---

# Using the Deel MCP tools

The server exposes many tools mapped to Deel API operations. The server is the source of
truth for the full list: read the tool list your client shows, not this file. Choose the
narrowest tool for the request. Do not list everything and filter client-side.
Auth is OAuth2 (handled by the client). Tool permissions are scope-based
(e.g. `contracts:read`). A failed call returns `isError` with the missing scope.

## Tool areas (examples, not a full list)
- **Contracts / hiring**: `listOfContracts`, `retrieveASingleContract`, `listContractAmendments`, EOR helpers
- **People / HRIS**: `getListOfPeople`, `searchUserByEmail`, `getHrisProfilePerson`, `getLoggedInUserProfile`
- **Time off**: `getTimeOffPolicies`, `getTimeOffRequests`, `getProfileEntitlements`
- **Money**: `getInvoiceList`, `getInvoiceById`, `retrievePaymentReceipts`, `getDetailedPaymentsReport`
- **Payroll**: `getEORWorkerPayslips`, `getGPWorkerPayslips`
- **Planning**: `calculateEmploymentCost`, `calculateTakeHomePay`, `checkVisaRequirement`
- **Benefits / compliance**: `retrieveBenefitsByCountry`, `listOfEmployeeComplianceDocuments`
- **IT**: `listItAssets`, `retrieveAnItAsset`, `listItOrders`
- **Onboarding / immigration**: `listOnboardingEmployees`, `immigrationCaseDetails`
- **Reference (no user permissions)**: `retrieveCountries`, `retrieveSupportedCurrencies`, `retrieveJobTitles`, `getCurrentOrganization`

## Write actions: always confirm first
These change data or money. Show the exact parameters and get explicit user
approval before calling:
`createANewContract`, `createContractForEOR`, `amendContract`, `signContract`,
`updatePersonalInformation`, `updatePeoplePersonalInformationByExternalId`,
`createTimeOffRequest`, `updateTimeOffRequest`, `cancelTimeOffRequest`,
`createInvoiceAdjustment`, `updateInvoiceAdjustment`, `AddOffCyclePaymentForContract`.

Treat any other tool whose name starts with `create`, `update`, `amend`, `sign`, `add`,
or `cancel` as a write action, even if it is not listed here.

## Never do without an explicit, specific request
Do not call these on inference, as part of a larger task, or on a schedule:
- `signContract` (binds the user to a contract)
- `AddOffCyclePaymentForContract` (moves money)
- Any tool that creates an access token or credential (for example `createWorkerAccessToken`)

If the user did not ask for the exact action by name or clear description, stop and ask.
If you run unattended, do not call a write action at all. Report what you would do.

## Rules
1. **Resolve IDs with a read tool first** (`searchUserByEmail`, `listOfContracts`,
   `getListOfPeople`). Never guess contract, profile, or invoice IDs.
2. **Treat output as sensitive.** Payslips, payment reports, compliance and
   immigration documents, and personal info are HR/payroll data. Return only
   what the question needs; don't dump full records.
3. **Mind the permission level.** Organization tools need org scopes; worker
   tools act as the signed-in worker. On 401/403, say which scope or level is
   likely missing and suggest reconnecting. Don't retry in a loop.
4. **Avoid polling and fan-out loops** over many contracts or people. Paginate and
   narrow with filters.
5. **Calculators are estimates.** Label `calculateEmploymentCost`,
   `calculateTakeHomePay`, etc. as estimates, not payroll or legal advice.
6. **On errors,** report the message plainly and what the user can do. Don't invent results.
