### **Exercise 1: Creating a Freestyle Job to Build and Test the Flask Application**

In this exercise, you’ll create a **Freestyle Jenkins job** to clone a Flask-based application, install dependencies, run database migrations, and execute tests.

#### Objective:
- Clone a Flask-based project from GitHub.
- Install required dependencies.
- Set environment variables for Flask.
- Run database migrations.
- Run tests using `flask test`.

#### Steps:

1. **Clone the Repository**:
   - First, clone the project from GitHub. For this exercise, we are using the **Flask RealWorld Example App**.
     ```bash
     git clone https://github.com/gothinkster/flask-realworld-example-app.git
     cd flask-realworld-example-app
     ```

2. **Create a New Freestyle Job in Jenkins**:
   - From the Jenkins dashboard, click on **New Item**.
   - Name the job (e.g., `Flask-App-Build-Test`), select **Freestyle project**, and click **OK**.

3. **Configure Git Source**:
   - Under the **Source Code Management** section, select **Git**.
   - In the **Repository URL**, enter the URL of the Flask repository:
     ```bash
     https://github.com/gothinkster/flask-realworld-example-app.git
     ```

4. **Set Environment Variables**:
   - In the **Build Environment** section, click **Add** and select **Inject environment variables**.
   - Add the following environment variables:
     - `FLASK_APP = ${WORKSPACE}/autoapp.py`  // Jenkins workspace path
     - `FLASK_DEBUG=1`
     - `CONDUIT_SECRET=something-really-secret`
   
5. **Install Dependencies**:
   - Under the **Build** section, click **Add build step** and select **Execute shell**.
   - In the **Command** field, enter the following commands to install dependencies:
     ```bash
     pip install -r requirements/dev.txt
     ```

6. **Run Database Migrations**:
   - In the next **Execute shell** step, add the commands to set up the database:
     ```bash
     flask db init
     flask db migrate
     flask db upgrade
     ```

7. **Run Tests**:
   - Add one more **Execute shell** step to run the Flask tests:
     ```bash
     flask test
     ```

8. **Post-build Actions** (Optional):
   - Optionally, you can add a **Post-build action** to archive test results, email notifications, or other actions.

9. **Run the Job**:
   - Save the job and click **Build Now** to run the job.
   - Jenkins will clone the repository, install dependencies, set environment variables, run migrations, and execute tests.

10. **Check the Build Output**:
    - Once the build finishes, review the **Console Output** to check if the application built correctly, the database migrations were successful, and the tests passed.

#### Outcome:
- You’ve learned how to configure a **Freestyle Job** in Jenkins for a Flask application.
- You’re now familiar with setting environment variables, running shell commands, and running tests in a Jenkins job.

---

### **Exercise 2: Creating a Pipeline to Automate Build, Migrations, and Testing**

In this exercise, you'll convert the process from Exercise 1 into a **Jenkins Pipeline**. This will allow you to define the entire process in code (Jenkinsfile) and give you more control over the build flow.

#### Objective:
- Define a Jenkins **Pipeline** to automate cloning, environment setup, migrations, and running tests for a Flask app.

#### Steps:

1. **Prepare the Flask Project**:
   If you haven’t done so already, clone the **Flask RealWorld Example App** repository:
   ```bash
   git clone https://github.com/gothinkster/flask-realworld-example-app.git
   cd flask-realworld-example-app
   ```

2. **Create a Jenkinsfile**:
   In the root of the repository, create a `Jenkinsfile` that will define the pipeline stages. Here’s an example Jenkinsfile:
   
   ```groovy
   pipeline {
       agent any
       environment {
          `FLASK_APP = ${WORKSPACE}/autoapp.py`  // Jenkins workspace path
           FLASK_DEBUG = '1'
           CONDUIT_SECRET = 'something-really-secret'
       }
       stages {
           stage('Clone Repository') {
               steps {
                   // Clone the Flask app from GitHub
                   git 'https://github.com/gothinkster/flask-realworld-example-app.git'
               }
           }
           stage('Install Dependencies') {
               steps {
                   // Install the required dependencies
                   script {
                       sh 'pip install -r requirements/dev.txt'
                   }
               }
           }
           stage('Database Migrations') {
               steps {
                   // Run the Flask database migrations
                   script {
                       sh 'flask db init'
                       sh 'flask db migrate'
                       sh 'flask db upgrade'
                   }
               }
           }
           stage('Run Tests') {
               steps {
                   // Run the Flask tests
                   script {
                       sh 'flask test'
                   }
               }
           }
       }
       post {
           always {
               // Clean up or notify on success/failure
               echo 'Pipeline execution completed.'
           }
       }
   }
   ```

3. **Create a New Pipeline Job in Jenkins**:
   - From the Jenkins dashboard, click on **New Item**.
   - Name the job (e.g., `Flask-App-Pipeline`), select **Pipeline**, and click **OK**.

4. **Configure the Pipeline Job**:
   - Under the **Pipeline** section, select **Pipeline script from SCM**.
   - Choose **Git** as the SCM option, and in the **Repository URL** field, enter the URL for the Flask repository:
     ```bash
     https://github.com/gothinkster/flask-realworld-example-app.git
     ```
   - In the **Script Path** field, enter `Jenkinsfile` (or whatever you named your pipeline script).
   
5. **Run the Pipeline**:
   - Save the configuration and click **Build Now** to run the pipeline.
   - Jenkins will follow the steps defined in the `Jenkinsfile`, including cloning the repo, installing dependencies, running database migrations, and executing tests.

6. **Check the Build Output**:
   - Once the build completes, check the **Console Output** to see the pipeline steps and verify that everything worked correctly.

#### Outcome:
- You’ve learned how to create a **Jenkins Pipeline** that automates the build, migration, and testing of a Flask application.
- The pipeline script allows you to version-control the process and have more flexibility for future enhancements.

---

### Summary of the Exercises

- **Exercise 1 (Freestyle Job)**: You learned how to create a Jenkins Freestyle job to clone the Flask repository, set environment variables, install dependencies, run database migrations, and execute tests.
- **Exercise 2 (Pipeline)**: You learned how to define the entire build, migration, and test process in a Jenkins **Pipeline** using a `Jenkinsfile`, which offers more flexibility and scalability for automation.

These exercises will help you get comfortable with both **Freestyle Jobs** and **Pipelines** in Jenkins and familiarize you with automating testing and deployment processes for a Flask web application.

--- 

