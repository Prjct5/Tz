# Student Management System Documentation

## 1. Project Overview

The Student Management System is a JavaFX-based desktop application designed to manage educational resources and user interactions within an academic institution. Developed in IntelliJ IDEA on Windows, it uses PostgreSQL as the backend database and incorporates JavaFX for the graphical user interface (GUI). The system supports three user roles—Admin, Teacher, and Student—each with role-specific functionalities, such as user management, course handling, and task viewing.

### 1.1 Purpose
The system aims to:
- Facilitate user authentication (login and registration).
- Allow admins to manage users, system settings, data, and reports.
- Enable teachers to manage courses, assignments, and students.
- Provide students access to courses, videos, and tasks.
- Demonstrate Object-Oriented Programming (OOP) concepts: encapsulation, inheritance, polymorphism, and abstraction.

### 1.2 Key Features
- **Authentication**: Login and registration with role-based access (Admin, Teacher, Student).
- **Admin Dashboard**: Manage users, import/export data, configure settings, and view reports.
- **Teacher Dashboard**: Manage courses, add assignments, and view student details.
- **Student Dashboard**: View enrolled courses, watch course videos, and check assignments.
- **Database Integration**: Store and retrieve user, course, and assignment data using PostgreSQL.
- **GUI**: Responsive JavaFX interfaces styled with CSS.

### 1.3 Development Environment
- **IDE**: IntelliJ IDEA
- **Platform**: Windows
- **Language**: Java (with JavaFX for GUI)
- **Database**: PostgreSQL
- **Libraries**:
  - JavaFX (for GUI components and FXML)
  - PostgreSQL JDBC Driver (for database connectivity)

## 2. System Architecture

The system follows a Model-View-Controller (MVC)-like architecture, with components organized as follows:

- **Model**: The `Database` class and PostgreSQL schema (`export.sql`) manage data storage and retrieval.
- **View**: FXML files (`login.fxml`, `AdminDashboard.fxml`, etc.) define the GUI structure, styled by CSS files (`style.css`, `designSTU.css`).
- **Controller**: Java classes (e.g., `LoginController`, `ManageUsersController`) handle user interactions and business logic, binding FXML elements to functionality.
- **Utility**: Classes like `Info`, `byInfo`, and `UserManager` provide supporting functionality, such as storing user context and managing user operations.

### 2.1 Package Structure
All Java classes reside in the `com.projecttest.projecttest` package, ensuring a unified namespace.

### 2.2 Component Interaction
- **Main Entry**: The `Main` class initializes the JavaFX application, loading the login interface (`login.fxml`).
- **Navigation**: Controllers use `FXMLLoader` to transition between views (e.g., from `AdminDashboardController` to `ManageUsersController`).
- **Database Access**: The `Database` class provides static methods for CRUD operations, used by controllers and `UserManager`.
- **User Management**: The `UserManager` class, integrated into `ManageUsersController`, encapsulates user addition and deletion logic, demonstrating OOP principles.

## 3. Components

### 3.1 Java Classes and Interfaces

#### 3.1.1 Info
- **File**: `Info.java`
- **Purpose**: Stores static user context (current user and role) for role-based access control.
- **Attributes**:
  - `public static String currentUser`: Stores the username of the logged-in user.
  - `public static String currentRole`: Stores the role (Admin, Teacher, Student).
- **Methods**: None.
- **Role in OOP**:
  - **Inheritance**: Base class for `byInfo` and indirectly `AbstractUserManager`, enabling a class hierarchy.

#### 3.1.2 byInfo (Abstract)
- **File**: `byInfo.java`
- **Purpose**: An abstract class extending `Info`, intended as a placeholder for future extensions.
- **Extends**: `Info`
- **Attributes**: None.
- **Methods**: None.
- **Role in OOP**:
  - **Inheritance**: Extends `Info`, providing a potential base for other classes (though currently unused beyond hierarchy).
  - **Abstraction**: As an abstract class, it defines a type without implementation.

