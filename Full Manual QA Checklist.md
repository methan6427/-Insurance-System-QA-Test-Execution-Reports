# 🧪 Insurance System – Full Manual QA Checklist

----------

# ✅ 1. Smoke Test (Basic Stability)

## 🌐 Public Pages

- [ ]  Landing page loads
    
- [ ]  About page loads
    
-  [ ] Help page loads
    
- [ ]  Reset password page loads
    
-  [ ] No blank screen on any public route
    

## 🔐 Login Basic

-  [ ] Client login works
    
-  [ ] Manager login works
    
-  [ ] Medical Admin login works
    
-  [ ] Doctor login works
    
-  [ ] Pharmacist login works
    
-  [ ] Lab Tech login works
    
-  [ ] Radiologist login works
    
-  [ ] Logout works
    
-  [ ] After logout, protected pages are blocked
    

----------

# 🔐 2. Authentication & Session Tests

## Login Validation

-  [ ] Valid email + valid password works
    
-  [ ] Valid email + wrong password shows error
    
-  [ ] Empty fields show validation error
    
-  [ ] Email with spaces handled correctly
    
-  [ ] SQL injection attempt in login fails safely
    
-  [ ] Very long email input handled safely
    

## Session Handling

-  [ ] Refresh after login keeps session
    
-  [ ] Close browser & reopen behaves correctly
    
-  [ ] Token expiry redirects to login
    
-  [ ] Logout in one tab logs out other tabs
    
-  [ ] Browser back button after logout does not show dashboard
    

## Password Reset

-  [ ] Forgot password works with valid email
    
-  [ ] Forgot password with invalid email handled safely
    
-  [ ] Reset with invalid token fails
    
-  [ ] Weak password validation enforced
    

----------

# 🔒 3. Role-Based Access Control (RBAC)

## Client Access Tests

-  [ ] Client cannot access ManagerDashboard
    
-  [ ] Client cannot access MedicalAdminDashboard
    
-  [ ] Client cannot access CoordinationDashboard
    
-  [ ] Client cannot approve claims
    

## Manager Access Tests

-  [ ] Manager cannot access DoctorDashboard
    
-  [ ] Manager cannot access PharmacistDashboard
    

## Doctor Access Tests

-  [ ] Doctor cannot access Manager claims management
    
-  [ ] Doctor cannot access admin account creation
    

## URL Manipulation Tests

-  [ ] Direct URL access blocked for unauthorized roles
    
-  [ ] API calls blocked if role not allowed
    

----------

# 📄 4. Claims Flow Testing

## Client – Create Claim

-  [ ] Create claim with valid data
    
-  [ ] Create claim missing required fields
    
-  [ ] Upload valid file (jpg/pdf)
    
-  [ ] Upload unsupported file type
    
-  [ ] Upload large file (10MB+)
    
- [ ]  Upload file with special characters in name
    
-  [ ] Submit twice quickly (duplicate prevention)
    
-  [ ] View created claim in “My Claims”
    

## Manager – Review Claim

-  [ ] View all claims
    
-  [ ] Approve claim
    
-  [ ] Reject claim with reason
    
-  [ ] Claim status updates for client
    
-  [ ] Status remains correct after refresh
    

## Claim Status Consistency

-  [ ] No unknown status displayed
    
-  [ ] Client & manager see same status
    
-  [ ] Approved claims appear in reports
    

----------

# 🚨 5. Emergency Requests Flow

## Create Emergency Request

-  [ ] Create valid emergency request
    
-  [ ] Submit incomplete emergency request
    
-  [ ] View request in personal list
    

## Review Emergency Request

- [ ]  Manager can approve
    
-  [ ] Medical Admin can approve
    
-  [ ] Reject with reason
    
-  [ ] Status updates correctly everywhere
    

----------

# 📊 6. Manager Dashboard Tests

-  [ ] ManagerDashboard loads
    
-  [ ] PendingRequests page loads
    
-  [ ] PolicyManagement works
    
-  [ ] Provider registrations approve/reject
    
-  [ ] Admin account creation works
    
