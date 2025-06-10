```sql
-- Database Schema for EduGuide AI

-- 1. Students Table
CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    phone_number VARCHAR(20) UNIQUE,
    current_degree_type VARCHAR(100),
    current_stream VARCHAR(100),
    current_gpa_or_percentage DECIMAL(4,2),
    budget DECIMAL(12,2),
    preferred_countries TEXT[], -- Array of country names/codes
    preferred_intake_season VARCHAR(50),
    goals TEXT, -- Could be comma-separated or JSON
    resume_url VARCHAR(512),
    sop_draft_url VARCHAR(512),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. Scores Table
CREATE TABLE Scores (
    score_id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES Students(student_id) ON DELETE CASCADE,
    exam_name VARCHAR(50) NOT NULL, -- e.g., IELTS, TOEFL, GRE, GMAT, CAT
    score_value VARCHAR(50), -- Can be complex, e.g., "L:8, R:7.5, W:7, S:7.5" for IELTS or a single number
    overall_score DECIMAL(5,2), -- For exams with a single overall score
    score_date DATE,
    scorecard_url VARCHAR(512), -- Link to uploaded scorecard PDF/image
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE (student_id, exam_name)
);

-- 3. Universities Table
CREATE TABLE Universities (
    university_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    country VARCHAR(100) NOT NULL,
    city VARCHAR(100),
    type VARCHAR(50), -- Govt, Private
    description TEXT,
    website_url VARCHAR(512),
    logo_url VARCHAR(512),
    visa_friendliness_rating DECIMAL(3,1), -- e.g., 1-5 scale
    work_permit_stats TEXT, -- Description of post-study work options
    global_ranking_qs INTEGER,
    national_ranking_nirf INTEGER, -- For Indian universities
    is_blacklisted BOOLEAN DEFAULT FALSE,
    blacklist_reason TEXT
);

-- 4. Programs Table
CREATE TABLE Programs (
    program_id SERIAL PRIMARY KEY,
    university_id INTEGER NOT NULL REFERENCES Universities(university_id) ON DELETE CASCADE,
    program_name VARCHAR(255) NOT NULL, -- e.g., "MS in Computer Science"
    degree_type VARCHAR(50), -- MS, MTech, MBA, BBA etc.
    stream_discipline VARCHAR(100), -- e.g., Computer Science, AI, Management
    duration_months INTEGER,
    tuition_fee DECIMAL(10,2),
    fee_currency VARCHAR(10) DEFAULT 'USD',
    eligibility_criteria TEXT, -- Detailed criteria
    application_opens_date DATE,
    application_deadline_date DATE,
    mode_of_study VARCHAR(50), -- On-campus, Online, Hybrid
    program_url VARCHAR(512), -- Link to program page on university website
    scholarship_details TEXT,
    UNIQUE (university_id, program_name, degree_type)
);

-- 5. Loans Table (Bank Loan Products)
CREATE TABLE Loans (
    loan_product_id SERIAL PRIMARY KEY,
    bank_name VARCHAR(100) NOT NULL, -- e.g., SBI, ICICI, Prodigy Finance
    loan_name VARCHAR(255), -- e.g., "Global Ed-Vantage", "Study Abroad Loan"
    min_interest_rate DECIMAL(4,2),
    max_interest_rate DECIMAL(4,2),
    interest_rate_type VARCHAR(20), -- Fixed, Floating
    max_loan_amount DECIMAL(15,2),
    min_repayment_period_months INTEGER,
    max_repayment_period_months INTEGER,
    processing_fee_details VARCHAR(255),
    co_applicant_required BOOLEAN,
    collateral_requirements TEXT,
    moratorium_period_months INTEGER,
    eligibility_criteria_general TEXT, -- General criteria for this loan product
    supported_countries TEXT[], -- Countries of study this loan applies to
    bank_api_endpoint VARCHAR(512), -- If direct integration exists
    notes TEXT
);

-- 6. Applications Table (Student Applications to Programs)
CREATE TABLE Applications (
    application_id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES Students(student_id) ON DELETE CASCADE,
    program_id INTEGER NOT NULL REFERENCES Programs(program_id) ON DELETE CASCADE,
    university_id INTEGER NOT NULL REFERENCES Universities(university_id) ON DELETE CASCADE, -- Denormalized for easier querying
    application_status VARCHAR(50) DEFAULT 'Draft', -- Draft, Applied, Accepted, Rejected, Waitlisted
    date_applied DATE,
    decision_date DATE,
    sop_submitted_url VARCHAR(512), -- Link to the specific SOP used for this application
    lor_submitted_urls TEXT[], -- Array of LOR urls
    notes_by_student TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE (student_id, program_id)
);

-- 7. Reminders Table
CREATE TABLE Reminders (
    reminder_id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES Students(student_id) ON DELETE CASCADE,
    related_entity_type VARCHAR(50), -- e.g., 'Program', 'Scholarship', 'Exam', 'Application'
    related_entity_id INTEGER, -- e.g., program_id if type is 'Program', application_id if type is 'Application'
    reminder_title VARCHAR(255) NOT NULL,
    reminder_datetime TIMESTAMP NOT NULL,
    notification_sent_email BOOLEAN DEFAULT FALSE,
    notification_sent_whatsapp BOOLEAN DEFAULT FALSE,
    google_calendar_event_id VARCHAR(255),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Index Suggestions (add as needed for performance)
-- CREATE INDEX idx_students_email ON Students(email);
-- CREATE INDEX idx_scores_student_exam ON Scores(student_id, exam_name);
-- CREATE INDEX idx_universities_country ON Universities(country);
-- CREATE INDEX idx_programs_university_degree ON Programs(university_id, degree_type);
-- CREATE INDEX idx_programs_stream ON Programs(stream_discipline);
-- CREATE INDEX idx_applications_student_status ON Applications(student_id, application_status);
-- CREATE INDEX idx_reminders_student_datetime ON Reminders(student_id, reminder_datetime);

-- Relationships Summary:
-- Students 1 <--> N Scores (One student can have multiple scores)
-- Students 1 <--> N Applications (One student can apply to multiple programs)
-- Students 1 <--> N Reminders (One student can have multiple reminders)
-- Universities 1 <--> N Programs (One university offers multiple programs)
-- Universities 1 <--> N Applications (Many applications can be for one university - via Program)
-- Programs 1 <--> N Applications (One program can have multiple student applications)
```

