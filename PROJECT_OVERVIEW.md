# IUST University Portal - Project Documentation

## Overview

The IUST University Portal is a full-stack web application designed to manage student council activities, events, FAQs, and student engagement at a university. It provides a platform for students to apply for council positions, for admins to manage events and duties, and for all users to access university-related FAQs. The system features role-based access control, automated duty assignment, and a chatbot for FAQ assistance.

---

## Data Flow & Architecture

- **Frontend**: Built with React and TypeScript, using Tailwind CSS for UI. Users interact via dashboards (student/admin), forms, and a chatbot.
- **Backend**: Node.js/Express.js REST API, with MongoDB for data storage. JWT-based authentication secures API endpoints.
- **Data Flow**:
  1. **Authentication**: Users register/login. JWT tokens are issued and used for protected API requests.
  2. **Student Actions**: Students can view their dashboard, apply for council positions, view assigned duties, and interact with the FAQ chatbot.
  3. **Admin Actions**: Admins access an admin dashboard to manage events, FAQs, council applications, and assign duties to council members.
  4. **Council Member Flow**: When a student's application is approved, they become a council member and can be assigned duties for events.
  5. **FAQ Chatbot**: All users can interact with a chatbot that answers questions using the FAQ database.

---

## User Roles & Permissions

### 1. **Student**
- **Register/Login**: Students can create an account using their university-issued student ID (validated against the system) and log in securely. This ensures only legitimate students access the portal.
- **Apply for Council**: Students can fill out and submit an application form to join the student council, specifying their motivation, experience, skills, and the position they are interested in (e.g., President, Secretary, Member, etc.).
- **View Own Application**: Students can track the status of their submitted council application (pending, approved, rejected) and see any feedback or comments provided by admins during the review process.
- **View Assigned Duties**: If a student becomes a council member, they can view a list of duties assigned to them for various events (e.g., managing event registration, handling publicity, coordinating logistics, technical support, decoration, etc.). Each duty includes details such as event name, role, status (pending, confirmed, declined, completed), and feedback from admins.
- **Update Duty Status**: Council members can confirm, decline, or mark their assigned duties as completed, and provide feedback or comments on their experience.
- **Interact with FAQ Chatbot**: All students can use the integrated chatbot to ask questions about university processes, events, or general information, receiving instant answers from the FAQ database.

### 2. **Council Member** (Student with `isCouncilMember: true`)
- **All Student Permissions**: Council members retain all standard student capabilities.
- **Assigned Duties**: Council members are assigned specific roles for university events (e.g., Event Coordinator, Volunteer, Registration Desk, Publicity, Technical Support, Decoration). For each event, they can:
  - View detailed duty descriptions and deadlines.
  - Confirm or decline assigned duties (with reasons).
  - Mark duties as completed and submit post-event feedback.
  - Request duty swaps with other council members (pending admin approval).
- **Event Proposals**: Council members can propose new events or initiatives for the student body, submitting details for admin review and approval.
- **Internal Communication**: Participate in internal discussions, receive announcements, and access shared documents related to council activities.
- **Attendance & Participation**: Mark attendance for council meetings and events, and view their participation history and performance analytics.

### 3. **Admin**
- **Full Access to Management Features**:
  - **Council Applications**:
    - View all submitted applications, review details, and provide feedback.
    - Approve or reject applications, triggering status updates and notifications to applicants.
    - Schedule interviews or additional review stages if needed.
  - **Event Management**:
    - Create, edit, or cancel university events.
    - Assign council members to specific duties/roles for each event (e.g., assigning a member as Event Coordinator, another for Registration, etc.).
    - Review event proposals submitted by council members and approve or request changes.
    - Set event budgets, approve expense reports, and manage event resources.
  - **Duty Management**:
    - Assign, reassign, or remove duties for council members.
    - Approve or deny duty swap requests between council members.
    - Monitor duty completion status and provide feedback or reminders.
    - View analytics on duty participation and performance.
  - **FAQ Management**:
    - Add, edit, or delete FAQ entries to keep the chatbot and FAQ section up to date.
    - Categorize FAQs and tag them for better searchability.
  - **Council Member Oversight**:
    - View a list of all current council members, their assigned duties, participation records, and performance analytics.
    - Send announcements, share documents, and communicate directly with council members.
  - **Student Engagement Tools**:
    - Create and manage polls, surveys, and volunteer signups for events.
    - Review feedback and suggestions from students and council members.
  - **Reporting & Analytics**:
    - Access dashboards and export reports on applications, events, duties, and council activities for transparency and improvement.

#### **Authorization Middleware**
- `authenticate`: Validates JWT and attaches user to request.
- `authorizeAdmin`: Restricts access to admin-only routes.
- `authorizeCouncilMember`: Restricts access to council member-only routes.

---

## Models

### 1. **User**
- `name`: String
- `email`: String (unique)
- `password`: String (hashed)
- `role`: 'student' | 'admin'
- `studentId`: String (unique, must be in ValidStudentId)
- `department`: String
- `year`: String
- `isCouncilMember`: Boolean (true if application approved)
- `profileImage`: String (optional)
- `createdAt`: Date

### 2. **Application** (Council Application)
- `student`: Reference to User
- `motivation`: String
- `experience`: String
- `skills`: [String]
- `position`: 'president' | 'vice-president' | 'secretary' | 'treasurer' | 'member'
- `status`: 'pending' | 'approved' | 'rejected'
- `reviewedBy`: Reference to User (admin)
- `reviewComments`: String
- `interests`: [String]
- `createdAt`: Date

### 3. **Duty**
- `event`: Reference to Event
- `student`: Reference to User (council member)
- `role`: 'coordinator' | 'volunteer' | 'registration' | 'publicity' | 'technical' | 'decoration'
- `status`: 'pending' | 'confirmed' | 'declined' | 'completed'
- `assignedBy`: Reference to User (admin)
- `feedback`: String
- `createdAt`: Date

### 4. **Event**
- `title`: String
- `description`: String
- `location`: String
- `date`: Date
- `startTime`: String
- `endTime`: String
- `organizer`: Reference to User (admin)
- `status`: 'upcoming' | 'ongoing' | 'completed' | 'cancelled'
- `image`: String (optional)
- `createdAt`: Date

### 5. **Faq**
- `question`: String (unique)
- `answer`: String
- `tags`: [String]
- `category`: 'admission' | 'hostel' | 'faculty' | 'examination' | 'general'
- `createdBy`: Reference to User (admin)
- `createdAt`: Date

### 6. **ValidStudentId**
- `studentId`: String (unique, used to validate registration)
- `createdAt`: Date

---

## Key Features
- **Role-based Dashboards**: Separate dashboards for students and admins.
- **Council Application Workflow**: Students apply, admins review, and upon approval, students become council members.
- **Automated Duty Assignment**: When events are created, duties are auto-assigned to council members.
- **FAQ Chatbot**: Users can ask questions and get answers from the FAQ database.
- **Secure Authentication**: JWT-based, with role and council member checks for sensitive actions.

---

## API Endpoints (Sample)
- `/api/auth/register` - Register new user
- `/api/auth/login` - Login
- `/api/applications` - CRUD for council applications
- `/api/duties` - CRUD for duties
- `/api/events` - CRUD for events
- `/api/faqs` - CRUD for FAQs
- `/api/users/council-members` - List all council members

---

## Conclusion
This portal streamlines student council management, event organization, and student engagement, providing a modern, secure, and user-friendly experience for both students and administrators at IUST University. 