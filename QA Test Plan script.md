# 0) What you’re testing (white-box map)

## Roles (from frontend + backend security)

You have these user roles and dashboards:

-   **Insurance Manager**
    
-   **Medical Admin**
    
-   **Coordination Admin**
    
-   **Insurance Client (Customer)**
    
-   **Doctor**
    
-   **Pharmacist**
    
-   **Lab Tech**
    
-   **Radiologist**
    

## Main feature areas (confirmed from routes + backend controllers)

-   Authentication (login, register variants, forgot/reset password, verify email, logout, “me”)
    
-   Claims (client creates claim + uploads files; manager reviews/approves/rejects)
    
-   Emergency Requests (create request; manager/medical admin approve/reject; doctor views)
    
-   Coverage / Policy management / Provider registrations / Provider price lists
    
-   Reports (claims report, financial report, coordination reports)
    
-   Notifications
    
-   Chat (REST + WebSocket chat)
    
-   Assignments (doctor medicine / doctor test assignment)
    
-   Data import (manager data import)
    
-   Chronic patients management (medical admin)
    

----------

# 1) Test preparation (do this before any testing)

## 1.1 Test accounts you MUST have

Ask the project owner/admin to provide **one account per role**:

-   manager@test.com (Insurance Manager)
    
-   medicaladmin@test.com (Medical Admin)
    
-   coordination@test.com (Coordination Admin)
    
-   client@test.com (Insurance Client)
    
-   doctor@test.com (Doctor)
    
-   pharmacist@test.com (Pharmacist)
    
-   lab@test.com (Lab Tech)
    
-   radiology@test.com (Radiologist)
    

For each account, ensure:

-   You can log in
    
-   It lands you on the correct dashboard
    
-   You know the password
    
-   It has enough seeded data to view lists (or you can create data as client)
    

## 1.2 Test environment checklist

-   Use **Chrome** + also test **Firefox**
    
-   Open **Incognito** sometimes (to test fresh sessions)
    
-   Turn on screen recording (for bug evidence)
    
-   Prepare a folder of test files:
    
    -   small jpg/png
        
    -   pdf
        
    -   a large file (10–25MB)
        
    -   a file with weird name: `x x(1)_عربي_#@!.pdf`
        

## 1.3 What to record for every failure (super important)

When something breaks, record:

-   What role you’re logged in as
    
-   Exact page/route
    
-   What you clicked
    
-   What you expected
    
-   What happened instead
    
-   Screenshot/video
    
-   Time + browser
    

----------

# 2) “Smoke test” (10 minutes) — confirm system isn’t broken

Do this in order:

### 2.1 Landing pages (public)

1.  Open `/`  
    ✅ Expected: redirects to Landing if not logged in, or to dashboard if logged in.
    
2.  Open `/LandingPage`, `/About`, `/Help`  
    ✅ Expected: load without crashes.
    
3.  Open `/reset-password`  
    ✅ Expected: form renders.
    

### 2.2 Login (basic)

4.  Log in as **client**  
    ✅ Expected: goes to **ClientDashboard**
    
5.  Log out  
    ✅ Expected: session cleared; going back shouldn’t show dashboard.
    
6.  Log in as **manager**  
    ✅ Expected: goes to **ManagerDashboard**
    

If anything fails here → don’t continue; fix auth first.

----------

# 3) Authentication & session test plan (deep)

## 3.1 Login validation

For each role account:

-   Try correct email + correct password → ✅ should log in
    
-   Try correct email + wrong password → ✅ should show error message (no crash)
    
-   Try empty fields → ✅ shows validation
    
-   Try email with spaces `" client@test.com "` → ✅ trims or errors nicely
    

## 3.2 Session persistence

-   Log in → refresh page (F5) → ✅ still logged in
    
-   Close tab, open site again → ✅ still logged in OR logs out consistently (whatever intended)
    
-   Wait idle 10–30 minutes → try navigating → ✅ should handle token expiry cleanly (redirect to login)
    

## 3.3 Logout

-   Logout → hit browser Back → ✅ should NOT show protected pages
    
-   Logout in one tab, keep another open → refresh other tab → ✅ should be logged out
    

## 3.4 Forgot / Reset password

-   Use forgot password with valid email → ✅ success message
    
-   Use forgot password with invalid email → ✅ safe message (should NOT leak whether account exists)
    
-   Reset with invalid token → ✅ error
    
-   Reset with weak password → ✅ validation (if exists)
    

## 3.5 Verify Email

-   Verify email endpoint exists → your UI might not expose it; still test flow if UI supports it.
    

----------

# 4) Role-based access (security testing, manual)

This is critical “white-box” testing.

## 4.1 Route access control (direct URL attack)

While logged in as **Client**, manually type these URLs:

-   `/ManagerDashboard`
    
-   `/MedicalAdminDashboard`
    
-   `/CoordinationDashboard`
    
-   `/DoctorDashboard`
    

✅ Expected: blocked (redirect/unauthorized screen).  
Do the same for other roles (Doctor trying to open Manager pages, etc.)