#### 3.1.3 UserOperations (Interface)
- **File**: `UserManager.java`
- **Purpose**: Defines a contract for user management operations, promoting abstraction.
- **Methods**:
  - `public boolean addUser(String username, String email, String password, String role)`: Adds a user to the database.
  - `public boolean removeUser(int userId)`: Deletes a user by ID.
  - `public String getUserDetails(int userId)`: Retrieves formatted user details.
- **Role in OOP**:
  - **Abstraction**: Provides a contract for user management, hiding implementation details.
  - **Polymorphism**: Enables different implementations (e.g., by `UserManager`).

#### 3.1.4 AbstractUserManager (Abstract)
- **File**: `UserManager.java`
- **Purpose**: Abstract base class for user management, encapsulating a `Database` instance and common logic.
- **Extends**: `Info` (simplified, as `byInfo` is empty)
- **Attributes**:
  - `private final Database database`: A `Database` instance for database operations.
- **Methods**:
  - `protected AbstractUserManager()`: Constructor initializing the `database` field.
  - `protected Database getDatabase()`: Getter for the `database` field.
  - `protected boolean userExists(String username)`: Checks if a username exists in the database.
  - `protected abstract String formatUserDetails(Map<String, String> userData)`: Abstract method for formatting user details.
- **Role in OOP**:
  - **Encapsulation**: The `database` field is private, accessible only via a protected getter.
  - **Inheritance**: Extends `Info`, serving as a base for `UserManager`.
  - **Abstraction**: Defines an abstract method, requiring subclasses to provide implementation.

#### 3.1.5 UserManager
- **File**: `UserManager.java`
- **Purpose**: Concrete class implementing user management operations, integrated with `ManageUsersController`.
- **Extends**: `AbstractUserManager`
- **Implements**: `UserOperations`
- **Attributes**: None (inherits `database` from `AbstractUserManager`).
- **Methods**:
  - `public UserManager()`: Constructor calling the superclass constructor.
  - `public boolean addUser(String username, String email, String password, String role)`: Validates inputs, checks for existing usernames, and delegates to `Database.registerUser`.
  - `public boolean removeUser(int userId)`: Deletes a user from the database by ID.
  - `public String getUserDetails(int userId)`: Retrieves and formats user details by ID.
  - `public String getUserDetails(String username)`: Overloaded method to retrieve details by username.
  - `protected String formatUserDetails(Map<String, String> userData)`: Formats user data into a string (e.g., "Username: x, Email: y, Role: z").
- **Role in OOP**:
  - **Encapsulation**: Validates inputs and encapsulates database access via inherited `database`.
  - **Inheritance**: Extends `AbstractUserManager`, inheriting its methods and attributes.
  - **Polymorphism**: Implements `UserOperations` methods and overloads `getUserDetails`.
  - **Abstraction**: Provides concrete implementation for the abstract `formatUserDetails` method.

#### 3.1.6 Database
- **File**: `Database.java`
- **Purpose**: Centralizes database operations, providing static methods for CRUD operations on PostgreSQL.
- **Attributes**:
  - `private static final String URL = "jdbc:postgresql://localhost:5432/oop2"`: Database connection URL.
  - `private static final String USER = "postgres"`: Database username.
  - `private static final String PASSWORD = "z20102006"`: Database password.
