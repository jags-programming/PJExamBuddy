✅ Core Entities and Table Structures
1. Users
Tracks registered users/students.

Field	Type	Description
UserID (PK)	UUID / Int	Unique identifier
Name	Text	Full name
Email	Text (unique)	Login / contact
PasswordHash	Text	For authentication
Role	Text	'Student', 'Admin'
RegisteredDate	DateTime	Date of registration

2. Subjects
Field	Type	Description
SubjectID (PK)	Int	Unique identifier
SubjectName	Text	e.g., Math, Physics

3. Topics
Field	Type	Description
TopicID (PK)	Int	Unique identifier
SubjectID (FK)	Int	Related subject
TopicName	Text	e.g., Algebra, Thermodynamics

4. SubTopics (Optional)
Field	Type	Description
SubTopicID (PK)	Int	Unique ID
TopicID (FK)	Int	Linked topic
SubTopicName	Text	e.g., Quadratic Equations

5. Exams
Field	Type	Description
ExamID (PK)	Int	e.g., JEE, SAT
ExamName	Text	Name of the exam

6. QuestionTypes
Field	Type	Description
QuestionTypeID (PK)	Int	Unique type ID
TypeName	Text	e.g., MCQ, True/False, Integer

7. Questions
Field	Type	Description
QuestionID (PK)	Int	Unique question ID
QuestionText	Text	Main content of the question
QuestionTypeID (FK)	Int	Type of the question
SubjectID (FK)	Int	Linked subject
TopicID (FK)	Int	Linked topic
SubTopicID (FK)	Int/null	Optional subtopic
ExamID (FK)	Int	e.g., JEE, SAT
DifficultyLevel	Int	1 (Easy), 2 (Medium), 3 (Hard)
Hint	Text	Optional hint
Explanation	Text	Explanation for answer
CreatedBy (FK)	Int	UserID of creator (admin/teacher)
CreatedDate	DateTime	Date added
IsActive	Boolean	Enable/disable question

8. Options (for MCQs)
Field	Type	Description
OptionID (PK)	Int	Unique ID
QuestionID (FK)	Int	Linked to a question
OptionText	Text	Option text
IsCorrect	Boolean	Indicates if this is the correct one

Note: For other question types (integer, short answer), Options table may be unused.

9. Tests
Field	Type	Description
TestID (PK)	Int	Unique ID
ExamID (FK)	Int	For which exam
TestName	Text	e.g., “JEE Mock Test 1”
Duration	Int	In minutes
CreatedBy	Int (FK)	UserID
CreatedDate	DateTime	When test was created

10. TestSections
Field	Type	Description
SectionID (PK)	Int	Unique section
TestID (FK)	Int	Linked test
Name	Text	e.g., Physics Section
SubjectID (FK)	Int	For filtering questions
QuestionCount	Int	No. of questions to pull
DifficultyLevel	Int/null	Optional filter for difficulty

11. TestQuestions
This records which question appears in which test/section.

Field	Type	Description
TestQuestionID	Int	Primary Key
TestID (FK)	Int	Linked test
SectionID (FK)	Int	From which section
QuestionID (FK)	Int	From question bank
SequenceNumber	Int	Order in test

12. TestAttempts
Field	Type	Description
AttemptID (PK)	Int	Unique attempt ID
TestID (FK)	Int	Which test was attempted
UserID (FK)	Int	Who took it
StartTime	DateTime	Start of test
EndTime	DateTime	End time
Score	Float	Final score
Completed	Boolean	Whether test was finished

13. AttemptAnswers
Field	Type	Description
AttemptAnswerID	Int	PK
AttemptID (FK)	Int	Attempt reference
QuestionID (FK)	Int	Question that was attempted
SelectedOptionID	Int/null	OptionID selected (if applicable)
AnswerText	Text	If subjective or integer answer
IsCorrect	Boolean	Based on evaluation logic
TimeSpentSeconds	Int	Time spent on question

