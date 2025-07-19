### **Notes: How to Integrate Jenkins with JFrog | Jenkins JFrog Maven Tomcat CI/CD Pipeline | Upload Artifacts**

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

### **Key Concepts**

#### **Build Tools**
- **Definition**: Automates the process of converting source code into executable applications.
- **Activities**: Includes compiling, linking, packaging, and converting code to executable form.
- **Examples**: Tools vary based on programming language. For Java, Apache Maven is used.

#### **Apache Maven**
- **Purpose**: A build tool for Java-based projects.
- **Key Features**:
  - Based on **Project Object Model (POM)**.
  - Resolves dependencies defined in `pom.xml`.
  - Downloads dependencies from the internet if not available locally.
  - Commands used:
    - `mvn compile`: Generates compiled code in `Target/classes`.
    - `mvn test`: Creates test reports in `Target/surefire-reports`.
    - `mvn package`: Creates an executable `.war` file in `Target`.

---

### **JFrog Artifactory**
- **Purpose**: Repository for storing and managing build artifacts.
- **Types of Repositories**:
  - **Local**: Stores artifacts (e.g., `.war`, `.zip`) private to the organization.
  - **Remote**: Proxy repository linked to external repositories, ensuring security and caching.
  - **Virtual**: Combines local and remote repositories into one URL for simplified access.
- **Advantages**:
  - Ensures tested and versioned builds.
  - Enhances security by avoiding direct internet downloads.
  - Saves bandwidth through caching.

---

### **JFrog OSS Artifactory Installation**
- **Steps**:
  1. Update packages and install Java (prerequisite).
  2. Download OSS binary and unzip the package.
  3. Start Artifactory using `artifactory.sh start`.
- **Configuration**:
  - Default username: `admin`.
  - Default password: `password`.
  - Create repositories (local, remote, virtual).

---

### **Apache Tomcat**
- **Overview**: Open-source web server and servlet container for Java applications.
- **Installation**:
  - Update packages and install Java.
  - Modify `tomcat-users.xml` to set username, password, and roles.
  - Change port from `8080` to `8082` for compatibility.

---

### **Jenkins Pipeline**
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

---

### **Master-Slave Setup**
- **Slave Configuration**:
  - Created a new node labeled `test-slave`.
  - Defined `/tmp` as the remote root directory for Jenkins files.
  - Connected slave to master using SSH credentials.

---

### **Pipeline Execution**
- **Process**:
  1. Open required ports (`8081`, `8082`).
  2. Execute pipeline stages sequentially.
  3. Verify successful artifact upload/download and application deployment.
- **Outcome**:
  - Artifacts uploaded to JFrog repository.
  - Application deployed to Tomcat server.
  - Address Book application successfully running.

---

### **Practical Demo Highlights**
- **JFrog Repository**:
  - Artifacts uploaded to `libs-snapshot-local`.
  - Verified artifact availability and download functionality.
- **Tomcat Server**:
  - Application deployed and accessible via browser.
  - Verified functionality by adding and editing contacts.

---

### **Challenges and Solutions**
- **Issue**: JFrog configuration in Jenkins system tab failed.
- **Solution**: Used JFrog pipeline functions (`RT server`, `RT upload`, `RT download`) directly in the script.

---

### **Conclusion**
- **Outcome**: Successfully integrated Jenkins with JFrog, built the application using Maven, and deployed it to Tomcat.
- **Recommendation**: Use declarative pipelines for simplicity and follow JFrog documentation for artifact management.

--- 

This concludes the notes based on the provided transcript. Let me know if you need further refinements!