**Relationships Summary (Conceptual):**

*   **Students to Scores:** One-to-Many
    *   A `Student` can have multiple `Scores` records (e.g., one for IELTS, one for GRE).
    *   Each `Score` belongs to exactly one `Student`.
    *   Implemented via `Scores.student_id` referencing `Students.student_id`.

*   **Students to Applications:** One-to-Many
    *   A `Student` can submit multiple `Applications`.
    *   Each `Application` is submitted by exactly one `Student`.
    *   Implemented via `Applications.student_id` referencing `Students.student_id`.

*   **Students to Reminders:** One-to-Many
    *   A `Student` can set multiple `Reminders`.
    *   Each `Reminder` belongs to exactly one `Student`.
    *   Implemented via `Reminders.student_id` referencing `Students.student_id`.

*   **Universities to Programs:** One-to-Many
    *   A `University` can offer multiple `Programs`.
    *   Each `Program` is offered by exactly one `University`.
    *   Implemented via `Programs.university_id` referencing `Universities.university_id`.

*   **Programs to Applications:** One-to-Many
    *   A `Program` can receive `Applications` from multiple students.
    *   Each `Application` targets exactly one `Program`.
    *   Implemented via `Applications.program_id` referencing `Programs.program_id`.

*   **Universities to Applications (Indirect):**
    *   While `Applications.university_id` is included for denormalization and easier querying (to quickly get all applications for a university without joining through Programs), the primary relationship is:
        *   A `University` can have many `Applications` *through* its `Programs`.
    *   This means an `Application` is linked to a `University` because it's linked to a `Program` which is, in turn, linked to that `University`.

*   **Loans Table:**
    *   The `Loans` table is a catalog of loan products. It is not directly linked via foreign keys to `Students` or `Applications` in this schema.
    *   If a student decides to take a loan, this information might be stored in a separate "Student_Loans" join table or referenced loosely by name/ID within the `Applications` or a student financial planning table (not currently in schema). For now, `Loans` serves as a browsable list.

This schema uses `SERIAL` for auto-incrementing primary keys (common in PostgreSQL, adapt if using another SQL dialect like MySQL's `AUTO_INCREMENT`). `ON DELETE CASCADE` is used on foreign keys where appropriate, meaning if a referenced record (e.g., a Student) is deleted, their related records (e.g., Scores, Applications, Reminders) will also be automatically deleted. This behavior should be carefully considered based on business rules (e.g., soft deletes might be preferred in some cases).
The `TEXT[]` type for arrays is specific to PostgreSQL. Other databases might use JSON or require join tables for similar functionality.
Basic indexes are suggested as comments; more specific indexing strategies would depend on common query patterns.
