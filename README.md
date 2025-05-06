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
