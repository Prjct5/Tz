# UML Description for JavaFX Student Management System

This document provides a complete textual description for creating UML diagrams for a JavaFX student management system project. The primary focus is the **class diagram**, with optional **use case** and **sequence diagrams**. The project is developed in IntelliJ on Windows, using JavaFX, PostgreSQL, and demonstrates all OOP concepts (encapsulation, inheritance, polymorphism, abstraction).

**Package**: `com.projecttest.projecttest`

## Class Diagram

The class diagram captures the system's structure, including classes, interfaces, attributes, methods, and relationships.

### Classes and Interfaces

#### 1. Class: Info
- **Description**: Utility class holding static user information.
- **Attributes**:
  - `+currentUser: String` (static)
  - `+currentRole: String` (static)
- **Methods**: None
- **Notes**: Base class for inheritance.

#### 2. Class: byInfo (Abstract)
- **Description**: Abstract class extending `Info`, serving as a base.
- **Extends**: `Info`
- **Attributes**: None
- **Methods**: None
- **Notes**: Empty abstract class for hierarchy.

#### 3. Interface: UserOperations
- **Description**: Defines a contract for user management operations (abstraction).
- **Methods**:
  - `+addUser(username: String, email: String, password: String, role: String): boolean`
  - `+removeUser(userId: int): boolean`
  - `+getUserDetails(userId: int): String`
- **Notes**: Implemented by `UserManager`.

#### 4. Class: AbstractUserManager (Abstract)
- **Description**: Abstract base class for user management, encapsulating a `Database` object.
- **Extends**: `Info`
- **Attributes**:
  - `-database: Database` (final)
- **Methods**:
  - `#AbstractUserManager()`
  - `#getDatabase(): Database`
  - `#userExists(username: String): boolean`
  - `#formatUserDetails(userData: Map<String, String>): String` (abstract)
- **Notes**: Encapsulates database access.

#### 5. Class: UserManager
- **Description**: Concrete class for user management.
- **Extends**: `AbstractUserManager`
- **Implements**: `UserOperations`
- **Attributes**: None
- **Methods**:
  - `+UserManager()`
  - `+addUser(username: String, email: String, password: String, role: String): boolean`
  - `+removeUser(userId: int): boolean`
  - `+getUserDetails(userId: int): String`
  - `+getUserDetails(username: String): String`
  - `#formatUserDetails(userData: Map<String, String>): String`
- **Notes**: Demonstrates polymorphism and encapsulation.

#### 6. Class: Database
- **Description**: Handles database connections and operations.
- **Attributes**:
  - `-URL: String` (static, final) = "jdbc:postgresql://localhost:5432/oop2"
  - `-USER: String` (static, final) = "postgres"
  - `-PASSWORD: String` (static, final) = "z20102006"
- **Methods**:
  - `+registerUser(username: String, email: String, password: String, role: String): boolean`
  - `+loginUser(username: String, password: String): Map<String, String>`
  - `+addStudent(userId: int, studentId: String, organisation: String, level: String, gpa: double): boolean`
  - `+addCourse(title: String, description: String, instructorId: int, requirements: String): boolean`
  - `+addCourseVideo(courseId: int, title: String, description: String, videoUrl: String, videoFilePath: String, duration: int, orderNumber: int): boolean`
  - `+enrollStudentInCourse(studentId: int, courseId: int): boolean`
  - `+addAssignment(courseId: int, title: String, description: String, dueDate: Timestamp, maxGrade: int): boolean`
  - `+submitAssignment(assignmentId: int, studentId: int, submissionFile: String, submissionText: String): boolean`
  - `+saveSetting(name: String, value: String): boolean`
  - `+getCourseVideos(courseId: int): ResultSet`
  - `+getAssignments(courseId: int): ResultSet`
  - `+getConnection(): Connection`
- **Notes**: Centralizes database operations.

#### 7. Class: Main
- **Description**: Entry point for the JavaFX application.
- **Extends**: `Application` (javafx.application)
- **Attributes**: None
- **Methods**:
  - `+start(primaryStage: Stage): void`
  - `+main(args: String[]): void`