## 4.2 Feature access control

Example checks:

-   Client should not be able to approve/reject claims.
    
-   Doctor should not be able to access manager reports.
    
-   Coordination Admin should not see manager-only admin registration pages unless intended.
    

If any role can access the wrong dashboard → **P0 security bug**.

----------

# 5) Claims testing (end-to-end flows)

White-box note: backend includes these claim actions:

-   Client: create claim, list own claims
    
-   Manager: list all claims, approve/reject
    
-   Both: view claim by id
    

## 5.1 Client creates a claim (happy path)

Role: **Client**

1.  Go to Claim creation page (`/AddClaim` if client has access, otherwise from client dashboard)
    
2.  Fill claim form with valid data
    
3.  Upload 1–3 attachments (jpg/pdf)
    
4.  Submit
    

✅ Expected:

-   Claim is created
    
-   You see it in “my claims”
    
-   Attachments preview or download works (if UI supports)
    

## 5.2 Client create claim (negative cases)

Try:

-   Missing required fields
    
-   Very long text
    
-   Upload unsupported file type (e.g. `.exe`)  
    ✅ Expected: rejected safely
    
-   Upload huge file  
    ✅ Expected: proper error (not silent fail)
    
-   Submit twice quickly  
    ✅ Expected: no duplicate or clear handling
    

## 5.3 Manager reviews claim (approval path)

Role: **Insurance Manager**

1.  Open claims list (manager pages: `/Manager/ClaimsManage` or similar)
    
2.  Open the claim created by client
    
3.  Approve it
    

✅ Expected:

-   Status changes
    
-   Client view updates (immediately or after refresh)
    
-   Approved claim appears in reports (if applicable)
    

## 5.4 Manager rejects claim (rejection path)

Role: **Insurance Manager**

1.  Reject a claim
    
2.  Enter rejection reason (if UI asks)
    

✅ Expected:

-   Status changes to rejected
    
-   Client sees rejection reason (if intended)
    

## 5.5 Claim status mismatch test (IMPORTANT)

Your frontend and backend describe **slightly different status lifecycles**.  
So you MUST test:

-   Does UI show a status that backend doesn’t support?
    
-   Does backend send a status UI can’t render?
    

How to test manually:

1.  Create claim (client)
    
2.  Approve claim (manager)
    
3.  Refresh both client + manager pages  
    ✅ Expected: status display consistent everywhere.  
    If you see “unknown status” or blank status → **P0 bug**.
    

----------

# 6) Emergency Requests testing (end-to-end flows)

Backend supports:

-   Create emergency request
    
-   Approve/reject by Manager or Medical Admin
    
-   Doctor views some lists
    

## 6.1 Create emergency request

Role: (depends on UI—often Client or Doctor)

1.  Create new emergency request with valid info
    
2.  Submit
    

✅ Expected:

-   Request appears in the creator’s “my requests”
    
-   Appears in manager/medical admin review list
    

## 6.2 Approve emergency request

Role: **Medical Admin** and **Manager**

1.  Open emergency requests page
    
2.  Approve a pending request
    

✅ Expected:

-   Creator sees approved status
    
-   It disappears from “pending” list
    

## 6.3 Reject emergency request

Role: **Medical Admin** and **Manager**  
Reject with reason.

✅ Expected:

-   Reason stored + visible if intended
    

## 6.4 Workflow mismatch test

Same as claims: ensure no “ghost statuses” appear.

----------

# 7) Dashboards by role (full flow checklist)

Below are the **flows you should run for each role**. Even if you don’t understand the feature, you can test it by checking that pages load, lists work, actions succeed/fail properly, and permissions are correct.

## 7.1 Insurance Manager flows

Routes to test:

-   `/ManagerDashboard`
    
-   `/PendingRequests`
    
-   `/PolicyManagement`
    
-   `/PendingProviderRegistrations`
    
-   `/AdminRegisterAccounts`
    
-   `/ManageNotifications`
    
-   `/ProviderPriceList`
    
-   `/ClaimsReport`
    
-   `/FinancialReport`
    
-   `/Profile`
    
-   `/Manager/MedicalClaimsReview`
    
-   `/Manager/MedicalDecisionsList`
    
-   `/Manager/ClaimsManage`
    
-   `/Manager/EmergencyRequests`
    
-   `/Manager/ConsultationPrices`
    
-   `/Manager/DataImport`
    
-   `/Manager/CoverageManagement`
    
-   `/Manager/DoctorMedicineAssignment`
    
-   `/Manager/DoctorTestAssignment`
    

Test steps for EACH page:

1.  Page loads (no blank screen)
    
2.  Lists show data or “empty state”
    
3.  Search/filter/sort works (if present)
    
4.  Pagination works (if present)
    
5.  View details works
    
6.  Any action buttons:
    
    -   success path
        
    -   failure path (try invalid input)
        
7.  Refresh page preserves state or resets cleanly
    
8.  No unauthorized access errors inside manager account
    

