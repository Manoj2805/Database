# Database

-- Create Database
CREATE DATABASE SkillTaskAllocationDB;
USE SkillTaskAllocationDB;

-- Role Table
CREATE TABLE Role (
    role_id INT PRIMARY KEY AUTO_INCREMENT,
    role_name VARCHAR(50) UNIQUE NOT NULL
);

-- Permission Table
CREATE TABLE Permission (
    permission_id INT PRIMARY KEY AUTO_INCREMENT,
    permission_name VARCHAR(100) UNIQUE NOT NULL
);

-- RolePermission (Many-to-Many)
CREATE TABLE RolePermission (
    role_id INT,
    permission_id INT,
    PRIMARY KEY (role_id, permission_id),
    FOREIGN KEY (role_id) REFERENCES Role(role_id),
    FOREIGN KEY (permission_id) REFERENCES Permission(permission_id)
);

-- User Table
CREATE TABLE User (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password VARCHAR(100) NOT NULL,
    role_id INT,
    manager_id INT,
    FOREIGN KEY (role_id) REFERENCES Role(role_id),
    FOREIGN KEY (manager_id) REFERENCES User(user_id)
);

-- Skill Table
CREATE TABLE Skill (
    skill_id INT PRIMARY KEY AUTO_INCREMENT,
    skill_name VARCHAR(100) NOT NULL,
    skill_description VARCHAR(255)
);

-- Task Table
CREATE TABLE Task (
    task_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    description TEXT,
    required_skill_id INT,
    assigned_to INT,
    deadline DATE,
    created_by INT,
    FOREIGN KEY (required_skill_id) REFERENCES Skill(skill_id),
    FOREIGN KEY (assigned_to) REFERENCES User(user_id),
    FOREIGN KEY (created_by) REFERENCES User(user_id)
);

-- TaskUpdate Table
CREATE TABLE TaskUpdate (
    update_id INT PRIMARY KEY AUTO_INCREMENT,
    task_id INT,
    status ENUM('In Progress', 'Completed', 'Blocked') NOT NULL,
    updated_by INT,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (task_id) REFERENCES Task(task_id),
    FOREIGN KEY (updated_by) REFERENCES User(user_id)
);

