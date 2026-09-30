# Project Documentation: ServiceNow Incident Management Enhancements

This document provides a detailed, step-by-step breakdown of the 10 modules implemented in this project to enhance the ServiceNow Incident Management workflow.

---

## Module 1: Create UI Policy for High Impact Control
**Goal**: Apply additional controls when an Incident has a high impact.

1. Navigate to **System UI → UI Policies**.
2. Click **New** to create a new UI Policy record.
3. Fill in the following details:
   - **Name**: High Impact Control (represents the purpose of the policy).
   - **Table**: Incident
   - **Active**: true
4. Configure the **Conditions** section:
   - **Field**: Impact
   - **Operator**: is
   - **Value**: 1 – High
   *(This ensures the policy triggers only when the Incident impact is set to High.)*
5. Under the UI Policy settings, check the **Reverse if false** box.
   *(This ensures field changes applied by the policy are reverted when conditions are no longer met.)*
6. Review the configuration for accuracy and click **Submit**.

---

## Module 2: Configure UI Policy Actions
**Goal**: Define specific field behaviors when the High Impact UI Policy is triggered.

1. Open the **High Impact Control** UI Policy created in Module 1.
2. Scroll to the **UI Policy Actions** related list and click **New**.
3. Fill in the Action Details:
   - **Field name**: Urgency
   - **Read-only**: true (Makes the Urgency field read-only when Impact = High)
   - **Visible**: Leave unchanged (Field remains visible)
4. Click **Submit**.

**Verification**: Open an Incident record, set Impact to High, and confirm Urgency becomes read-only. Change Impact to a different value to confirm the field reverts back.

---

## Module 3: Auto-set Urgency for High Impact (onChange Client Script)
**Goal**: Automatically set Urgency to High when Impact changes to High.

1. Navigate to **System UI → Client Scripts**.
2. Click **New** and fill in the details:
   - **Name**: Auto set urgency for high impact
   - **Table**: Incident
   - **Type**: onChange
   - **Field name**: Impact
   - **Active**: true
3. Paste the following script in the script editor:
   ```javascript
   function onChange(control, oldValue, newValue, isLoading) {
       if (isLoading || newValue == '') {
           return;
       }

       if (newValue == '1') {
           g_form.setValue('urgency', '1');
           g_form.addInfoMessage('Urgency set to High for High impact incident.');
       }
   }
   ```
4. Click **Submit**.

**Verification**: Open an Incident record and change the Impact field to High. The Urgency field should update automatically.

---

## Module 4: Prevent Save if Assigned To is Missing (onSubmit Client Script)
**Goal**: Prevent saving a High Impact incident if it is not assigned to anyone.

1. Navigate to **System UI → Client Scripts**.
2. Click **New** and fill in the details:
   - **Name**: Prevent save if Assigned To missing
   - **Table**: Incident
   - **Type**: onSubmit
   - **Active**: true
3. Paste the following script in the script editor:
   ```javascript
   function onSubmit() {
       if (g_form.getValue('impact') == '1' &&
           g_form.getValue('assigned_to') == '') {

           g_form.showErrorBox(
               'assigned_to',
               'Assigned To is mandatory for High impact incidents.'
           );
           return false;
       }
       return true;
   }
   ```
4. Click **Submit**.

**Verification**: Try saving an Incident without filling the Assigned To field while Impact is High. The script should block the save and display an error box.

---

## Module 5: Prevent State Change via List Edit (onCellEdit Client Script)
**Goal**: Prevent users from changing the State field directly from a list view.

1. Navigate to **System UI → Client Scripts**.
2. Click **New** and fill in the details:
   - **Name**: Prevent state change via list edit
   - **Table**: Incident
   - **Type**: onCellEdit
   - **Field name**: State
   - **Active**: true
3. Paste the following script in the script editor:
   ```javascript
   function onCellEdit(sysIDs, table, oldValues, newValue, callback) {
       alert('State cannot be updated using list editing. Please open the Incident.');
       callback(false);
   }
   ```
4. Click **Submit**.

**Verification**: Open an Incident list and try to change the State field directly. The script should block the change.

---

## Module 6: Testing - Save Prevention for Missing Assignee
**Goal**: End-to-end testing of the onSubmit Client Script.

1. Navigate to **Incident → Create New**.
2. Set the **Impact** field to High.
3. Leave **Assigned To** empty.
4. Click **Submit**.
5. **Verify**: The Incident should not save, and an error message should appear on the Assigned To field indicating it cannot be empty. Check that the UI Policy actions (like read-only Urgency) and the onChange auto-urgency updates fired correctly.

---

## Module 7: Testing - Successful Save with Assignee
**Goal**: End-to-end testing of a successfully configured High Impact incident.

1. On the same Incident form used in Module 6, select a user for the **Assigned To** field.
2. Click **Submit**.
3. **Verify**: The Incident should save successfully without errors. Ensure the read-only constraints and the auto-assigned Urgency behavior remained intact.

---

## Module 8: Testing - Reversing UI Policy Actions
**Goal**: Test the "Reverse if false" functionality on the UI Policy.

1. Go to **Incident → Open** and select an Incident where Impact is currently High.
2. Change **Impact** from High to Medium.
3. **Verify**: The Urgency field should now be editable, and Assigned To is no longer enforced as mandatory upon save.
4. Click **Submit** to save the changes successfully.

---

## Module 9: Testing - List Edit Restriction
**Goal**: Verify the onCellEdit Client Script functionality.

1. Navigate to **Incident → All** to view the list of Incidents.
2. Locate an Incident and double-click the **State** field to edit it directly in the list.
3. **Verify**: An alert message should appear stating that direct changes are not allowed. The State value should revert to its original value.

---

## Module 10: Testing - State Change via Form
**Goal**: Verify that form updates are allowed even when list view updates are restricted.

1. Open the same Incident record used in Module 9 by clicking into the form.
2. Update the **State** field on the form itself.
3. Click **Update**.
4. **Verify**: The State change should save successfully. Other rules like UI Policies and Client Scripts should continue to function normally.
