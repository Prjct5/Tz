# Technical Documentation for Teaching Zone Platform

## Overview
The Teaching Zone Platform is a JavaFX-based desktop application designed for educational institutions to manage users, courses, assignments, and system settings. It uses a PostgreSQL database for data persistence and HikariCP for connection pooling. The application follows object-oriented programming (OOP) principles, including encapsulation, inheritance, polymorphism, and abstraction. The user interface is built using FXML files with CSS styling, and navigation is handled via a `NavigationService`.

## Package Structure
- **com.projecttest.projecttest.models**: Contains data models representing users and session management.
- **com.projecttest.projecttest.services**: Includes service classes for database operations and navigation.
- **com.projecttest.projecttest.controllers**: Contains controller classes for handling UI interactions.
- **com.projecttest.projecttest**: Main application entry point.

## Classes and Methods

### Models Package

#### User (Abstract Class)
**Purpose**: Represents a generic user with common attributes (ID, username, email, role) and defines an abstract method for dashboard navigation.
- **Fields**:
  - `id` (int, private final): Unique user identifier.
  - `username` (String, private final): User's username.
  - `email` (String, private final): User's email address.
  - `role` (String, private final): User's role (Admin, Teacher, Student).
- **Constructor**:
  - `User(int id, String username, String email, String role)`: Initializes user attributes.
- **Methods**:
  - `getId(): int`: Returns the user ID.
  - `getUsername(): String`: Returns the username.
  - `getEmail(): String`: Returns the email.
  - `getRole(): String`: Returns the role.
  - `abstract getDashboardFXML(): String`: Abstract method to return the FXML file for the user's dashboard.
- **Usage**: Extended by `Admin`, `Teacher`, and `Student` to provide role-specific behavior.

#### Admin (Extends User)
**Purpose**: Represents an admin user with access to the admin dashboard.
- **Constructor**:
  - `Admin(int id, String username, String email)`: Initializes with role "Admin".
- **Methods**:
  - `getDashboardFXML(): String`: Returns "AdminDashboard.fxml".
- **Usage**: Used in `LoginController` to redirect admins after login.

#### Teacher (Extends User)
**Purpose**: Represents a teacher user with access to the teacher dashboard.
- **Constructor**:
  - `Teacher(int id, String username, String email)`: Initializes with role "Teacher".
- **Methods**:
  - `getDashboardFXML(): String`: Returns "Mesarw3Teacher.fxml".
- **Usage**: Used in `LoginController` to redirect teachers after login.

#### Student (Extends User)
**Purpose**: Represents a student user with access to the student dashboard.
- **Constructor**:
  - `Student(int id, String username, String email)`: Initializes with role "Student".
- **Methods**:
  - `getDashboardFXML(): String`: Returns "StudentController.fxml".
- **Usage**: Used in `LoginController` to redirect students after login.

#### UserSession
**Purpose**: Manages the current user's session using the Singleton pattern.
- **Fields**:
  - `instance` (UserSession, private static): Singleton instance.
  - `currentUser` (User, private): Currently logged-in user.
- **Constructor**:
  - `UserSession()` (private): Prevents direct instantiation.
- **Methods**:
  - `getInstance(): UserSession`: Returns the singleton instance.
  - `setCurrentUser(User user): void`: Sets the current user.
  - `getCurrentUser(): User`: Returns the current user.
  - `clearSession(): void`: Clears the current user session.
- **Usage**: Used across controllers to access the logged-in user's details (e.g., in `TeacherController` to get `teacherId`).

### Services Package

#### DatabaseService (Interface)
**Purpose**: Defines methods for database operations, ensuring abstraction.
- **Methods**:
  - `registerUser(String username, String email, String password, String role): boolean`: Registers a new user.
  - `loginUser(String username, String password): Map<String, String>`: Authenticates a user and returns user data.
  - `addStudent(int userId, String studentId, String organisation, String level, double gpa, String fullName): boolean`: Adds a student to the database.
  - `addCourse(String title, String description, int instructorId, String requirements): boolean`: Adds a course.
  - `addCourseVideo(int courseId, String title, String description, String videoUrl, String videoFilePath, int duration, int orderNumber): boolean`: Adds a course video.
  - `enrollStudentInCourse(int studentId, int courseId): boolean`: Enrolls a student in a course.
  - `addAssignment(int courseId, String title, String description, Timestamp dueDate, int maxGrade): boolean`: Adds an assignment.
  - `submitAssignment(int assignmentId, int studentId, String submissionFile, String submissionText): boolean`: Submits an assignment.
  - `saveSetting(String name, String value): boolean`: Saves a system setting.
  - `getCourseVideos(int courseId): ResultSet`: Retrieves videos for a course.
  - `getAssignments(int courseId): ResultSet`: Retrieves assignments for a course.
  - `getConnection(): Connection`: Returns a database connection.
  - `closeConnection(): void`: Closes the database connection pool.