- **Methods**:
  - `public static boolean registerUser(String username, String email, String password, String role)`: Registers a new user, checking for username uniqueness.
  - `public static Map<String, String> loginUser(String username, String password)`: Authenticates a user, returning user data if successful.
  - `public static boolean addStudent(int userId, String studentId, String organisation, String level, double gpa)`: Adds a student record.
  - `public static boolean addCourse(String title, String description, int instructorId, String requirements)`: Adds a course.
  - `public static boolean addCourseVideo(int courseId, String title, String description, String videoUrl, String videoFilePath, int duration, int orderNumber)`: Adds a course video.
  - `public static boolean enrollStudentInCourse(int studentId, int courseId)`: Enrolls a student in a course.
  - `public static boolean addAssignment(int courseId, String title, String description, Timestamp dueDate, int maxGrade)`: Adds an assignment.
  - `public static boolean submitAssignment(int assignmentId, int studentId, String submissionFile, String submissionText)`: Submits an assignment.
  - `public static boolean saveSetting(String name, String value)`: Saves or updates a system setting.
  - `public static ResultSet getCourseVideos(int courseId)`: Retrieves videos for a course.
  - `public static ResultSet getAssignments(int courseId)`: Retrieves assignments for a course.
  - `public static Connection getConnection()`: Establishes a database connection.
- **Role in OOP**:
  - **Encapsulation**: Connection details are private and static, with controlled access via methods.

#### 3.1.7 Main
- **File**: `Main.java`
- **Purpose**: Entry point for the JavaFX application, launching the login interface.
- **Extends**: `Application` (javafx.application)
- **Attributes**: None.
- **Methods**:
  - `public void start(Stage primaryStage)`: Loads `login.fxml` and sets up the initial window.
  - `public static void main(String[] args)`: Launches the JavaFX application.
- **Role in OOP**:
  - **Inheritance**: Extends `Application` to leverage JavaFX functionality.

#### 3.1.8 LoginController
- **File**: `LoginController.java`
- **Purpose**: Manages the login interface, authenticating users via `Database`.
- **Attributes**:
  - `private TextField usernameField` (FXML): Input field for username.
  - `private PasswordField passwordField` (FXML): Input field for password.
  - `private Label statusLabel` (FXML): Displays login status.
- **Methods**:
  - `private void handleLogin()`: Validates inputs, calls `Database.loginUser`, and navigates to role-specific dashboards (AdminDashboard, haythamSTUDENT, or Mesarw3Teacher).
  - `private void handleGoToRegister()`: Navigates to the registration interface (`register.fxml`).
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private, bound to UI elements.

#### 3.1.9 RegisterController
- **File**: `RegisterController.java`
- **Purpose**: Manages the registration interface, creating new users via `Database`.
- **Attributes**:
  - `private TextField usernameField` (FXML): Input field for username.
  - `private TextField emailField` (FXML): Input field for email.
  - `private PasswordField passwordField` (FXML): Input field for password.
  - `private ComboBox<String> roleComboBox` (FXML): Dropdown for selecting role.
  - `private Label statusLabel` (FXML): Displays registration status.
- **Methods**:
  - `private void handleRegister()`: Validates inputs, checks email format, and calls `Database.registerUser`.
  - `private void handleGoToLogin()`: Navigates to the login interface (`login.fxml`).
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private, with validation logic encapsulated.

#### 3.1.10 AdminDashboardController
- **File**: `AdminDashboardController.java`
- **Purpose**: Manages the admin dashboard, providing navigation to other admin interfaces.
- **Attributes**:
  - `private Button manageUsersButton` (FXML): Navigates to user management.
  - `private Button systemSettingsButton` (FXML): Navigates to system settings.
  - `private Button dataManagementButton` (FXML): Navigates to data management.
  - `private Button reportsButton` (FXML): Navigates to reports.
  - `private Button logoutButton` (FXML): Logs out to login screen.
- **Methods**:
  - `private void openNewWindow(String fxmlFile, String title)`: Loads a new FXML view and closes the current window.
  - `private void handleManageUsers()`: Opens `ManageUsers.fxml`.
  - `private void handleSystemSettings()`: Opens `SystemSettings.fxml`.
  - `private void handleDataManagement()`: Opens `DataManagement.fxml`.
  - `private void handleReports()`: Opens `Reports.fxml`.
  - `private void handleLogout()`: Opens `login.fxml`.
