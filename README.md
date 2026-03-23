# UI Specification Document: User Management Screen

## 1. Overview
This document outlines the user interface specifications for the **User Management Screen**. The interface follows a pattern with a split-screen layout. The left pane contains a data grid displaying a list of users, while the right pane contains a form for creating new users or editing existing ones.

## 2. Initial State (On Page Load)
* **Data Grid:** Populated with the first page of user data fetched from the API.
* **Hide Disabled User Checkbox:** Checked by default (as per the UI mockup). The grid should filter out users with `Enabled = false`.
* **Form Panel:** * Displays the title "New User".
  * All input fields are empty/reset.
  * The "Enabled" checkbox is unchecked.
* **Save User Button:** Visible but disabled until required fields in the form are filled.
* **Row Selection:** No row is selected by default unless a specific user ID is passed via URL parameters.

## 3. UI Component Details & Behavior

### 3.1 Top Action Bar
* **`+ New User` Button**
  * **Type:** Primary Button (Icon + Text).
  * **Action:** When clicked, clears any active row selection in the data grid, resets the form to its empty state, and changes the form title to "New User".
* **`Hide Disabled User` Checkbox**
  * **Type:** Checkbox with Label.
  * **Action:** Toggles the visibility of disabled users in the Data Grid. 
    * *Checked:* Filters out users where `Enabled == false`.
    * *Unchecked:* Shows all users.
  * **Trigger:** Triggers a grid refresh/re-render immediately upon toggling.
* **`Save User` Button**
  * **Type:** Primary Action Button.
  * **Location:** Top right corner.
  * **Action:** Submits the form data. Triggers validation before submission.
  * **State:** Should show a loading spinner or disable state during API call processing to prevent double-clicks.

### 3.2 User Data Grid (Left Panel)
* **Type:** Data Table / Grid.
* **Columns:**
  * **`ID`**: Numeric user identifier.
  * **`User Name`**: String.
  * **`Email`**: String.
  * **`Enabled`**: Boolean (`true`/`false`).
* **Header Controls:** Every column header includes:
  * **Sort Icon (Up/Down Arrows):** Clicking sorts the column ascending/descending.
  * **Filter Icon (Funnel):** Clicking opens a contextual popover/input to filter data specifically for that column.
* **Row Interaction:**
  * **Hover:** Rows should have a subtle background color change on hover.
  * **Click (Selection):** Clicking a row highlights it (e.g., light blue background). Upon selection, the right-hand form populates with the selected user's data, and the form title changes from "New User" to "Edit User" (or reflects the selected username).

### 3.3 User Details Form (Right Panel)
* **Section Title:** Dynamic text. Shows "New User" during creation flow, and ideally "Edit User" or "Edit [Username]" when a grid row is selected.
* **Input Fields:**
  * **`Username`**: Text input. Alphanumeric. (Required)
  * **`Display Name`**: Text input. (Required)
  * **`Phone`**: Text input. Numeric/telephonic format. (Optional)
  * **`Email`**: Text input. Must pass standard email format validation. (Required)
  * **`User Roles`**: Dropdown / Select input. 
    * Displays placeholder "Select user roles...".
    * Options: `Guest`, `Admin`, `SuperAdmin`.
    * Interaction: Must support selecting one role (or multiple, depending on backend RBAC logic. UI implies a standard dropdown list).
  * **`Enabled`**: Checkbox. 
    * Checked = `true` (Active user)
    * Unchecked = `false` (Disabled user)

## 4. Functional Workflows

### 4.1 Creating a New User
1. User clicks `+ New User` (or begins typing in the empty form on load).
2. User fills out `Username`, `Display Name`, `Email`, and selects a `User Role`.
3. User clicks `Save User`.
4. **System Response:** Form validates. If successful, sends a `POST` request. On success, displays a toast notification ("User created successfully"), refreshes the data grid to include the new user, and clears the form.

### 4.2 Editing an Existing User
1. User clicks a row in the Data Grid (e.g., row ID 2 "Test User").
2. The grid highlights the row.
3. The Form populates with the data of the selected user. 
4. User modifies fields (e.g., changes "Display Name").
5. User clicks `Save User`.
6. **System Response:** Form validates. Sends a `PUT` or `PATCH` request with the user's ID. On success, displays a toast notification ("User updated successfully"), updates the specific row in the data grid, and maintains the current selection state.

## 5. Validation Rules & Error Handling
* **Required Fields:** Attempting to save without `Username`, `Display Name`, `Email`, or `User Roles` should outline the respective fields in red and display inline error text.
* **Email Validation:** The `Email` field must contain an `@` and a valid domain.
* **API Errors:** If the `Save User` action fails (e.g., 500 Server Error or 409 Conflict for duplicate username), an error banner or toast notification must be displayed to the user with the failure reason.