-- Report Table
CREATE TABLE Report (
    report_id INT PRIMARY KEY AUTO_INCREMENT,
    task_id INT,
    status ENUM('Completed', 'Pending', 'Overdue') NOT NULL,
    report_date DATE,
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- Notification Table
CREATE TABLE Notification (
    notification_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    message TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES User(user_id)
);

-- SkillHistory Table
CREATE TABLE SkillHistory (
    history_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    skill_id INT,
    acquired_on DATE,
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);

-- TeamGroup Table
CREATE TABLE TeamGroup (
    team_id INT PRIMARY KEY AUTO_INCREMENT,
    team_name VARCHAR(100)
);

-- TeamMember Table
CREATE TABLE TeamMember (
    user_id INT,
    team_id INT,
    skill_id INT,
    task_id INT,
    PRIMARY KEY (user_id, team_id),
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (team_id) REFERENCES TeamGroup(team_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- TaskRequest Table
CREATE TABLE TaskRequest (
    request_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    task_id INT,
    request_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Requested', 'Approved', 'Rejected') DEFAULT 'Requested',
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- UserSkill Table
CREATE TABLE UserSkill (
    user_id INT,
    skill_id INT,
    PRIMARY KEY (user_id, skill_id),
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);

-- TaskSkill Table
CREATE TABLE TaskSkill (
    task_id INT,
    skill_id INT,
    PRIMARY KEY (task_id, skill_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);






-- Role
INSERT INTO Role (role_name) VALUES ('Admin'), ('Manager'), ('Employee');

-- Permission
INSERT INTO Permission (permission_name) VALUES 
('Create Task'), 
('Assign Task'), 
('Update Task'), 
('View Reports');

-- RolePermission
INSERT INTO RolePermission (role_id, permission_id) VALUES
(1, 1), (1, 2), (1, 3), (1, 4),
(2, 2), (2, 3), (2, 4),
(3, 3);

-- Users
INSERT INTO User (name, email, password, role_id, manager_id) VALUES
('Alice', 'alice@example.com', 'pass123', 1, NULL),
('Bob', 'bob@example.com', 'pass456', 2, 1),
('Charlie', 'charlie@example.com', 'pass789', 3, 2);

-- Skills
INSERT INTO Skill (skill_name, skill_description) VALUES
('Java', 'Backend programming language'),
('Python', 'General purpose programming language'),
('SQL', 'Database query language');

-- Task
INSERT INTO Task (title, description, required_skill_id, assigned_to, deadline, created_by) VALUES
('Build Backend', 'Develop backend services', 1, 3, '2025-06-01', 2),
('Write Queries', 'Write optimized SQL queries', 3, 3, '2025-06-05', 2);

-- TaskUpdate
INSERT INTO TaskUpdate (task_id, status, updated_by) VALUES
(1, 'In Progress', 3),
(2, 'In Progress', 3);

-- Report
INSERT INTO Report (task_id, status, report_date) VALUES
(1, 'Pending', '2025-05-05'),
(2, 'Pending', '2025-05-05');

-- Notification
INSERT INTO Notification (user_id, message) VALUES
(3, 'You have a new task assigned.'),
(3, 'Task deadline approaching.');

-- SkillHistory
INSERT INTO SkillHistory (user_id, skill_id, acquired_on) VALUES
(3, 1, '2023-01-01'),
(3, 2, '2024-02-15');

-- TeamGroup
INSERT INTO TeamGroup (team_name) VALUES ('Alpha Team');

-- TeamMember
INSERT INTO TeamMember (user_id, team_id, skill_id, task_id) VALUES
(3, 1, 1, 1),
(3, 1, 2, 2);

-- TaskRequest
INSERT INTO TaskRequest (user_id, task_id, status) VALUES
(3, 1, 'Requested'),
(3, 2, 'Approved');

-- UserSkill
INSERT INTO UserSkill (user_id, skill_id) VALUES
(3, 1),
(3, 2),
(2, 2);

-- TaskSkill
INSERT INTO TaskSkill (task_id, skill_id) VALUES
(1, 1),
(1, 2),
(2, 3);

select *from TaskSkill;
select *from UserSkill;



front_end 



1. PROJECT/PRODUCT OVERVIEW

The Skill-Based Task Allocation System should be designed to streamline the process of assigning tasks to employees based on their skills, availability, and workload. The system should ensure optimal task distribution, improving efficiency, and enhancing team productivity.

1. User Story Description:

As an administrator, I want to manage user roles and permissions so that I can control system access.

Acceptance Criteria

The system should allow creating, updating, and deleting user roles.

Only authorized users should be able to modify permissions

2. User Story Description:

As a manager, I want to define required skills so that I can assign them to the most suitable employees.

Acceptance Criteria

The system should allow defining new skills.

3. User Story Description:

As a manager, I want to modify required skills so that the skill descriptions are up to date.

Acceptance Criteria

The system should allow modifying existing skills

4.User Story Description:

As a manager, I want to create tasks so that I can assign them to the most suitable employees.

Acceptance Criteria

The system should allow task creation with required skills and deadlines.

5.User Story Description:

As a manager, I want to view task completion reports so that I can analyse team performance.

Acceptance Criteria

The system should generate reports showing completed, pending, and overdue tasks.

Reports should be filterable by date range, employee, and skill.

6. User Story Description

As a manager, I want to modify tasks so that I can update the tasks created by me.

Acceptance Criteria

The system should allow task modification with required skills and deadlines.

7. Uher Story Description

As a manager, I want to remove tasks so that I can cancel any task that I have created.

Acceptance Criteria

The system should allow task removal

8. User Story Description

As a tean nomber, I want to opt for tasks based on my skills so that I can work on tasks that match my

expertise.

Acceptance Criteria

Team members should be able to opt or request for new task assignments

9. User Story Description

As a team member, I want to receive tasks based on my skills so that I can work on tasks that match my

expertise.

Acceptance Criteria

The system should assign tasks based on predefined skill matching logic.

Team members should receive notifications for new task assignments

10. User Story Description

I

As a team member, I want to update task progress so that my manager can track my work status.

Acceptance Criteria

The system should allow employees to update the task status (e.g., In Progress, Completed, Blocked). Managers should receive real-time updates on task progress.

11. User Story Description

As a user, I want to view all the skills so that I can know the various skills that I can acquire.

Acceptance Criteria

The system should allow all users to view all the skills.


HARDWARE AND SOFTWARE REQUIREMENTS

a. Deployment Environment Requirements

Hardware requirements:

Component Specification

Server Intel Xeon 2.4 GHz, 16GB RAM, 500GB SSD

Database Server 8GB RAM, 250GB SSD

Software requirements:

Component Specification

Backend Java, Spring Boot

Frontend Angular

Database MySQL

Operating System Windows Server

b. Development Environment Requirements

Operating System: Windows

IDE: eclipse and Visual Studio Code

Database: MySQL

Version Control: GitHub
Prepare an Interface for Front End Using angular:


[06/05, 9:16 pm] Manoj Reddy: -- Create Database
CREATE DATABASE SkillTaskAllocationDB;
USE SkillTaskAllocationDB;

-- Role Table
CREATE TABLE Role (
    role_id INT PRIMARY KEY AUTO_INCREMENT,
    role_name VARCHAR(50) UNIQUE NOT NULL
);

-- Permission Table
CREATE TABLE Permission (
    permission_id INT PRIMARY KEY AUTO_INCREMENT,
    permission_name VARCHAR(100) UNIQUE NOT NULL
);

-- RolePermission (Many-to-Many)
CREATE TABLE RolePermission (
    role_id INT,
    permission_id INT,
    PRIMARY KEY (role_id, permission_id),
    FOREIGN KEY (role_id) REFERENCES Role(role_id),
    FOREIGN KEY (permission_id) REFERENCES Permission(permission_id)
);

-- User Table
CREATE TABLE User (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password VARCHAR(100) NOT NULL,
    role_id INT,
    manager_id INT,
    FOREIGN KEY (role_id) REFERENCES Role(role_id),
    FOREIGN KEY (manager_id) REFERENCES User(user_id)
);

-- Skill Table
CREATE TABLE Skill (
    skill_id INT PRIMARY KEY AUTO_INCREMENT,
    skill_name VARCHAR(100) NOT NULL,
    skill_description VARCHAR(255)
);

-- Task Table
CREATE TABLE Task (
    task_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    description TEXT,
    required_skill_id INT,
    assigned_to INT,
    deadline DATE,
    created_by INT,
    FOREIGN KEY (required_skill_id) REFERENCES Skill(skill_id),
    FOREIGN KEY (assigned_to) REFERENCES User(user_id),
    FOREIGN KEY (created_by) REFERENCES User(user_id)
);

-- TaskUpdate Table
CREATE TABLE TaskUpdate (
    update_id INT PRIMARY KEY AUTO_INCREMENT,
    task_id INT,
    status ENUM('In Progress', 'Completed', 'Blocked') NOT NULL,
    updated_by INT,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (task_id) REFERENCES Task(task_id),
    FOREIGN KEY (updated_by) REFERENCES User(user_id)
);

-- Report Table
CREATE TABLE Report (
    report_id INT PRIMARY KEY AUTO_INCREMENT,
    task_id INT,
    status ENUM('Completed', 'Pending', 'Overdue') NOT NULL,
    report_date DATE,
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- Notification Table
CREATE TABLE Notification (
    notification_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    message TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES User(user_id)
);

-- SkillHistory Table
CREATE TABLE SkillHistory (
    history_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    skill_id INT,
    acquired_on DATE,
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);

-- TeamGroup Table
CREATE TABLE TeamGroup (
    team_id INT PRIMARY KEY AUTO_INCREMENT,
    team_name VARCHAR(100)
);

-- TeamMember Table
CREATE TABLE TeamMember (
    user_id INT,
    team_id INT,
    skill_id INT,
    task_id INT,
    PRIMARY KEY (user_id, team_id),
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (team_id) REFERENCES TeamGroup(team_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- TaskRequest Table
CREATE TABLE TaskRequest (
    request_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    task_id INT,
    request_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Requested', 'Approved', 'Rejected') DEFAULT 'Requested',
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id)
);

-- UserSkill Table
CREATE TABLE UserSkill (
    user_id INT,
    skill_id INT,
    PRIMARY KEY (user_id, skill_id),
    FOREIGN KEY (user_id) REFERENCES User(user_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);

-- TaskSkill Table
CREATE TABLE TaskSkill (
    task_id INT,
    skill_id INT,
    PRIMARY KEY (task_id, skill_id),
    FOREIGN KEY (task_id) REFERENCES Task(task_id),
    FOREIGN KEY (skill_id) REFERENCES Skill(skill_id)
);
[06/05, 9:17 pm] Manoj Reddy: -- Role
INSERT INTO Role (role_name) VALUES ('Admin'), ('Manager'), ('Employee');

-- Permission
INSERT INTO Permission (permission_name) VALUES 
('Create Task'), 
('Assign Task'), 
('Update Task'), 
('View Reports');

-- RolePermission
INSERT INTO RolePermission (role_id, permission_id) VALUES
(1, 1), (1, 2), (1, 3), (1, 4),
(2, 2), (2, 3), (2, 4),
(3, 3);

-- Users
INSERT INTO User (name, email, password, role_id, manager_id) VALUES
('Alice', 'alice@example.com', 'pass123', 1, NULL),
('Bob', 'bob@example.com', 'pass456', 2, 1),
('Charlie', 'charlie@example.com', 'pass789', 3, 2);

-- Skills
INSERT INTO Skill (skill_name, skill_description) VALUES
('Java', 'Backend programming language'),
('Python', 'General purpose programming language'),
('SQL', 'Database query language');

-- Task
INSERT INTO Task (title, description, required_skill_id, assigned_to, deadline, created_by) VALUES
('Build Backend', 'Develop backend services', 1, 3, '2025-06-01', 2),
('Write Queries', 'Write optimized SQL queries', 3, 3, '2025-06-05', 2);

-- TaskUpdate
INSERT INTO TaskUpdate (task_id, status, updated_by) VALUES
(1, 'In Progress', 3),
(2, 'In Progress', 3);

-- Report
INSERT INTO Report (task_id, status, report_date) VALUES
(1, 'Pending', '2025-05-05'),
(2, 'Pending', '2025-05-05');

-- Notification
INSERT INTO Notification (user_id, message) VALUES
(3, 'You have a new task assigned.'),
(3, 'Task deadline approaching.');

-- SkillHistory
INSERT INTO SkillHistory (user_id, skill_id, acquired_on) VALUES
(3, 1, '2023-01-01'),
(3, 2, '2024-02-15');

-- TeamGroup
INSERT INTO TeamGroup (team_name) VALUES ('Alpha Team');

-- TeamMember
INSERT INTO TeamMember (user_id, team_id, skill_id, task_id) VALUES
(3, 1, 1, 1),
(3, 1, 2, 2);

-- TaskRequest
INSERT INTO TaskRequest (user_id, task_id, status) VALUES
(3, 1, 'Requested'),
(3, 2, 'Approved');

-- UserSkill
INSERT INTO UserSkill (user_id, skill_id) VALUES
(3, 1),
(3, 2),
(2, 2);

-- TaskSkill
INSERT INTO TaskSkill (task_id, skill_id) VALUES
(1, 1),
(1, 2),
(2, 3);

1.USER

First we created user which consists of the fields:

1. berid

2. Name

3. Email

4. Password

5. Phone

6. Address

7. Role

8. Manager Which is self referential

We created an API with method createllser which takes userIQ as parameter

We created an API with method updateluser which takes userId and userDIO as parameter

We created an API with method deletellser which takes userld as parameter

We created an API with method getUserById which takes userld as parameter

We created an API with method getAllUsers

We created an API with method getUsersByßale which takes poleld as parameter

We created an API with method getUserllyManager which takes managerid as parameter

2. TASK

First we created task which consists of the fields:
View

1. taskId

2. title

3. description

4. priority

5. status

6. estimatedliours

7. actualHours

8. requiredSkills

9. assignedIo

10. deadline

11. startDate

12. endDate

13. createdfly

14. skill

We created an API with method createTask which takes taskDIO as parameter

We created an API with method undateTask which takes taskid, taskDIO as parameters

We created an API with method deleteTask which takes taskid as parameter

We created an API with method getTaskByManager which takes managerld as parameter

We created an API with method getLaskByEmployee which takes userId as parameter

3. SKILL

First we created task which consists of the fields:

1. skillld

2. skillName

3. skillDescription
   category

5. level

We created an API with method addskill which takes skill010 as parameter

We created an API with method updateskill which takes skillld.skilIDIO as parameter

We created an API with method getAllSkills

We created an API with method getSkillByName which takes skillllame as parameter

4. USERSKILL

We created an API with method getUserSkills which takes userid as parameter

We created an API with method hasSkill which takes userld, skillid as parameter

5. TASKSKILL

We created an API with method assignSkillTolask which takes taskSkillDTQ as parameter

We created an API with method getSkillsByTaskid which takes taskid as parameter

6. TASKREQUEST

I

We created an API with method requestTaskMethad which takes taskRequestDIO as parameter

We created an API with method getRequestByUser which takes userId as parameter

7. TASKUPDATE

We created an API with methed updateTaskStatus which takes taskUpdateDIO as parameter

We created an API with method getupdatesBylask which takes taskid as parameter
We created an API with method updateTaskStatus which takes taskUpdateRIO as parameter

We created an API with method gettindatesllyTask which takes taskid as parameter

8. SKILLHISTORY

We created an API with method logSkillAcquisition which takes skillHistoryDIO as parameter

We created an API with method getSkillHistory@yuser which takes userld as parameter

9. ROLE

First we created task which consists of the fields:

1. raleld

2. coleName

3. description

4. List<String> UserName

We created an API with method createRole which takes caleDIQ as parameter

We created an API with method updateßole which takes roleld, coleDIO as parameter

We created an API with method deleteRole which takes poleld as parameter

We created an API with method getAllRoles

We created an API with method getBolellyld which takes userid as parameter

10. PERMISSION

First we created task which consists of the fields:

1. rolePermissionId (Composite Primary Key)
2.grantedBy
3.grantAt
We created on API with method createfermission which takes permissionDIO as parameter

We created an API with method getAllPermission

We created an API with method getPermissionlyId which takes permissiلتصو parameter

We created an API with method undatePermission which takes permissionid, permission ID as parameters

We created an API with method deletefermission which takes permissionid as parameter

We created an API with method getlyllane which takes permissionliane as parameter

11. NOTIFICATION

We created an API with method notifyuser which takes notificationDID as parameter

We created an API with method getiinreadiotifications which takes userld as parameter

12. REPORT

We created an API with method getReportByStatustethed which takes status(this is of Enum type) as parameter

We created an API with method getReportsByDateßange which takes stactDate, endDate as parameter

We created an API with method getReports@yilser which takes userId as parameter

13. TEAMSERVICE

We created an API with method createTeam which takes teamGroupDIO as parameter

We created an API with method addUserIolean which takes tcantienberDIO as parameter

We created an API with method getleanflembers which takes teamld as parameter



generate the frontend application as mentioned above things.
