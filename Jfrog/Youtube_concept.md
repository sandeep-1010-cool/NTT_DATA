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
- **Types of Repositories**:
  - **Local**: Stores artifacts (e.g., `.war`, `.zip`) private to the organization.
  - **Remote**: Proxy repository linked to external repositories, ensuring security and caching.
  - **Virtual**: Combines local and remote repositories into one URL for simplified access.
- **Advantages**:
  - Ensures tested and versioned builds.
  - Enhances security by avoiding direct internet downloads.
  - Saves bandwidth through caching.

### **3.2 JFrog OSS Artifactory Installation**
- **Steps**:
  1. Update packages and install Java (prerequisite).
  2. Download OSS binary and unzip the package.
  3. Start Artifactory using `artifactory.sh start`.
- **Configuration**:
  - Default username: `admin`.
  - Default password: `password`.
  - Create repositories (local, remote, virtual).

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