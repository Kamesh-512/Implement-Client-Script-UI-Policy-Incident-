# ServiceNow Incident Management Enhancements

## Overview
This project implements custom enhancements to the Incident Management workflow in ServiceNow. It introduces UI Policies and Client Scripts to enforce specific business rules and improve data integrity, especially for High Impact incidents.

## Features
- **High Impact Control**: Enforces mandatory fields and read-only behaviors when an incident's impact is set to High.
- **Automated Urgency**: Automatically sets Urgency to High when Impact is updated to High.
- **Save Prevention**: Blocks saving an incident if it is High impact but lacks an assigned user.
- **List Edit Restrictions**: Prevents users from changing the Incident State directly from list views, ensuring updates are made via the form.
- **Dynamic Form Behavior**: Automatically reverts field constraints when conditions are no longer met.

## Implementation Details
The project utilizes the following ServiceNow platform features:
- **UI Policies and UI Policy Actions**: For dynamic form styling and field properties (e.g., read-only).
- **Client Scripts**: 
  - `onChange`: For auto-populating fields based on user input.
  - `onSubmit`: For complex form validation before saving.
  - `onCellEdit`: For restricting unauthorized list view edits.

## Documentation
For a detailed step-by-step guide and technical documentation of the implemented modules (including testing procedures), refer to [ProjectDocumentation.md](ProjectDocumentation.md).