✅ Relationships Overview
Question ←→ Options (1:M)

Test ←→ TestSections (1:M)

TestSections ←→ TestQuestions (1:M)

Test ←→ TestQuestions (1:M)

Question ←→ TestQuestions (1:M)

Test ←→ TestAttempts (1:M)

TestAttempts ←→ AttemptAnswers (1:M)

AttemptAnswers ←→ Question (M:1)

🔧 Optional Tables
Tags (for tagging questions)

Resources (linked to questions, e.g., images, diagrams)

Reports (for analytics: average score, topic performance)

🧠 Next Steps


SQL scripts for these tables

Sample data inserts

Entity Relationship (ER) diagram

Code structure (backend/frontend)

Desktop app architecture suggestions (e.g., Electron, .NET, Python + Tkinter/PyQt)


✅ Common Digital Test Question Types (With Examples)
Type	Description	Example Exams
1. Single Correct MCQ	4 or 5 options, only one is correct	JEE Main, NEET, SAT
2. Multiple Correct MCQ	More than one correct answer	JEE Advanced
3. Integer / Numeric	Answer is a number, typed in (e.g., between 0 to 99)	JEE Advanced
4. True/False	Statement is either true or false	Olympiads, Logical Reasoning
5. Match the Following	Columns A and B to match	JEE Advanced
6. Assertion & Reason	Two statements (Assertion, Reason) with logic-based correctness and link	NEET, UPSC
7. Fill in the Blanks	User enters a word or short phrase	School level, SAT
8. Comprehension Based	Passage + several questions based on it	JEE Advanced, SAT Reading
9. Matrix Match	Each row in column A matches one or more in column B	JEE Advanced
10. Paragraph / Subjective	Requires long written answers (rare in digital tests, but possible)	Optional

🧩 How to Represent These in  Schema
Add a QuestionType table:
sql
Copy
Edit
QuestionTypeID | TypeName              | Description
---------------|-----------------------|-----------------------------------
1              | SingleCorrectMCQ      | One correct out of many options
2              | MultipleCorrectMCQ    | Multiple correct options
3              | Integer               | User types in a number
4              | TrueFalse             | True or False
5              | AssertionReason       | Assertion/Reason with logic
6              | MatchTheFollowing     | Match two lists
7              | Comprehension         | Text + multiple questions
8              | FillInTheBlank        | User fills text
9              | MatrixMatch           | Matrix style matching
10             | Subjective            | Long answer (essay)
Then structure questions dynamically:
Options table can have a Correct = true/false column

For AssertionReason, Options may include combinations like:

A: True, R: True, R explains A

A: True, R: True, R doesn't explain A

Integer/Fill in the Blank: Skip the Options table, use a CorrectAnswer field in Questions

Match the Following / Matrix Match: Use a child table MatchPairs(QuestionID, ColumnA, ColumnB, IsCorrect)

Comprehension: Store passage in parent question, and link child questions to it via ParentQuestionID

🔧 Example Design Adjustments
In Questions table:

| QuestionID | QuestionText | QuestionTypeID | CorrectAnswer | ParentQuestionID |
CorrectAnswer: For numeric, fill-in-the-blank, or True/False

ParentQuestionID: Used for comprehension/paragraph questions

In Options table:

| OptionID | QuestionID | OptionText | IsCorrect |
For multiple correct MCQs, more than one IsCorrect = true

For True/False, two rows: True, False, only one marked correct

For Match the Following:

CREATE TABLE MatchPairs (
  MatchPairID INT PRIMARY KEY,
  QuestionID INT,
  ColumnA TEXT,
  ColumnB TEXT,
  IsCorrect BOOLEAN
);
🎯 Summary
You’ll want to:

Design the database to support flexible types

Represent each question type’s structure appropriately

Allow dynamic rendering and evaluation in the application based on type


🧱 FINAL DATABASE SCHEMA FOR DIGITAL TEST PREP APPLICATION
🔹 1. Subjects