- **Usage**: Implemented by `Database` to provide database functionality.

#### Database (Implements DatabaseService)
**Purpose**: Handles database connectivity and operations using HikariCP and PostgreSQL.
- **Fields**:
  - `dataSource` (HikariDataSource, private final): Connection pool for database connections.
- **Constructor**:
  - `Database()`: Initializes HikariCP with PostgreSQL configuration (URL: `jdbc:postgresql://localhost:5432/oop2`, username: `postgres`, password: `z20102006`).
- **Methods**:
  - Implements all `DatabaseService` methods using prepared statements for security.
  - Uses `BCrypt` for password hashing (`registerUser`, `loginUser`).
  - Handles SQL exceptions and returns appropriate boolean or result set values.
- **Database Connectivity**:
  - Uses HikariCP for connection pooling to manage database connections efficiently.
  - Configured with:
    - JDBC URL: `jdbc:postgresql://localhost:5432/oop2`
    - Username: `postgres`
    - Password: `z20102006`
    - Driver: `org.postgresql.Driver`
    - Maximum pool size: 10
  - Connections are obtained via `getConnection()` and managed by HikariCP.
  - `closeConnection()` shuts down the pool when the application exits.
- **Usage**: Injected into controllers via `setDatabaseService` to perform CRUD operations.

#### NavigationService
**Purpose**: Manages navigation between FXML scenes, closing the current window and opening a new one.
- **Methods**:
  - `openNewWindow(Control control, String fxmlFile, String title): void`: Loads the specified FXML file, creates a new stage, and closes the current stage.
- **Usage**: Used by controllers to navigate between screens (e.g., `LoginController` to `AdminDashboard`).

### Controllers Package

#### BaseController (Abstract Class)
**Purpose**: Provides common functionality for all controllers, including database and navigation services.
- **Fields**:
  - `databaseService` (DatabaseService, protected): Database service instance.
  - `navigationService` (NavigationService, protected): Navigation service instance.
  - `statusLabel` (Label, protected): UI label for status messages.
- **Methods**:
  - `setDatabaseService(DatabaseService databaseService): void`: Sets the database service.
  - `setNavigationService(NavigationService navigationService): void`: Sets the navigation service.
  - `setStatusLabel(Label statusLabel): void`: Sets the status label.
  - `showError(String message): void`: Displays an error message on the status label or console.
- **Usage**: Extended by all controller classes to inherit common functionality.

#### LoginController (Extends BaseController)
**Purpose**: Handles user login and navigation to the register screen.
- **Fields**:
  - `usernameField` (TextField, FXML): Input field for username.
  - `passwordField` (PasswordField, FXML): Input field for password.
  - `statusLabel` (Label, FXML): Displays login status.
- **Methods**:
  - `handleLogin(): void`: Authenticates the user, creates a `User` object based on role, sets the session, and navigates to the appropriate dashboard.
  - `handleGoToRegister(): void`: Navigates to the register screen.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `login.fxml` to manage user authentication.

#### RegisterController (Extends BaseController)
**Purpose**: Handles user registration and navigation to the login screen.
- **Fields**:
  - `usernameField` (TextField, FXML): Input field for username.
  - `emailField` (TextField, FXML): Input field for email.
  - `passwordField` (PasswordField, FXML): Input field for password.
  - `roleComboBox` (ComboBox<String>, FXML): Dropdown for selecting role.
  - `statusLabel` (Label, FXML): Displays registration status.
- **Methods**:
  - `handleRegister(): void`: Validates input, registers the user, and navigates to the login screen.
  - `handleGoToLogin(): void`: Navigates to the login screen.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `register.fxml` to manage user registration.