- **Notes**: Launches the login screen.

#### 8. Class: LoginController
- **Description**: Manages the login interface.
- **Attributes**:
  - `-usernameField: TextField` (FXML)
  - `-passwordField: PasswordField` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-handleLogin(): void` (FXML)
  - `-handleGoToRegister(): void` (FXML)
- **Notes**: Interacts with `Database` for login.

#### 9. Class: RegisterController
- **Description**: Manages the registration interface.
- **Attributes**:
  - `-usernameField: TextField` (FXML)
  - `-emailField: TextField` (FXML)
  - `-passwordField: PasswordField` (FXML)
  - `-roleComboBox: ComboBox<String>` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-handleRegister(): void` (FXML)
  - `-handleGoToLogin(): void` (FXML)
- **Notes**: Uses `Database` for registration.

#### 10. Class: AdminDashboardController
- **Description**: Manages the admin dashboard interface.
- **Attributes**:
  - `-manageUsersButton: Button` (FXML)
  - `-systemSettingsButton: Button` (FXML)
  - `-dataManagementButton: Button` (FXML)
  - `-reportsButton: Button` (FXML)
  - `-logoutButton: Button` (FXML)
- **Methods**:
  - `-openNewWindow(fxmlFile: String, title: String): void`
  - `-handleManageUsers(): void` (FXML)
  - `-handleSystemSettings(): void` (FXML)
  - `-handleDataManagement(): void` (FXML)
  - `-handleReports(): void` (FXML)
  - `-handleLogout(): void` (FXML)
- **Notes**: Navigates to admin interfaces.

#### 11. Class: DataManagementController
- **Description**: Manages data import/export and student addition.
- **Attributes**:
  - `-exportButton: Button` (FXML)
  - `-importButton: Button` (FXML)
  - `-backButton: Button` (FXML)
  - `-fullNameField: TextField` (FXML)
  - `-gpaField: TextField` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-openNewWindow(fxmlFile: String, title: String): void`
  - `-handleExportData(): void` (FXML)
  - `-handleImportData(): void` (FXML)
  - `-handleBack(): void` (FXML)
- **Notes**: Interacts with `Database` for student data.

#### 12. Class: SystemSettingsController
- **Description**: Manages system settings interface.
- **Attributes**:
  - `-languageButton: Button` (FXML)
  - `-permissionsButton: Button` (FXML)
  - `-resetButton: Button` (FXML)
  - `-backButton: Button` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-initialize(): void` (FXML)
  - `-handleChangeLanguage(): void` (FXML)
  - `-handleManagePermissions(): void` (FXML)
  - `-handleReset(): void` (FXML)
  - `-handleBack(): void` (FXML)
- **Notes**: Placeholder for settings.