SubjectID (PK)
SubjectName          -- e.g. Mathematics, Physics
🔹 2. Topics

TopicID (PK)
SubjectID (FK)
TopicName            -- e.g. Algebra, Thermodynamics
🔹 3. SubTopics (optional)

SubTopicID (PK)
TopicID (FK)
SubTopicName         -- e.g. Quadratic Equations
🔹 4. Exams

ExamID (PK)
ExamName             -- e.g. JEE, SAT
🔹 5. QuestionTypes

QuestionTypeID (PK)
TypeName             -- SingleCorrectMCQ, MultipleCorrectMCQ, Integer, etc.
Description
🔹 6. Questions

QuestionID (PK)
QuestionText
QuestionTypeID (FK)
SubjectID (FK)
TopicID (FK)
SubTopicID (FK, nullable)
ExamID (FK)
DifficultyLevel      -- Easy, Medium, Hard
Hint (nullable)
Explanation (nullable)
CorrectAnswer (nullable)   -- For Integer, Fill-in-the-Blank, True/False
ParentQuestionID (nullable) -- For comprehension-style grouping
🔹 7. Options (used only for MCQs, AssertionReason, True/False)

OptionID (PK)
QuestionID (FK)
OptionText
IsCorrect (Boolean)
OptionLabel           -- e.g. A, B, C, D
🔹 8. MatchPairs (used only for Match the Following / Matrix Match)

MatchPairID (PK)
QuestionID (FK)
ColumnA
ColumnB
IsCorrect (Boolean)
🔹 9. Tests

TestID (PK)
TestName
ExamID (FK)
CreatedByUserID (FK)
CreatedDate
DurationMinutes
🔹 10. TestSections

SectionID (PK)
TestID (FK)
SectionName              -- e.g. Physics, Verbal
TimeLimit (nullable)
Instructions (nullable)
🔹 11. TestQuestions

TestQuestionID (PK)
SectionID (FK)
QuestionID (FK)
Marks
NegativeMarks
QuestionOrder
🔹 12. Users

UserID (PK)
Name
Email
PasswordHash
Role                    -- Admin, Student, etc.
🔹 13. TestAttempts

AttemptID (PK)
UserID (FK)
TestID (FK)
StartTime
EndTime
Score
🔹 14. UserAnswers

UserAnswerID (PK)
AttemptID (FK)
QuestionID (FK)
SelectedOptionIDs (array or separate rows)  -- For MCQs
TypedAnswer (nullable)                      -- For numeric, fill-in-the-blank
MatchedPairs (nullable JSON or reference)   -- For Match type
IsCorrect
MarksObtained
✅ Special Notes
CorrectAnswer in Questions is for numeric or text answers.

Options is flexible to support multiple correct or True/False.

MatchPairs gives full support to Match the Following and Matrix Match types.

ParentQuestionID allows you to link comprehension passages to their sub-questions.

UserAnswers supports:

MCQs via selected options

Integer via typed answer

Match-type via MatchedPairs JSON or join table

📌 Example: How it Works for Different Types
Question Type	Used Tables
Single MCQ	Questions + Options (1 IsCorrect)
Multiple MCQ	Questions + Options (many correct)
Integer	Questions.CorrectAnswer
Assertion & Reason	Questions + 4 special Options
Match the Following	Questions + MatchPairs
Comprehension	Questions (one parent, many children)
True/False	Questions.CorrectAnswer or 2 options
Subjective (optional)	Questions, evaluate manually


🧾 DDL Script

-- 1. Subjects
CREATE TABLE Subjects (
    SubjectID INT PRIMARY KEY AUTO_INCREMENT,
    SubjectName VARCHAR(100) NOT NULL
);

-- 2. Topics
CREATE TABLE Topics (
    TopicID INT PRIMARY KEY AUTO_INCREMENT,
    SubjectID INT NOT NULL,
    TopicName VARCHAR(100) NOT NULL,
    FOREIGN KEY (SubjectID) REFERENCES Subjects(SubjectID)
);