Special manager tests:

-   Provider registration approvals/rejections
    
-   Data import: try correct file + wrong file type
    
-   Coverage management: create/edit/delete (if present)
    
-   Reports: export/download if exists
    

## 7.2 Medical Admin flows

Routes:

-   `/MedicalAdminDashboard`
    
-   `/MedicalClaimsReview`
    
-   `/MedicalDecisionsList`
    
-   `/PendingRequestsClients`
    
-   `/ClientListFinal`
    
-   `/MedicalAdminProfile`
    
-   `/MedicalAdminNotifications`
    
-   `/MedicalAdminEmergencyRequests`
    
-   `/ChronicPatientsManagement`
    

Test Chronic patients:

-   Add chronic condition
    
-   Update it
    
-   Remove it
    
-   Ensure patient list reflects changes
    

## 7.3 Coordination Admin flows

Routes:

-   `/CoordinationDashboard`
    
-   `/CoordinationProfile`
    
-   `/CoordinationNotifications`
    
-   `/CoordinationPendingRequests`
    
-   `/ClientList`
    
-   `/ClaimsList`
    
-   `/ClaimsManage`
    
-   `/AddClaim` (coordination can access too)
    

Key coordination tests:

-   Create claim on behalf of client (if UI supports)
    
-   Manage claims list (view vs edit permissions)
    
-   Pending requests: approve/reject flows
    

## 7.4 Client flows

Routes:

-   `/ClientDashboard`
    
-   `/Chat` (if available)
    
-   Claim create (`/AddClaim` if client route shows it)
    
-   “My claims” list (whatever UI includes)
    

Client tests:

-   Create claim
    
-   View claim
    
-   Upload docs
    
-   Check status updates after manager action
    
-   Chat with provider/admin (if chat exists)
    

## 7.5 Doctor flows

Route:

-   `/DoctorDashboard`
    

Doctor tests (typical):

-   View assigned patients/requests
    
-   Create medical records/reports (if UI supports)
    
-   View emergency requests list for doctor (backend supports doctor views)
    
-   Chat
    

## 7.6 Pharmacist flows

Route:

-   `/PharmacistDashboard`
    

Test:

-   View prescriptions/requests
    
-   Confirm/deny actions (if present)
    
-   Search patient / medicine
    

## 7.7 Lab Tech flows

Route:

-   `/LabDashboard`
    

Test:

-   View lab test requests
    
-   Upload results (if present)
    
-   Status updates
    

## 7.8 Radiologist flows

Route:

-   `/RadiologyDashboard`
    

Test:

-   View radiology requests
    
-   Upload imaging report (if present)
    
-   Validate that upload can be opened/downloaded
    

----------

# 8) Chat testing (REST + WebSocket)

Backend has chat REST endpoints and a websocket endpoint.

## 8.1 Basic chat flow

1.  Open Chat page as client
    
2.  Send message to manager/doctor (whatever UI supports)
    
3.  Open chat as the other role in another browser/incognito  
    ✅ Expected: message appears
    

## 8.2 Real-time update

-   Keep both chats open
    
-   Send messages quickly  
    ✅ Expected: no duplicates, ordering correct, no disconnect spam
    

## 8.3 Chat failure modes

-   Turn off internet mid chat  
    ✅ Expected: reconnect or clear error message
    
-   Send empty message  
    ✅ Expected: blocked
    

----------

# 9) File upload testing (critical)

Wherever uploads exist (claims, radiology, lab):  
Test matrix:

-   jpg/png
    
-   pdf
    
-   large file
    
-   weird filename with spaces + Arabic
    
-   upload multiple files
    
-   delete uploaded file (if possible)
    
-   upload then cancel form  
    Expected:
    
-   consistent success/failure messages
    
-   no silent failures
    
-   no broken download links
    

----------

# 10) UI/UX reliability tests (manual but important)

Do these everywhere:

-   Back/forward navigation doesn’t break
    
-   Refresh doesn’t cause blank screen
    
-   Loading spinners appear during API calls
    
-   Empty states are clear (“No claims yet”)
    
-   Error messages are user-friendly
    
-   Mobile responsive (at least login + main dashboards)
    
-   Accessibility basics:
    
    -   Can you tab through forms?
        
    -   Is text readable?
        
    -   Buttons labeled?
        

----------

# 11) Production readiness “exit criteria”

You can call it “ready to production” only if:

### P0 (must be zero)

-   Any user can access another role’s pages (RBAC bypass)
    
-   Login broken / session loops / cannot logout
    
-   Claim create or manager approve/reject broken
    
-   Emergency request create/review broken
    
-   Blank screen / crash in dashboards
    
-   File upload causes server crash or lost data
    

### P1 (can ship only if accepted)

-   Bad validation messages
    
-   Minor UI rendering glitches
    
-   Non-critical report export issues
    
-   Some pages missing empty states
    

### P2 (nice-to-have)

-   Alignment, spacing, typography issues
    
-   Minor performance optimizations