#### AdminDashboardController (Extends BaseController)
**Purpose**: Provides the admin dashboard UI with navigation to other admin screens.
- **Fields**:
  - `manageUsersButton` (Button, FXML): Button to manage users.
  - `systemSettingsButton` (Button, FXML): Button for system settings.
  - `dataManagementButton` (Button, FXML): Button for data management.
  - `reportsButton` (Button, FXML): Button for reports.
  - `logoutButton` (Button, FXML): Button to log out.
  - `statusLabel` (Label, FXML): Displays status messages.
- **Methods**:
  - `handleManageUsers(): void`: Navigates to the manage users screen.
  - `handleSystemSettings(): void`: Navigates to the system settings screen.
  - `handleDataManagement(): void`: Navigates to the data management screen.
  - `handleReports(): void`: Navigates to the reports screen.
  - `handleLogout(): void`: Navigates to the login screen.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `AdminDashboard.fxml` to provide admin navigation.

#### ManageUsersController (Extends BaseController)
**Purpose**: Manages user creation and deletion, displaying users in a table.
- **Fields**:
  - `usernameField` (TextField, FXML): Input field for username.
  - `emailField` (TextField, FXML): Input field for email.
  - `passwordField` (TextField, FXML): Input field for password.
  - `roleComboBox` (ComboBox<String>, FXML): Dropdown for selecting role.
  - `addButton` (Button, FXML): Button to add a user.
  - `deleteButton` (Button, FXML): Button to delete a user.
  - `backButton` (Button, FXML): Button to return to the dashboard.
  - `usersTable` (TableView<User>, FXML): Table displaying users.
  - `idColumn`, `usernameColumn`, `emailColumn`, `roleColumn` (TableColumn, FXML): Table columns.
  - `statusLabel` (Label, FXML): Displays status messages.
  - `usersList` (ObservableList<User>, private): List of users for the table.
- **Inner Class**:
  - `User`: Represents a user in the table with ID, username, email, and role.
- **Methods**:
  - `initialize(): void`: Sets up table columns and loads users.
  - `loadUsers(): void`: Queries the database and populates the users table.
  - `handleAddUser(): void`: Validates input and adds a new user.
  - `handleDeleteUser(): void`: Deletes the selected user from the database.
  - `handleBack(): void`: Navigates to the admin dashboard.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `ManageUsers.fxml` to manage user accounts.

#### DataManagementController (Extends BaseController)
**Purpose**: Handles importing and exporting student data.
- **Fields**:
  - `exportButton` (Button, FXML): Button to export data.
  - `importButton` (Button, FXML): Button to import data.
  - `backButton` (Button, FXML): Button to return to the dashboard.
  - `fullNameField` (TextField, FXML): Input field for student full name.
  - `gpaField` (TextField, FXML): Input field for student GPA.
  - `statusLabel` (Label, FXML): Displays status messages.
- **Methods**:
  - `handleExportData(): void`: Exports student data to a CSV file.
  - `handleImportData(): void`: Imports a student record into the database.
  - `handleBack(): void`: Navigates to the admin dashboard.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `DataManagement.fxml` to manage student data.

#### ReportsController (Extends BaseController)
**Purpose**: Displays user role statistics in a table.
- **Fields**:
  - `reportsTable` (TableView<ReportItem>, FXML): Table displaying report data.
  - `categoryColumn`, `countColumn` (TableColumn, FXML): Table columns.
  - `refreshButton` (Button, FXML): Button to refresh reports.
  - `backButton` (Button, FXML): Button to return to the dashboard.
  - `statusLabel` (Label, FXML): Displays status messages.
  - `reportsList` (ObservableList<ReportItem>, private): List of report items.
- **Inner Class**:
  - `ReportItem`: Represents a report entry with category and count.
- **Methods**:
  - `initialize(): void`: Sets up table columns and loads reports.
  - `loadReports(): void`: Queries the database for user counts by role.
  - `handleRefresh(): void`: Reloads the reports table.
  - `handleBack(): void`: Navigates to the admin dashboard.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `Reports.fxml` to display statistics.

#### SystemSettingsController (Extends BaseController)
**Purpose**: Manages system settings (placeholder implementation).
- **Fields**:
  - `languageButton`, `permissionsButton`, `resetButton`, `backButton` (Button, FXML): Buttons for settings actions.
  - `statusLabel` (Label, FXML): Displays status messages.