- **Role in OOP**:
  - **Encapsulation**: FXML buttons are private, with navigation logic encapsulated.

#### 3.1.11 DataManagementController
- **File**: `DataManagementController.java`
- **Purpose**: Manages data import/export and student addition.
- **Attributes**:
  - `private Button exportButton` (FXML): Triggers data export (placeholder).
  - `private Button importButton` (FXML): Triggers data import (placeholder).
  - `private Button backButton` (FXML): Returns to admin dashboard.
  - `private TextField fullNameField` (FXML): Input for student name.
  - `private TextField gpaField` (FXML): Input for student GPA.
  - `private Label statusLabel` (FXML): Displays operation status.
- **Methods**:
  - `private void openNewWindow(String fxmlFile, String title)`: Loads a new FXML view.
  - `private void handleExportData()`: Placeholder for exporting data.
  - `private void handleImportData()`: Placeholder for importing data.
  - `private void handleBack()`: Opens `AdminDashboard.fxml`.
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private, with placeholder logic.

#### 3.1.12 SystemSettingsController
- **File**: `SystemSettingsController.java`
- **Purpose**: Manages system settings (placeholder functionality).
- **Attributes**:
  - `private Button languageButton` (FXML): Placeholder for language settings.
  - `private Button permissionsButton` (FXML): Placeholder for permissions.
  - `private Button resetButton` (FXML): Placeholder for system reset.
  - `private Button backButton` (FXML): Returns to admin dashboard.
  - `private Label statusLabel` (FXML): Displays status.
- **Methods**:
  - `private void initialize()`: Initializes the controller (empty).
  - `private void handleChangeLanguage()`: Placeholder for language change.
  - `private void handleManagePermissions()`: Placeholder for permissions.
  - `private void handleReset()`: Placeholder for reset.
  - `private void handleBack()`: Opens `AdminDashboard.fxml`.
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private.

#### 3.1.13 StudentController
- **File**: `StudentController.java`
- **Purpose**: Manages the student dashboard, displaying courses, videos, and tasks.
- **Attributes**:
  - `private VBox contentArea` (FXML): Container for dynamic content.
  - `private Button coursesButton` (FXML): Displays courses.
  - `private Button videosButton` (FXML): Displays videos.
  - `private Button tasksButton` (FXML): Displays tasks.
  - `private Button backButtonPrev` (FXML): Placeholder for previous view.
  - `private Button backButtonLogin` (FXML): Returns to login.
  - `private Label statusLabel` (FXML): Displays status.
- **Methods**:
  - `private void initialize()`: Initializes the controller.
  - `private void handleViewCourses()`: Placeholder for course display.
  - `private void handleWatchVideos()`: Placeholder for video display.
  - `private void handleCheckTasks()`: Placeholder for task display.
  - `private void handleBackLogin()`: Opens `login.fxml`.
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private.

#### 3.1.14 TeacherController
- **File**: `TeacherController.java`
- **Purpose**: Manages the teacher dashboard, handling courses, assignments, and students.
- **Attributes**:
  - `private VBox contentArea` (FXML): Container for content.
  - `private Button coursesButton` (FXML): Manages courses.
  - `private Button addTaskButton` (FXML): Adds assignments.
  - `private Button studentsButton` (FXML): Views students.
  - `private Button backButtonPrev` (FXML): Placeholder for previous view.
  - `private Button backButtonLogin` (FXML): Returns to login.
  - `private Label statusLabel` (FXML): Displays status.
- **Methods**:
  - `private void initialize()`: Initializes the controller.
  - `private void handleManageCourses()`: Placeholder for course management.
  - `private void handleAddTask()`: Placeholder for adding assignments.
  - `private void handleViewStudents()`: Placeholder for viewing students.
  - `private void handleBackPrev()`: Placeholder for previous view.
  - `private void handleBackLogin()`: Opens `login.fxml`.