-  [ ] ManageNotifications works
    
-  [ ] ClaimsReport generates data
    
-  [ ] FinancialReport generates data
    
-  [ ] ConsultationPrices editable
    
-  [ ] DataImport works with valid file
    
-  [ ] DataImport fails safely with invalid file
    
-  [ ] CoverageManagement CRUD works
    
-  [ ] DoctorMedicineAssignment works
    
-  [ ] DoctorTestAssignment works
    

----------

# 🏥 7. Medical Admin Tests

-  [ ] MedicalAdminDashboard loads
    
-  [ ] MedicalClaimsReview works
    
-  [ ] MedicalDecisionsList works
    
-  [ ] PendingRequestsClients works
    
-  [ ] ClientListFinal loads
    
-  [ ] ChronicPatientsManagement add/edit/delete works
    
-  [ ] Emergency requests review works
    
-  [ ] Notifications work
    

----------

# 📋 8. Coordination Admin Tests

-  [ ] CoordinationDashboard loads
    
-  [ ] CoordinationPendingRequests works
    
-  [ ] ClientList loads
    
- [ ]  ClaimsList loads
    
-  [ ] ClaimsManage functions correctly
    
- [ ]  AddClaim works (if allowed)
    
-  [ ] Notifications work
    

----------

# 👤 9. Client Tests

- [ ]  ClientDashboard loads
    
-  [ ] Create claim works
    
-  [ ] View own claims works
    
-  [ ] Claim status updates after approval
    
-  [ ] Chat works
    
-  [ ] Profile updates save correctly
    

----------

# 👨‍⚕️ 10. Doctor Tests

-  [ ] DoctorDashboard loads
    
-  [ ] View assigned cases
    
-  [ ] Create medical report (if available)
    
-  [ ]  View emergency requests
    
-  [ ] Chat works
    

----------

# 💊 11. Pharmacist Tests

-  [ ] PharmacistDashboard loads
    
-  [ ] View prescriptions
    
-  [ ] Confirm prescription works
    
-  [ ] Reject prescription works
    
-  [ ] Search patient works
    

----------

# 🧪 12. Lab Tech Tests

-  [ ] LabDashboard loads
    
-  [ ] View lab requests
    
-  [ ] Upload lab results
    
-  [ ] Results downloadable
    

----------

# 🩻 13. Radiology Tests

-  [ ] RadiologyDashboard loads
    
-  [ ] View radiology requests
    
-  [ ] Upload imaging report
    
-  [ ] Download imaging report works
    

----------

# 💬 14. Chat & Real-Time

-  [ ] Send message client → manager
    
-  [ ] Receive message in real time
    
-  [ ] Refresh keeps chat history
    
-  [ ] No duplicate messages
    
-  [ ]  Empty message blocked
    
-  [ ] Network disconnect handled safely
    

----------

# 📎 15. File Upload Stress Tests

-  [ ]  Upload multiple files at once
    
-  [ ] Delete uploaded file (if allowed)
    
-  [ ] Cancel upload mid-way
    
-  [ ] Large file does not crash system
    
-  [ ] Uploaded files open correctly
    

----------

# 📱 16. UI & UX Stability

-  [ ] No blank screen on refresh
    
-  [ ] Back/forward navigation works
    
-  [ ] Loading indicators visible
    
-  [ ] Error messages user-friendly
    
-  [ ] Mobile layout usable
    
-  [ ] Forms accessible via keyboard (tab)
    

----------

# 🌐 17. Cross-Browser Testing

-  [ ] Chrome
    
-  [ ] Firefox
    
-  [ ] Edge
    
-  [ ] Incognito mode
    
-  [ ] Mobile view simulation
    

----------

# 🚨 18. Critical Production Checklist

-  [ ] No unauthorized role access possible
    
-  [ ] Claims lifecycle fully working
    
-  [ ] Emergency lifecycle fully working
    
-  [ ] File uploads stable
    
-  [ ]  Chat stable
    
-  [ ] No major crashes in dashboards
    
-  [ ] No inconsistent statuses