- **Methods**:
  - `initialize(): void`: Initializes the controller.
  - `handleChangeLanguage(): void`: Saves language setting (example).
  - `handleManagePermissions(): void`: Placeholder for permissions management.
  - `handleReset(): void`: Placeholder for resetting settings.
  - `handleBack(): void`: Navigates to the admin dashboard.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `SystemSettings.fxml` for system configuration.

#### TeacherController (Extends BaseController)
**Purpose**: Provides the teacher dashboard for managing courses, tasks, and students.
- **Fields**:
  - `contentArea` (VBox, FXML): Area for dynamic content.
  - `coursesButton`, `addTaskButton`, `studentsButton`, `backButtonPrev`, `backButtonLogin` (Button, FXML): Action buttons.
  - `statusLabel` (Label, FXML): Displays status messages.
- **Methods**:
  - `initialize(): void`: Initializes the controller.
  - `handleManageCourses(): void`: Displays courses taught by the teacher.
  - `handleAddTask(): void`: Provides a form to add a new assignment.
  - `handleViewStudents(): void`: Displays students enrolled in the teacher’s courses.
  - `handleBackPrev(): void`: Placeholder for previous page navigation.
  - `handleBackLogin(): void`: Navigates to the login screen.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `TeacherController.fxml` and `Mesarw3Teacher.fxml`.

#### StudentController (Extends BaseController)
**Purpose**: Provides the student dashboard for viewing courses, videos, and tasks.
- **Fields**:
  - `contentArea` (VBox, FXML): Area for dynamic content.
  - `coursesButton`, `videosButton`, `tasksButton`, `backButtonLogin` (Button, FXML): Action buttons.
  - `statusLabel` (Label, FXML): Displays status messages.
- **Methods**:
  - `initialize(): void`: Initializes the controller.
  - `handleViewCourses(): void`: Displays enrolled courses.
  - `handleWatchVideos(): void`: Displays available course videos.
  - `handleCheckTasks(): void`: Displays assigned tasks.
  - `handleBackLogin(): void`: Navigates to the login screen.
  - `setStatusLabel(Label statusLabel): void`: Overrides to set the status label.
- **Usage**: Controls `StudentController.fxml`.

### Main Class

#### Main (Extends Application)
**Purpose**: Entry point for the JavaFX application.
- **Methods**:
  - `start(Stage primaryStage): void`: Initializes the application, loads `login.fxml`, and sets up services.
  - `stop(): void`: Closes the database connection pool.
  - `main(String[] args): void`: Launches the JavaFX application.
- **Usage**: Starts the application and manages the application lifecycle.

## Database Connectivity
The application uses PostgreSQL with HikariCP for connection pooling. The `Database` class configures the connection pool as follows:
- **JDBC URL**: `jdbc:postgresql://localhost:5432/oop2`
- **Username**: `postgres`
- **Password**: `z20102006`
- **Driver**: `org.postgresql.Driver`
- **Max Pool Size**: 10

### Setup Instructions
1. **Install PostgreSQL**:
   - Ensure PostgreSQL is installed and running on `localhost:5432`.
   - Create a user `postgres` with password `z20102006` or update the `Database` class with your credentials.
2. **Create Database**:
   - Run the `setup_database.sql` script to create the `oop2` database and tables.
3. **Dependencies**:
   - The `pom.xml` includes dependencies for PostgreSQL, HikariCP, JavaFX, and BCrypt.
   - Run `mvn install` to download dependencies.
4. **Run the Application**:
   - Use the `run.sh` script or run `mvn javafx:run` from the project root.

## Security Considerations
- **Password Hashing**: Uses BCrypt to hash passwords before storage.
- **Prepared Statements**: Prevents SQL injection by using parameterized queries.
- **Session Management**: Uses `UserSession` to track the current user securely.

## Limitations
- Some features (e.g., permissions management, reset settings) are placeholders and require further implementation.
- File-based import/export in `DataManagementController` is basic and could be enhanced with a file chooser.
- Error handling is console-based in some cases; a more robust logging system could be added.

## Future Enhancements
- Implement file chooser for import/export.
- Add advanced settings management (e.g., permissions, roles).
- Integrate logging framework (e.g., SLF4J).
- Add unit tests for services and controllers.