- **Role in OOP**:
  - **Encapsulation**: FXML fields are private.

#### 3.1.15 ReportsController
- **File**: `ReportsController.java`
- **Purpose**: Displays reports, such as user role counts.
- **Attributes**:
  - `private TableView<ReportItem> reportsTable` (FXML): Displays report data.
  - `private TableColumn<ReportItem, String> categoryColumn` (FXML): Shows report category.
  - `private TableColumn<ReportItem, Integer> countColumn` (FXML): Shows count.
  - `private Button refreshButton` (FXML): Refreshes the table.
  - `private Button backButton` (FXML): Returns to admin dashboard.
  - `private Label statusLabel` (FXML): Displays status.
  - `private ObservableList<ReportItem> reportsList`: Stores report data.
- **Inner Class: ReportItem**
  - **Attributes**:
    - `private final String category`: Report category (e.g., role).
    - `private final int count`: Number of items in category.
  - **Methods**:
    - `public ReportItem(String category, int count)`: Constructor.
    - `public String getCategory()`: Getter for category.
    - `public int getCount()`: Getter for count.
- **Methods**:
  - `private void initialize()`: Sets up the table columns.
  - `private void loadReports()`: Loads role counts from `Database`.
  - `private void handleRefresh()`: Calls `loadReports`.
  - `private void handleBack()`: Opens `AdminDashboard.fxml`.
- **Role in OOP**:
  - **Encapsulation**: Private fields and inner class.
  - **Aggregation**: Contains `ReportItem` instances.

#### 3.1.16 ManageUsersController
- **File**: `ManageUsersController.java`
- **Purpose**: Manages user addition and deletion, integrated with `UserManager`.
- **Attributes**:
  - `private TextField usernameField` (FXML): Input for username.
  - `private TextField emailField` (FXML): Input for email.
  - `private TextField passwordField` (FXML): Input for password.
  - `private ComboBox<String> roleComboBox` (FXML): Dropdown for role.
  - `private Button addButton` (FXML): Adds a user.
  - `private Button deleteButton` (FXML): Deletes a user.
  - `private Button backButton` (FXML): Returns to admin dashboard.
  - `private TableView<User> usersTable` (FXML): Displays user data.
  - `private TableColumn<User, Integer> idColumn` (FXML): Shows user ID.
  - `private TableColumn<User, String> usernameColumn` (FXML): Shows username.
  - `private TableColumn<User, String> emailColumn` (FXML): Shows email.
  - `private TableColumn<User, String> roleColumn` (FXML): Shows role.
  - `private Label statusLabel` (FXML): Displays status.
  - `private ObservableList<User> usersList`: Stores user data.
  - `private final UserManager userManager`: Handles user operations.
- **Inner Class: User**
  - **Attributes**:
    - `private final int id`: User ID.
    - `private final String username`: Username.
    - `private final String email`: Email.
    - `private final String role`: Role.
  - **Methods**:
    - `public User(int id, String username, String email, String role)`: Constructor.
    - `public int getId()`: Getter for ID.
    - `public String getUsername()`: Getter for username.
    - `public String getEmail()`: Getter for email.
    - `public String getRole()`: Getter for role.
- **Methods**:
  - `private void initialize()`: Sets up table columns.
  - `private void loadUsers()`: Loads user data from `Database`.
  - `private void openNewWindow(String fxmlFile, String title)`: Loads a new FXML view.
  - `private boolean isEmailExists(String email)`: Checks if an email exists in `Database`.
  - `private void handleAddUser()`: Validates inputs, checks email, and calls `userManager.addUser`.
  - `private void handleDeleteUser()`: Calls `userManager.removeUser` for the selected user.
  - `private void handleBack()`: Opens `AdminDashboard.fxml`.
- **Role in OOP**:
  - **Encapsulation**: Private fields and `userManager` dependency.
  - **Aggregation**: Contains `User` instances.
  - **Polymorphism**: Uses `UserManager`’s interface methods.