#### 13. Class: StudentController
- **Description**: Manages the student dashboard interface.
- **Attributes**:
  - `-contentArea: VBox` (FXML)
  - `-coursesButton: Button` (FXML)
  - `-videosButton: Button` (FXML)
  - `-tasksButton: Button` (FXML)
  - `-backButtonPrev: Button` (FXML)
  - `-backButtonLogin: Button` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-initialize(): void` (FXML)
  - `-handleViewCourses(): void` (FXML)
  - `-handleWatchVideos(): void` (FXML)
  - `-handleCheckTasks(): void` (FXML)
  - `-handleBackLogin(): void` (FXML)
- **Notes**: Displays courses, videos, tasks.

#### 14. Class: TeacherController
- **Description**: Manages the teacher dashboard interface.
- **Attributes**:
  - `-contentArea: VBox` (FXML)
  - `-coursesButton: Button` (FXML)
  - `-addTaskButton: Button` (FXML)
  - `-studentsButton: Button` (FXML)
  - `-backButtonPrev: Button` (FXML)
  - `-backButtonLogin: Button` (FXML)
  - `-statusLabel: Label` (FXML)
- **Methods**:
  - `-initialize(): void` (FXML)
  - `-handleManageCourses(): void` (FXML)
  - `-handleAddTask(): void` (FXML)
  - `-handleViewStudents(): void` (FXML)
  - `-handleBackPrev(): void` (FXML)
  - `-handleBackLogin(): void` (FXML)
- **Notes**: Manages courses, assignments, students.

#### 15. Class: ReportsController
- **Description**: Manages the reports interface.
- **Attributes**:
  - `-reportsTable: TableView<ReportItem>` (FXML)
  - `-categoryColumn: TableColumn<ReportItem, String>` (FXML)
  - `-countColumn: TableColumn<ReportItem, Integer>` (FXML)
  - `-refreshButton: Button` (FXML)
  - `-backButton: Button` (FXML)
  - `-statusLabel: Label` (FXML)
  - `-reportsList: ObservableList<ReportItem>`
- **Inner Class: ReportItem**
  - **Attributes**:
    - `-category: String` (final)
    - `-count: int` (final)
  - **Methods**:
    - `+ReportItem(category: String, count: int)`
    - `+getCategory(): String`
    - `+getCount(): int`
- **Methods**:
  - `-initialize(): void` (FXML)
  - `-loadReports(): void`
  - `-handleRefresh(): void` (FXML)
  - `-handleBack(): void` (FXML)
- **Notes**: Displays user role counts.

#### 16. Class: ManageUsersController
- **Description**: Manages the user management interface, integrated with `UserManager`.
- **Attributes**:
  - `-usernameField: TextField` (FXML)
  - `-emailField: TextField` (FXML)
  - `-passwordField: TextField` (FXML)
  - `-roleComboBox: ComboBox<String>` (FXML)
  - `-addButton: Button` (FXML)
  - `-deleteButton: Button` (FXML)
  - `-backButton: Button` (FXML)
  - `-usersTable: TableView<User>` (FXML)
  - `-idColumn: TableColumn<User, Integer>` (FXML)
  - `-usernameColumn: TableColumn<User, String>` (FXML)
  - `-emailColumn: TableColumn<User, String>` (FXML)
  - `-roleColumn: TableColumn<User, String>` (FXML)
  - `-statusLabel: Label` (FXML)
  - `-usersList: ObservableList<User>`
  - `-userManager: UserManager` (final)
- **Inner Class: User**
  - **Attributes**:
    - `-id: int` (final)
    - `-username: String` (final)
    - `-email: String` (final)
    - `-role: String` (final)
  - **Methods**:
    - `+User(id: int, username: String, email: String, role: String)`
    - `+getId(): int`
    - `+getUsername(): String`
    - `+getEmail(): String`
    - `+getRole(): String`
- **Methods**:
  - `-initialize(): void` (FXML)
  - `-loadUsers(): void`
  - `-openNewWindow(fxmlFile: String, title: String): void`
  - `-isEmailExists(email: String): boolean`
  - `-handleAddUser(): void` (FXML)
  - `-handleDeleteUser(): void` (FXML)
  - `-handleBack(): void` (FXML)
- **Notes**: Uses `UserManager` for user operations.

### Relationships
- **Inheritance**:
  - `byInfo` -> `Info`
  - `AbstractUserManager` -> `Info`
  - `UserManager` -> `AbstractUserManager`
  - `Main` -> `Application`
- **Interface Implementation**:
  - `UserManager` -> `UserOperations`
- **Composition**:
  - `AbstractUserManager` owns `Database`
- **Dependency**:
  - `ManageUsersController` -> `UserManager`
  - `LoginController`, `RegisterController`, `DataManagementController`, `StudentController`, `TeacherController`, `ReportsController` -> `Database`
  - `AdminDashboardController`, `DataManagementController`, `SystemSettingsController`, `ReportsController`, `ManageUsersController` -> `FXMLLoader`
- **Aggregation**:
  - `ReportsController` contains `ReportItem`
  - `ManageUsersController` contains `User`

### Database Schema (Context)
- **Tables**: `users`, `students`, `courses`, `course_videos`, `course_enrollments`, `assignments`, `assignment_submissions`, `settings`
- **Key Relationships**:
  - `users(id)` -> `students(user_id)`, `courses(instructor_id)`
  - `courses(id)` -> `course_videos(course_id)`, `course_enrollments(course_id)`, `assignments(course_id)`
  - `students(id)` -> `course_enrollments(student_id)`, `assignment_submissions(student_id)`
  - `assignments(id)` -> `assignment_submissions(assignment_id)`
- **Note**: Represent `Database` class in UML; tables are external but inform method parameters.

## Use Case Diagram
- **Actors**:
  - **Admin**: Manages users, settings, data, reports.
  - **Teacher**: Manages courses, adds assignments, views students.
  - **Student**: Views courses, videos, tasks.
  - **System**: Interacts with the database.
- **Use Cases**:
  - **Admin**:
    - Login (`LoginController`)
    - Register (`RegisterController`)
    - Manage Users (`ManageUsersController`)
    - Manage Data (`DataManagementController`)
    - View Reports (`ReportsController`)
    - Manage Settings (`SystemSettingsController`)
  - **Teacher**:
    - Login (`LoginController`)
    - Register (`RegisterController`)
    - Manage Courses (`TeacherController`)
    - Add Assignment (`TeacherController`)
    - View Students (`TeacherController`)
  - **Student**:
    - Login (`LoginController`)
    - Register (`RegisterController`)
    - View Courses (`StudentController`)
    - View Videos (`StudentController`)
    - View Tasks (`StudentController`)
  - **System**:
    - Database Operations (`Database`)
- **Relationships**:
  - Actors to use cases via associations.
  - `Login` and `Register` shared across roles (extend/include).

## Sequence Diagram (Example: Add User)
- **Scenario**: Admin adds a user via `ManageUsersController`.
- **Objects**:
  - Admin (Actor)
  - `ManageUsersController`
  - `UserManager`
  - `Database`
  - Database (external system)
- **Flow**:
  1. Admin clicks “Add User” button.
  2. `ManageUsersController.handleAddUser()`
  3. Validate inputs (username, email, password, role).
  4. `ManageUsersController.isEmailExists()` -> `Database`
  5. `Database` returns email existence.
  6. `ManageUsersController` -> `UserManager.addUser()`
  7. `UserManager.userExists()` -> `Database`
  8. `UserManager` -> `Database.registerUser()`
  9. `Database` inserts user.
  10. `UserManager` returns result.
  11. `ManageUsersController.loadUsers()`
  12. Update `statusLabel`.

## Notes
- **Focus**: Class diagram for OOP concepts.
- **OOP Concepts**:
  - **Encapsulation**: Private fields in `UserManager`, controllers; getters/setters.
  - **Inheritance**: `UserManager` -> `AbstractUserManager` -> `Info`.
  - **Polymorphism**: `UserManager` implements `UserOperations`; overloaded `getUserDetails`.
  - **Abstraction**: `UserOperations` interface; `AbstractUserManager`.
- **FXML/CSS**: FXML files (`login.fxml`, etc.) define UI, linked to controllers via `@FXML`. CSS files (`style.css`, `designSTU.css`) style UI, not in class diagram.
- **Database**: `Database` class with static methods; note interactions via dependency arrows.
- **Visibility**: `+` (public), `-` (private), `#` (protected).

## Instructions for Drawing
- **Class Diagram**:
  - Draw classes/interfaces as rectangles, italicize abstract classes/interfaces.
  - Show inheritance (solid arrows), interface realization (dashed arrows), dependencies (dashed arrows).
  - Include inner classes (`ReportItem`, `User`) within parents.
  - Note database schema (`export.sql`) for table relationships.
- **Use Case Diagram** (optional):
  - Actors as stick figures, use cases as ovals.
  - Connect with solid lines; use `<<include>>`, `<<extend>>` for shared use cases.
- **Sequence Diagram** (optional):
  - Objects as rectangles with lifelines (dashed lines).
  - Method calls (solid arrows), returns (dashed arrows).
  - Include conditions (e.g., email validation) as guards.
- **Tools**: Use Lucidchart, Draw.io, or Enterprise Architect.
- **Verification**: Cross-check with code files.