-- 3. SubTopics (optional)
CREATE TABLE SubTopics (
    SubTopicID INT PRIMARY KEY AUTO_INCREMENT,
    TopicID INT NOT NULL,
    SubTopicName VARCHAR(100) NOT NULL,
    FOREIGN KEY (TopicID) REFERENCES Topics(TopicID)
);

-- 4. Exams
CREATE TABLE Exams (
    ExamID INT PRIMARY KEY AUTO_INCREMENT,
    ExamName VARCHAR(100) NOT NULL
);

-- 5. Question Types
CREATE TABLE QuestionTypes (
    QuestionTypeID INT PRIMARY KEY AUTO_INCREMENT,
    TypeName VARCHAR(100) NOT NULL,
    Description TEXT
);

-- 6. Questions
CREATE TABLE Questions (
    QuestionID INT PRIMARY KEY AUTO_INCREMENT,
    QuestionText TEXT NOT NULL,
    QuestionTypeID INT NOT NULL,
    SubjectID INT NOT NULL,
    TopicID INT NOT NULL,
    SubTopicID INT,
    ExamID INT NOT NULL,
    DifficultyLevel ENUM('Easy', 'Medium', 'Hard'),
    Hint TEXT,
    Explanation TEXT,
    CorrectAnswer TEXT, -- for numeric, fill-in, or true/false
    ParentQuestionID INT,
    FOREIGN KEY (QuestionTypeID) REFERENCES QuestionTypes(QuestionTypeID),
    FOREIGN KEY (SubjectID) REFERENCES Subjects(SubjectID),
    FOREIGN KEY (TopicID) REFERENCES Topics(TopicID),
    FOREIGN KEY (SubTopicID) REFERENCES SubTopics(SubTopicID),
    FOREIGN KEY (ExamID) REFERENCES Exams(ExamID),
    FOREIGN KEY (ParentQuestionID) REFERENCES Questions(QuestionID)
);

-- 7. Options
CREATE TABLE Options (
    OptionID INT PRIMARY KEY AUTO_INCREMENT,
    QuestionID INT NOT NULL,
    OptionText TEXT NOT NULL,
    OptionLabel VARCHAR(5), -- A, B, C, etc.
    IsCorrect BOOLEAN NOT NULL,
    FOREIGN KEY (QuestionID) REFERENCES Questions(QuestionID)
);

-- 8. Match Pairs
CREATE TABLE MatchPairs (
    MatchPairID INT PRIMARY KEY AUTO_INCREMENT,
    QuestionID INT NOT NULL,
    ColumnA TEXT NOT NULL,
    ColumnB TEXT NOT NULL,
    IsCorrect BOOLEAN NOT NULL,
    FOREIGN KEY (QuestionID) REFERENCES Questions(QuestionID)
);

-- 9. Users
CREATE TABLE Users (
    UserID INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(100) NOT NULL,
    Email VARCHAR(150) UNIQUE NOT NULL,
    PasswordHash TEXT NOT NULL,
    Role ENUM('Student', 'Admin') DEFAULT 'Student'
);

-- 10. Tests
CREATE TABLE Tests (
    TestID INT PRIMARY KEY AUTO_INCREMENT,
    TestName VARCHAR(150) NOT NULL,
    ExamID INT NOT NULL,
    CreatedByUserID INT NOT NULL,
    CreatedDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    DurationMinutes INT,
    FOREIGN KEY (ExamID) REFERENCES Exams(ExamID),
    FOREIGN KEY (CreatedByUserID) REFERENCES Users(UserID)
);

-- 11. Test Sections
CREATE TABLE TestSections (
    SectionID INT PRIMARY KEY AUTO_INCREMENT,
    TestID INT NOT NULL,
    SectionName VARCHAR(100) NOT NULL,
    TimeLimit INT,
    Instructions TEXT,
    FOREIGN KEY (TestID) REFERENCES Tests(TestID)
);