### 3.2 FXML Files

FXML files define the GUI structure, bound to controllers via `@FXML` annotations.

#### 3.2.1 login.fxml
- **Purpose**: Login interface.
- **Elements**:
  - `TextField` (usernameField): Username input.
  - `PasswordField` (passwordField): Password input.
  - `Button`: Login button (calls `handleLogin`).
  - `Button`: Register button (calls `handleGoToRegister`).
  - `Label` (statusLabel): Displays status messages.
- **Controller**: `LoginController`

#### 3.2.2 register.fxml
- **Purpose**: Registration interface.
- **Elements**:
  - `TextField` (usernameField): Username input.
  - `TextField` (emailField): Email input.
  - `PasswordField` (passwordField): Password input.
  - `ComboBox` (roleComboBox): Role selection.
  - `Button`: Register button (calls `handleRegister`).
  - `Button`: Login button (calls `handleGoToLogin`).
  - `Label` (statusLabel): Displays status.
- **Controller**: `RegisterController`

#### 3.2.3 AdminDashboard.fxml
- **Purpose**: Admin dashboard for navigation.
- **Elements**:
  - `Button` (manageUsersButton): Opens user management.
  - `Button` (systemSettingsButton): Opens settings.
  - `Button` (dataManagementButton): Opens data management.
  - `Button` (reportsButton): Opens reports.
  - `Button` (logoutButton): Logs out.
- **Controller**: `AdminDashboardController`

#### 3.2.4 ManageUsers.fxml
- **Purpose**: User management interface.
- **Elements**:
  - `TextField` (usernameField, emailField, passwordField): User input fields.
  - `ComboBox` (roleComboBox): Role selection.
  - `Button` (addButton, deleteButton, backButton): User operations and navigation.
  - `TableView` (usersTable): Displays users.
  - `TableColumn` (idColumn, usernameColumn, emailColumn, roleColumn): Table columns.
  - `Label` (statusLabel): Displays status.
- **Controller**: `ManageUsersController`

#### 3.2.5 DataManagement.fxml
- **Purpose**: Data import/export and student addition.
- **Elements**:
  - `Button` (exportButton, importButton, backButton): Data operations and navigation.
  - `TextField` (fullNameField, gpaField): Student input fields.
  - `Label` (statusLabel): Displays status.
- **Controller**: `DataManagementController`

#### 3.2.6 SystemSettings.fxml
- **Purpose**: System settings interface.
- **Elements**:
  - `Button` (languageButton, permissionsButton, resetButton, backButton): Settings operations.
  - `Label` (statusLabel): Displays status.
- **Controller**: `SystemSettingsController`

#### 3.2.7 haythamSTUDENT.fxml
- **Purpose**: Student dashboard.
- **Elements**:
  - `VBox` (contentArea): Dynamic content container.
  - `Button` (coursesButton, videosButton, tasksButton, backButtonPrev, backButtonLogin): Student actions.
  - `Label` (statusLabel): Displays status.
- **Controller**: `StudentController`

#### 3.2.8 Mesarw3Teacher.fxml
- **Purpose**: Teacher dashboard.
- **Elements**:
  - `VBox` (contentArea): Dynamic content container.
  - `Button` (coursesButton, addTaskButton, studentsButton, backButtonPrev, backButtonLogin): Teacher actions.
  - `Label` (statusLabel): Displays status.
- **Controller**: `TeacherController`

#### 3.2.9 Reports.fxml
- **Purpose**: Reports interface.
- **Elements**:
  - `TableView` (reportsTable): Displays report data.
  - `TableColumn` (categoryColumn, countColumn): Report columns.
  - `Button` (refreshButton, backButton): Report actions.
  - `Label` (statusLabel): Displays status.
- **Controller**: `ReportsController`

### 3.3 CSS Files

