### **Notes: How to Integrate Jenkins with JFrog | Jenkins JFrog Maven Tomcat CI/CD Pipeline | Upload Artifacts**

---

## **Table of Contents**

1. **[Build Tools Fundamentals](#1-build-tools-fundamentals)**
2. **[Apache Maven](#2-apache-maven)**
3. **[JFrog Artifactory](#3-jfrog-artifactory)**
4. **[Apache Tomcat](#4-apache-tomcat)**
5. **[Jenkins CI/CD Pipeline](#5-jenkins-cicd-pipeline)**
6. **[Practical Implementation](#6-practical-implementation)**
7. **[Conclusion](#7-conclusion)**

---

#### **Overview**
- **Objective**: Upload artifacts to JFrog repository using Jenkins CI/CD pipeline, build a Java-based application, and deploy it to a Tomcat server.
- **Key Steps**:
  - Introduction to JFrog and Maven build tools.
  - Creation of Jenkins CI/CD pipeline.
  - Uploading and downloading artifacts from JFrog repository.
  - Installation of Apache Tomcat server.
  - Deployment of the Java application.

---

## **1. BUILD TOOLS FUNDAMENTALS**

### **1.1 Build Tools Introduction**
- **Definition**: Build tools are commonly known as programs that automate the process of building an executable application from source code.
- **Activities**: This building process includes activities like compiling, linking, and packaging the code into an executable form.
- **Market**: There are multiple build tools available in the market.
- **Popular Examples**:
  - **Apache Ant**: Traditional build tool with XML-based configuration
  - **Apache Maven**: Project Object Model (POM) based build tool for Java projects
  - **Gradle**: Modern build tool with Groovy/Kotlin DSL
- **Language-Specific**: Tools vary based on programming language. For Java, Apache Maven is commonly used.

### **1.2 Software Build Process**
- **Overview**: There are multiple phases during software build process, so different tools are used for each phase and which tool is for which phase, everything is defined under `pom.xml`.
- **Build Process Phases**:
  - **Compilation**: Uses `gcc compiler` to compile source code
  - **Code Review**: Uses `pmd` and `SonarQube` for static code analysis and quality checks
  - **Test Cases**: Uses `Junit` for unit testing
  - **Test Case Report**: Uses `Sure-Fire` for generating test reports
  - **Code Coverage**: Uses `Cobertura` for code coverage analysis
  - **Package**: Creates `application.war` as the final artifact

---

## **2. APACHE MAVEN**

### **2.1 Maven Basics**
- **Purpose**: A build tool for Java-based projects.
- **Key Features**:
  - Based on **Project Object Model (POM)**.
  - Resolves dependencies defined in `pom.xml`.
  - Downloads dependencies from the internet if not available locally.

### **2.2 Java Project Structure**
- **Standard Maven Directory Structure**:
  - **`src` folder**: Contains source code and test cases
    - **`main` folder**: Contains Java source code
    - **`test` folder**: Contains test cases
  - **`pom.xml` file**: Project Object Model configuration file
  - **Additional files**: `build.properties`, `build.xml` (for Ant projects)

### **2.3 Maven Commands**
- **`mvn compile`**: Generates compiled code in `Target/classes` folder. Inside classes folder there will be compiled code `.class` files.
- **`mvn test`**: Creates test reports in `Target/surefire-reports` folder which will contain test case report.
- **`mvn package`**: Creates an executable `.war` file in `Target` (e.g., `target/example.war`) which is basically the file that we will deploy to Tomcat server.

### **2.4 pom.xml Configuration for Build Tools**
- **PMD Plugin**: 
  ```xml
  <!-- PMD -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
  </plugin>
  ```
- **JUnit Testing**:
  ```xml
  <!-- TESTING -->
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
  </dependency>
  ```
- **Surefire Reports**:
  ```xml
  <reporting>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-report-plugin</artifactId>
      </plugin>
    </plugins>
  </reporting>
  ```
- **Cobertura Coverage**:
  ```xml
  <plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>cobertura-maven-plugin</artifactId>
    <version>${cobertura.version}</version>
  </plugin>
  ```

---

## **3. JFROG ARTIFACTORY**

### **3.1 JFrog Artifactory Overview**
- **Purpose**: Repository for storing and managing build artifacts.
- **Repository Types**: Three types of repositories in JFrog Artifactory for each Build/Package Manager Tool (Maven, NPM, etc.)

#### **Local Repository**
- **Description**: This is basically a repository where we store our artifacts (tar, war, zip, images, etc.)
- **Privacy**: This is private to organization and not publicly available.
- **Example**: Our application.war post successful build always placed here.
- **Advantage**: Fully tested and versioned build will be available all time.

#### **Remote Repository**
- **Description**: This is basically a repository which is linked to remote repository.
- **Alternative Name**: This can also be called a mirror to remote repository.
- **Usage in Organizations**: Usually in organizations each machine cannot access public sites so these machines reach out to public site using a proxy and then that content (dependencies) which is fetched from remote public repository is now stored in local repository as cache. Next time when similar kind of request will come then it will be served from cache.
- **Advantage**: It saves time and network bandwidth for each similar request.

#### **Virtual Repository**
- **Description**: This repository itself does nothing rather it combines other repositories.
- **Functionality**: In this repository we can group LOCAL + REMOTE repositories.
- **Advantage**: No need to give individual URLs of hosted and proxy rather give just one URL in configuration.
- **Purpose**: Provides a unified interface to multiple repository types.
- **Diagram Concept**: 
  ```
  ┌─────────────────────────────────────────────────────────────┐
  │                    EXTERNAL REPOSITORIES                   │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐       │
  │  │maven-central│  │typesafe     │  │repo.scala-  │       │
  │  │             │  │ivy-releases │  │sbt.org      │       │
  │  └─────────────┘  └─────────────┘  └─────────────┘       │
  │         ↓               ↓               ↓                 │
  └─────────────────────────────────────────────────────────────┘
                              │
                              ▼
  ┌─────────────────────────────────────────────────────────────┐
  │                 PROXY REPOSITORY                           │
  │              (Virtual Repository)                          │
  │                                                           │
  │         ┌─────────────────────────────────┐               │
  │         │    Single Unified Access        │               │
  │         │        Point (URL)              │               │
  │         └─────────────────────────────────┘               │
  └─────────────────────────────────────────────────────────────┘
                              │
                              ▼
  ┌─────────────────────────────────────────────────────────────┐
  │                    DEVELOPERS                              │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐       │
  │  │Developer #1 │  │Developer #2 │  │Developer #3 │       │
  │  │             │  │             │  │             │       │
  │  └─────────────┘  └─────────────┘  └─────────────┘       │
  └─────────────────────────────────────────────────────────────┘
  ```
  - Single proxy repository acts as a virtual repository providing unified access to multiple underlying repositories

#### **General Advantages**
  - Ensures tested and versioned builds.
  - Enhances security by avoiding direct internet downloads.
  - Saves bandwidth through caching.

### **3.2 JFrog OSS Artifactory Installation**
- **Prerequisites**:
  - Linux system (Debian/Ubuntu-based)
  - Internet connection for downloading packages
- **Installation Steps**:
  1. **Update system packages**:
     ```bash
     sudo apt update
     ```
  2. **Install Java Runtime Environment**:
     ```bash
     sudo apt install default-jre
     ```
  3. **Download JFrog Artifactory OSS**:
     ```bash
     wget https://releases.jfrog.io/artifactory/bintray-artifactory/org/artifactory/oss/jfrog-artifactory-oss/6.17.4/jfrog-artifactory-oss-6.17.4.zip
     ```
  4. **Install unzip utility**:
     ```bash
     sudo apt install unzip
     ```
  5. **Extract the downloaded package**:
     ```bash
     unzip jfrog-artifactory-oss-6.17.4.zip
     ```
  6. **Navigate to the bin directory**:
     ```bash
     cd artifactory-oss-6.17.4/bin/
     ```
  7. **Start JFrog Artifactory**:
     ```bash
     ./artifactory.sh start
     ```
- **Configuration**:
  - Default username: `admin`.
  - Default password: `password`.
  - Create repositories (local, remote, virtual).
- **Verification**:
  - Access Artifactory web interface at `http://localhost:8081`
  - Login with default credentials

### **3.3 JFrog Artifactory Initial Setup**
- **First Login**:
  - Navigate to `http://localhost:8081`
  - Use default credentials:
    - Username: `admin`
    - Password: `password`
  - Check "Remember me" option if desired
  - Click "Log In" button

- **Proxy Server Configuration** (Optional):
  - **Dialog Title**: "Configure a Proxy Server"
  - **Purpose**: "This lets you access resources remotely"
  - **Required Fields**:
    - **Proxy Key***: Unique identifier for the proxy
    - **Host***: Proxy server hostname/IP
    - **Port***: Proxy server port number
  - **Options**:
    - **System Default**: Checkbox for using system default proxy
    - **More Options**: Expandable section for additional settings
  - **Navigation**: Use "Skip" or "Next" buttons to proceed

- **Repository Creation**:
  - **Dialog Title**: "Create Repositories"
  - **Message**: "Select the package type(s) you want - we'll create the default repositories for you!"
  - **Note**: "Disabled package types already have default repositories configured"
  - **Available Package Types**:
    - **Top Row**: Gradle, Ivy, Maven, SBT, Generic, Bower, Chef, CocoaPods, Conan
    - **Middle Row**: CONDA, CRAN, Debian, Docker, Gems, Git LFS, Go, Helm, npm
    - **Bottom Row**: NuGet, Opkg, Composer, PyPI, Puppet, V, RPM
  - **Selection**: Highlight Maven for Java projects
  - **Navigation**: Use "Back", "Skip", or "Create" buttons

- **Onboarding Completion**:
  - **Message**: "Artifactory on-boarding complete!"
  - **Success Message**: "Congrats! These are the default repositories we created for you."
  - **Ready Message**: "You're now ready to speed up your software releases!"
  - **Next Steps**:
    - "Want to configure your client(s) and get started? Click the Set Me Up button for each repository."
    - "Want to learn more about different repository types? Consult the JFrog Artifactory User Guide."
  - **Default Repositories Created**:
    - `maven`
    - `libs-snapshot-local`
    - `libs-release-local`
    - `jcenter`
    - `libs-snapshot`
    - `libs-release`
  - **Completion**: Click "Finish" button to complete setup

### **3.4 JFrog Artifactory Dashboard Overview**
- **Dashboard Status**:
  - **Status Message**: "Artifactory is happily serving 0 artifacts"
  - **Version Information**: Artifactory Version 6.17.4
  - **Uptime**: Shows system uptime (e.g., "0d 0h 14m 37s")
  - **Latest Release**: Available version information

- **Default Repositories Created**:
  - `libs-release`
  - `libs-snapshot`
  - `artifactory-build-info`
  - `libs-release-local`
  - `libs-snapshot-local`
  - `jcenter`

- **Dashboard Sections**:
  - **Quick Search**: Package Search, Archive Search, Property Search, Checksum Search, JCenter Search
  - **Set Me Up**: Repository configuration and setup
  - **Activity Summaries**: Last Deployed Builds, Most Downloaded Artifacts

### **3.5 JFrog Artifactory Navigation**
- **Main Navigation**:
  - **Home**: Dashboard overview
  - **Artifacts**: Repository browser (currently selected)
  - **Packages**: Package management
  - **Search**: Advanced search functionality
  - **Builds**: Build information and management
  - **Admin**: System administration

- **Repository Browser**:
  - **Tree View**: Hierarchical repository structure
  - **Repository Details**: Package type, repository path, layout information
  - **Actions**: Set Me Up, Deploy, Actions dropdown

### **3.6 User Management in JFrog**
- **Access Path**: Admin → Security → Users
- **User Management Interface**:
  - **Title**: "Users Management"
  - **User Count**: Shows total number of users (e.g., "3 Users")
  - **Search Functionality**: "Filter by Name or Email" search bar
  - **Action Buttons**: "New", "Delete", "Unlock"

- **User Table Columns**:
  - **Name**: Username
  - **Email**: User email address
  - **Realm**: Authentication realm (e.g., "internal")
  - **Related Groups**: Associated user groups
  - **Admin**: Admin privileges status (green checkmark for admin users)
  - **Locked...**: Account lock status
  - **Last Login...**: Timestamp of last login

- **Default Users**:
  - **access-admin**: Internal realm user
  - **admin**: Internal realm user with admin privileges, last login timestamp
  - **anonymous**: Internal realm user

- **User Management Features**:
  - **Create Users**: "+ New" button for adding users
  - **User Actions**: Delete, Unlock functionality
  - **User Information**: Related Groups, Admin status, Locked status, Last Login
  - **Security Configuration**: Users, Groups, Permissions, Access Tokens

### **3.7 Creating New Users in JFrog**
- **Form Title**: "Add New User"
- **User Settings Section**:
  - **User Name***: Required field for username (e.g., "user1")
  - **Email Address***: Required field for email (e.g., "admin@admin.com")
  - **Privilege Options**:
    - **Admin Privileges**: Checkbox for granting admin access
    - **Disable UI Access**: Checkbox to restrict web interface access
    - **✔ Can Update Profile**: Checkbox for profile modification rights
    - **Disable Internal Password**: Checkbox with question mark for external authentication

- **Set Password Section**:
  - **Password***: Required field with masked characters
  - **Password Strength**: Indicator showing "Good" with green progress bar (~75%)
  - **Retype Password***: Required confirmation field with masked characters

- **Form Validation**:
  - Required fields marked with asterisk (*)
  - Password strength validation
  - Password confirmation requirement

### **3.8 Group Assignment in JFrog**
- **Related Groups Section**:
  - **Title**: "Related Groups"
  - **Dual List Interface**: Two side-by-side list boxes for group management
  - **Filter Functionality**: "Filter..." input field above each list box
  - **Navigation Arrows**: `<<`, `<`, `>`, `>>` for moving groups between lists
  - **Selection Highlighting**: Selected groups highlighted in light green with red border
  - **Active Button**: Single right arrow (`>`) highlighted with red border for moving selected groups

- **Group Management Process**:
  - **Available Groups**: Left list box shows available groups (e.g., "readers")
  - **Assigned Groups**: Right list box shows groups assigned to the user
  - **Selection**: Click to select groups in the left list
  - **Assignment**: Use arrow buttons to move selected groups to the right list
  - **Bulk Operations**: Double arrows (`<<`, `>>`) for moving all groups at once

- **User Permissions Section**:
  - **Repositories**: Repository-level permissions
  - **Builds**: Build-level permissions

### **3.9 Groups Management in JFrog**
- **Access Path**: Admin → Security → Groups
- **Groups Management Interface**:
  - **Title**: "Groups Management"
  - **Group Count**: Shows total number of groups (e.g., "1 Group")
  - **Search Functionality**: "Filter by Group Name" search bar
  - **Action Buttons**: "New" (highlighted with red rectangle), "Delete" (greyed out when no selection)

- **Groups Table Structure**:
  - **Group Name**: Name of the group (sortable column with upward arrow)
  - **Permissions**: Number of permissions and description (e.g., "1 | Anything")
  - **External**: External group management indicator
  - **Admin**: Administrative privileges indicator
  - **Auto Join**: Green checkmark for automatic user assignment

- **Default Groups**:
  - **readers**: Default group with "1 | Anything" permissions and Auto Join enabled

- **Group Management Features**:
  - **Create Groups**: "New" button for adding groups
  - **Delete Groups**: "Delete" button (active when group selected)
  - **Selection**: Radio button for selecting groups
  - **Sorting**: Group Name column is sortable

### **3.10 Creating and Managing Groups**
- **Group Creation Process**:
  - **New Group**: "newgroup" created with admin privileges
  - **Group Selection**: Radio button selection with red rectangle highlighting
  - **Admin Privileges**: Green checkmark in Admin column for "newgroup"
  - **Group Count**: Updated to "2 Groups" after creation

- **User-Group Association**:
  - **User "user1"**: Associated with multiple groups
  - **Related Groups**: "2 | readers newgroup" (user belongs to 2 groups)
  - **Group Assignment**: "newgroup" highlighted with green oval
  - **User Details**:
    - **Name**: "user1"
    - **Email**: "admin@admin.com"
    - **Realm**: "internal"
    - **Admin**: Green checkmark (admin privileges)
    - **Last Login**: "02-12-23 15:..."

### **3.11 User Authentication and Login**
- **Login Interface**:
  - **Title**: "Welcome to JFrog Artifactory!"
  - **Username Field**: Pre-filled with "user1" (red border)
  - **Password Field**: Masked characters (red border)
  - **Remember Me**: Checkbox option
  - **Log In Button**: Green button with red border highlighting
- **Authentication Process**:
  - **User Credentials**: Username and password validation
  - **Session Management**: Remember me functionality
  - **Access Control**: Based on user groups and permissions

### **3.12 JFrog Artifactory Dashboard After Login**
- **User Session Information**:
  - **Logged-in User**: "Welcome, user1" (highlighted with red rectangular border)
  - **Current Selection**: "Home" option selected in navigation
  - **Session Status**: Active user session with admin privileges

- **Dashboard Status**:
  - **Status Message**: "Artifactory is happily serving 0 artifacts"
  - **Version Information**: Artifactory Version 6.17.4
  - **Uptime**: "0d 0h 56m 33s"
  - **Latest Release**: Available version 7.33.9

- **Dashboard Sections**:
  - **Quick Search**: Package Search, Archive Search, Property Search, Checksum Search, JCenter Search
  - **Set Me Up**: Repository configuration with filter functionality
  - **Last Deployed Builds**: Shows timestamp and refresh capability
  - **Most Downloaded Artifacts**: Shows timestamp and refresh capability

- **Default Repositories Available**:
  - `libs-release`
  - `libs-snapshot`
  - `artifactory-build-info`
  - `libs-release-local`
  - `libs-snapshot-local`
  - `jcenter`

- **Resource Links**:
  - User Guide, Webinar Signup, Support Portal, Stack Overflow, Blog, Rest API

---

## **4. APACHE TOMCAT**

### **4.1 Tomcat Overview**
- **Overview**: Open-source web server and servlet container for Java applications.
- **Installation**:
  - Update packages and install Java.
  - Modify `tomcat-users.xml` to set username, password, and roles.
  - Change port from `8080` to `8082` for compatibility.

---

## **5. JENKINS CI/CD PIPELINE**

### **5.1 Jenkins Pipeline Overview**
- **Pipeline Type**: Declarative syntax used for simplicity and clarity.
- **Stages**:
  1. **Checkout**: Pulls code from Git repository.
  2. **Install Maven**: Downloads and extracts Maven binaries.
  3. **Compile**: Runs `mvn compile` to generate `.class` files.
  4. **Test**: Runs `mvn test` to execute test cases.
  5. **Package**: Runs `mvn package` to create `.war` file.
  6. **Upload Artifacts**:
     - Uses JFrog functions (`RT server`, `RT upload`).
     - Uploads `.war` file to `libs-snapshot-local`.
  7. **Download Artifacts**:
     - Uses `RT download` to retrieve `.war` file from the repository.
  8. **Tomcat Installation**: Installs and configures Apache Tomcat.
  9. **Application Deployment**:
     - Places `.war` file in `webapps` directory.
     - Restarts Tomcat server.

### **5.2 Master-Slave Setup**
- **Slave Configuration**:
  - Created a new node labeled `test-slave`.
  - Defined `/tmp` as the remote root directory for Jenkins files.
  - Connected slave to master using SSH credentials.

### **5.3 Pipeline Execution**
- **Process**:
  1. Open required ports (`8081`, `8082`).
  2. Execute pipeline stages sequentially.
  3. Verify successful artifact upload/download and application deployment.
- **Outcome**:
  - Artifacts uploaded to JFrog repository.
  - Application deployed to Tomcat server.
  - Address Book application successfully running.

---

## **6. PRACTICAL IMPLEMENTATION**

### **6.1 Practical Demo Highlights**
- **JFrog Repository**:
  - Artifacts uploaded to `libs-snapshot-local`.
  - Verified artifact availability and download functionality.
- **Tomcat Server**:
  - Application deployed and accessible via browser.
  - Verified functionality by adding and editing contacts.

### **6.2 Challenges and Solutions**
- **Issue**: JFrog configuration in Jenkins system tab failed.
- **Solution**: Used JFrog pipeline functions (`RT server`, `RT upload`, `RT download`) directly in the script.

---

## **7. CONCLUSION**

### **7.1 Summary**
- **Outcome**: Successfully integrated Jenkins with JFrog, built the application using Maven, and deployed it to Tomcat.
- **Recommendation**: Use declarative pipelines for simplicity and follow JFrog documentation for artifact management.

--- 

This concludes the notes based on the provided transcript. Let me know if you need further refinements!