-- 12. Test Questions
CREATE TABLE TestQuestions (
    TestQuestionID INT PRIMARY KEY AUTO_INCREMENT,
    SectionID INT NOT NULL,
    QuestionID INT NOT NULL,
    Marks FLOAT,
    NegativeMarks FLOAT,
    QuestionOrder INT,
    FOREIGN KEY (SectionID) REFERENCES TestSections(SectionID),
    FOREIGN KEY (QuestionID) REFERENCES Questions(QuestionID)
);

-- 13. Test Attempts
CREATE TABLE TestAttempts (
    AttemptID INT PRIMARY KEY AUTO_INCREMENT,
    UserID INT NOT NULL,
    TestID INT NOT NULL,
    StartTime TIMESTAMP,
    EndTime TIMESTAMP,
    Score FLOAT,
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (TestID) REFERENCES Tests(TestID)
);

-- 14. User Answers
CREATE TABLE UserAnswers (
    UserAnswerID INT PRIMARY KEY AUTO_INCREMENT,
    AttemptID INT NOT NULL,
    QuestionID INT NOT NULL,
    SelectedOptionIDs TEXT, -- JSON string or CSV (e.g., "1,3") for MCQs
    TypedAnswer TEXT, -- For numeric, fill-in-the-blank
    MatchedPairs TEXT, -- JSON or description of matchings
    IsCorrect BOOLEAN,
    MarksObtained FLOAT,
    FOREIGN KEY (AttemptID) REFERENCES TestAttempts(AttemptID),
    FOREIGN KEY (QuestionID) REFERENCES Questions(QuestionID)
);
🔧 Notes
Use TEXT or VARCHAR with appropriate lengths depending on your DB engine.

SelectedOptionIDs and MatchedPairs can use JSON if your database supports it (e.g., PostgreSQL, MySQL 5.7+).

For secure password handling, store hashed passwords, not plain text.

You can index QuestionID, UserID, etc., for faster lookup depending on performance needs.


✅ Factors to Consider
Factor	Best Choice
Single-user or multi-user	SQLite for single-user, PostgreSQL or MySQL for multi-user
Internet access	No: SQLite, Yes: PostgreSQL/MySQL
Concurrent access	High: PostgreSQL; Low: SQLite
Data volume	Low to medium: SQLite; Large: PostgreSQL
Ease of setup	SQLite (zero-config)
Desktop install footprint	SQLite (lightweight)
Need for advanced features	PostgreSQL

Flat file structure for initial testing:
If you are starting with a single Excel file to manage your question bank in a flat structure, here is a well-organized set of columns that can support a wide range of questions — including MCQs, true/false, numerical, and assertion-reason — for Physics, Chemistry, and Mathematics:

✅ Recommended Columns in Excel
Column Name	Description
QuestionID	Unique ID for each question (e.g., 101, JEE2024PHY001)
Exam	Target exam (e.g., JEE, NEET, Olympiad)
Subject	Physics, Chemistry, Mathematics
Topic	Broad topic (e.g., Mechanics, Thermodynamics, Algebra)
SubTopic	(Optional) More specific breakdown
Type	MCQ-Single, MCQ-Multiple, TrueFalse, Numerical, AssertionReason
Difficulty	Easy, Medium, Hard
QuestionText	The question (support LaTeX syntax for formulas)
OptionA	First answer option (can be blank for non-MCQs)
OptionB	Second option
OptionC	Third option
OptionD	Fourth option
CorrectOptions	One or more correct answers (e.g., A, A,B, True, 5.67, A:True;R:True;Cause)
Explanation	Explanation or solution (can use LaTeX)
Hint	Optional hint
Tags	Comma-separated keywords for filtering (e.g., kinematics, displacement, motion)
ImagePath (opt.)	If the question has an image, relative or full path (optional)
CreatedBy (opt.)	Author or contributor name
IsActive (opt.)	Yes or No — for soft-deletion or status filtering