#### 3.3.1 style.css
- **Purpose**: Styles login and registration interfaces.
- **Styles**:
  - `.title`: Font size 24px, dark color.
  - `.text-field, .password-field`: 14px font, 250px width, rounded borders.
  - `.button`: Blue background, white text, bold, rounded, hover effect.

#### 3.3.2 designSTU.css
- **Purpose**: Styles student and other interfaces.
- **Styles**:
  - `.root`: Gradient background, Segoe UI font.
  - `.button`: Blue background, white text, shadow effect, hover effect.
  - `.text-field`: White background, rounded borders.
  - `.label`: Bold, dark text.
  - `.table-view`: White background, rounded borders, styled headers and rows.

### 3.4 Database Schema
- **File**: `export.sql`
- **Purpose**: Defines the PostgreSQL database structure.
- **Tables**:
  - **users**:
    - `id: integer` (PK): User ID.
    - `username: varchar(50)`: Unique username.
    - `email: varchar(100)`: User email.
    - `password: varchar(255)`: Plain-text password.
    - `role: varchar(20)`: Role (Admin, Teacher, Student).
    - `created_at: timestamp`: Creation timestamp.
  - **students**:
    - `id: integer` (PK): Student ID.
    - `user_id: integer` (FK to users): Links to user.
    - `student_id: varchar(50)`: Student identifier.
    - `organisation: varchar(100)`: Organization name.
    - `level: varchar(50)`: Academic level.
    - `gpa: numeric(3,2)`: GPA.
    - `created_at: timestamp`: Creation timestamp.
  - **courses**:
    - `id: integer` (PK): Course ID.
    - `title: varchar(100)`: Course title.
    - `description: text`: Course description.
    - `instructor_id: integer` (FK to users): Instructor.
    - `requirements: text`: Prerequisites.
    - `created_at: timestamp`: Creation timestamp.
  - **course_videos**:
    - `id: integer` (PK): Video ID.
    - `course_id: integer` (FK to courses): Linked course.
    - `title: varchar(100)`: Video title.
    - `description: text`: Video description.
    - `video_url: varchar(255)`: Video URL.
    - `video_file_path: varchar(255)`: File path (optional).
    - `duration: integer`: Video duration (seconds).
    - `order_number: integer`: Display order.
    - `created_at: timestamp`: Creation timestamp.
  - **course_enrollments**:
    - `student_id: integer` (FK to students): Student.
    - `course_id: integer` (FK to courses): Course.
    - `enrollment_date: timestamp`: Enrollment timestamp.
  - **assignments**:
    - `id: integer` (PK): Assignment ID.
    - `course_id: integer` (FK to courses): Linked course.
    - `title: varchar(100)`: Assignment title.
    - `description: text`: Assignment description.
    - `due_date: timestamp`: Due date.
    - `max_grade: integer`: Maximum grade.
    - `created_at: timestamp`: Creation timestamp.
  - **assignment_submissions**:
    - `id: integer` (PK): Submission ID.
    - `assignment_id: integer` (FK to assignments): Linked assignment.
    - `student_id: integer` (FK to students): Student.
    - `submission_file: varchar(255)`: File path.
    - `submission_text: text`: Submission text.
    - `grade: integer`: Assigned grade.
    - `submitted_at: timestamp`: Submission timestamp.
  - **settings**:
    - `id: integer` (PK): Setting ID.
    - `setting_name: varchar(50)`: Setting name (unique).
    - `setting_value: text`: Setting value.
    - `updated_at: timestamp`: Update timestamp.
- **Constraints**:
  - Foreign keys ensure referential integrity (e.g., `students.user_id` references `users.id`).
  - `users.role` is constrained to Admin, Teacher, or Student.
- **Sample Data**:
  - Includes test users (e.g., `testuser`), a course (`Math 101`), a video, and an assignment.

## 4. OOP Concepts

The project demonstrates all OOP principles, enhanced by the `UserManager` integration:

