# Lab 13: Manual Testing Report

## 1. Test Execution Summary

| Metric | Details |
| :--- | :--- |
| **Project Name** | Chitkaar Welfare Society Platform |
| **Version Tested** | v1.0.0-beta |
| **Test Date** | March 30, 2026 |
| **Tester Name** | Kanishka Narang |
| **Total Test Cases** | 25 |
| **Passed** | 22 |
| **Failed** | 3 |
| **Pass Percentage** | 88% |

## 2. Detailed Test Case Results

### 2.1 Functional Testing (Module: Event Management)

| Test ID | Scenario | Expected Result | Actual Result | Status | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-01** | **View Event List** | List should display all active events sorted by date. | Displayed correctly. | **PASS** | - |
| **TC-02** | **Register (Valid)** | Form submits, data saved to Firebase, email sent. | Email received instantly. | **PASS** | - |
| **TC-03** | **Register (Duplicate)** | System should block duplicate email for same event. | Showed "Already Registered" error. | **PASS** | - |
| **TC-04** | **Filter by Category** | Clicking "Education" should only show Education events. | Filter worked as expected. | **PASS** | - |
| **TC-05** | **Past Events** | Past events should be visually distinct (greyscale). | **Bug:** Past events look identical to active ones. | **FAIL** | Low |

### 2.2 Functional Testing (Module: Admin Dashboard)

| Test ID | Scenario | Expected Result | Actual Result | Status | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-06** | **Admin Login** | Secure access to dashboard. | Access granted. | **PASS** | - |
| **TC-07** | **Create Event** | New event should appear on frontend immediately. | Event appeared after refresh. | **PASS** | - |
| **TC-08** | **Export CSV** | Downloaded file should contain all volunteer data. | **Bug:** Phone number column is missing in CSV. | **FAIL** | Medium |

### 2.3 Non-Functional Testing (UI/UX)

| Test ID | Scenario | Expected Result | Actual Result | Status | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-09** | **Mobile Responsiveness** | No horizontal scrolling on iPhone SE. | Layout is perfect. | **PASS** | - |
| **TC-10** | **404 Page** | Accessing invalid URL shows custom 404 page. | Shows default Next.js 404. | **FAIL** | Low |

## 3. Defect Report
A summary of the bugs found during this cycle.

| Defect ID | Associated TC | Description | Priority | Status |
| :--- | :--- | :--- | :--- | :--- |
| **D-01** | TC-05 | UI: Past events need visual distinction (opacity 0.5). | Low | Open |
| **D-02** | TC-08 | Backend: CSV Export function missing 'phone' field mapping. | Medium | Assigned |
| **D-03** | TC-10 | UX: Custom 404 page not implemented. | Low | Fixed |

## 4. Test Conclusion
The core functionalities (Registration, Event Listing, Admin CRUD) are stable. The identified defects are non-critical blockers and can be fixed in the next sprint (v1.0.1). The application is **Ready for Pilot Deployment**.