### 4.1 Encapsulation
- **UserManager**: The `database` field is private, accessible via a protected getter. Input validation in `addUser` ensures data integrity.
- **Controllers**: FXML fields (e.g., `usernameField`) are private, bound to UI elements, with logic encapsulated in methods.
- **Database**: Connection details (`URL`, `USER`, `PASSWORD`) are private and static.

### 4.2 Inheritance
- **UserManager**: Extends `AbstractUserManager`, which extends `Info`, forming a hierarchy.
- **byInfo**: Extends `Info`, providing a base (though unused beyond hierarchy).
- **Main**: Extends `Application` for JavaFX functionality.

### 4.3 Polymorphism
- **UserManager**:
  - Implements `UserOperations`, providing concrete implementations for `addUser`, `removeUser`, and `getUserDetails`.
  - Overloads `getUserDetails` (by `userId` and `username`).
  - Overrides `formatUserDetails` from `AbstractUserManager`.
- **ManageUsersController**: Uses `UserManager`’s interface methods, leveraging polymorphism.

### 4.4 Abstraction
- **UserOperations**: Interface defining user management operations, hiding implementation details.
- **AbstractUserManager**: Abstract class with an abstract `formatUserDetails` method, enforcing abstraction for subclasses.

## 5. Setup and Running Instructions

### 5.1 Prerequisites
- **Java**: JDK 17 or later.
- **JavaFX**: JavaFX SDK (configured in IntelliJ).
- **PostgreSQL**: Version 17.4, with `pg_dump` for schema import.
- **IntelliJ IDEA**: Community or Ultimate edition.
- **Dependencies**:
  - PostgreSQL JDBC Driver (included in project dependencies).
  - JavaFX libraries.

### 5.2 Database Setup
1. Install PostgreSQL and ensure it’s running on `localhost:5432`.
2. Create a database named `oop2`.
3. Set the username to `postgres` and password to `z20102006` (or update `Database.java` if different).
4. Import the schema:
   ```bash
   psql -U postgres -d oop2 -f export.sql
   ```
5. Verify the tables (`users`, `students`, etc.) and sample data.

### 5.3 Project Setup in IntelliJ
1. Open IntelliJ and import the project as a Java project.
2. Configure the JavaFX SDK:
   - Add JavaFX libraries to the module path.
   - Set VM options: `--module-path /path/to/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml`
3. Add the PostgreSQL JDBC driver to the project dependencies.
4. Place FXML files in the `resources` directory and CSS files in the appropriate resource path.
5. Ensure all Java files are in `src/com/projecttest/projecttest`.

### 5.4 Running the Application
1. Run `Main.java` as the main class.
2. The login interface (`login.fxml`) will appear.
3. Use test credentials (e.g., username: `testuser`, password: `password123`, role: `Student`) or register a new user.
4. Navigate based on role:
   - **Admin**: Access user management, data, settings, and reports.
   - **Teacher**: Manage courses, assignments, and students.
   - **Student**: View courses, videos, and tasks.

## 6. Limitations and Future Improvements
- **Placeholder Functionality**: `StudentController`, `TeacherController`, and `SystemSettingsController` have placeholder methods that need implementation.
- **Security**: Passwords are stored in plain text in the `users` table; hashing (e.g., BCrypt) should be implemented.
- **Error Handling**: Some controllers lack robust error handling for database failures.
- **UI Completeness**: Some interfaces (e.g., reports) are basic and could benefit from enhanced visualizations.

## 7. Conclusion
The Student Management System is a robust JavaFX application that effectively manages educational resources with role-based access. The integration of `UserManager` enhances its OOP compliance, demonstrating encapsulation, inheritance, polymorphism, and abstraction. The system is well-structured, with a clear separation of concerns via MVC-like architecture, and is ready for academic evaluation.

**Developed by**: [Your Name]
**Date**: May 23, 2025