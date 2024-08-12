# CI/CD Integration with Jenkins & GitHub Webhook

This guide will help you set up Jenkins with GitHub and configure a CI/CD pipeline effectively. By integrating Jenkins with GitHub, you can automate the build and testing processes, reducing manual intervention, saving time, and minimizing errors.

## Prerequisites

### Install Java Development Kit (JDK)
1. Download JDK 21 or JDK 17 from the [official Oracle website](https://www.oracle.com/java/technologies/javase-downloads.html).
2. Set the JDK installation path to the `JAVA_HOME` environment variable.

### Install Apache Maven
1. Download Apache Maven from the [official Maven website](https://maven.apache.org/download.cgi).
2. Set the Maven installation path to the `MAVEN_HOME` environment variable.

## Jenkins Installation and Setup

1. **Download Jenkins**
   - Download the `jenkins.war` file from the [official Jenkins website](https://www.jenkins.io/download/).

2. **Run Jenkins**
   - Open Command Prompt (cmd) in the directory where `jenkins.war` is saved.
   - Execute:
     ```sh
     java -jar jenkins.war --enable-future-java --httpPort=9090
     ```

3. **Initial Setup**
   - Open a web browser and navigate to `http://localhost:9090`.
   - Install suggested plugins when prompted.
   - After plugin installation, restart Jenkins by re-running the command from Step 2.

4. **Login and Configuration**
   - Log in using the initial password provided in the command line.
   - Set up an admin username and password.

5. **Configure Maven Plugin**
   - Navigate to **Manage Jenkins** -> **Manage Plugins**.
   - Search for and install the **Maven Integration** plugin.
   - After installation, restart Jenkins.

6. **Add Maven Tool**
   - Go to **Manage Jenkins** -> **Global Tool Configuration**.
   - Add Maven under **Maven Installations** by specifying the local path and version number.

## Creating a Jenkins Job

### For Local Machine

1. **Create New Job**
   - Click on **New Item**, enter a name, and select **Maven Project**.

2. **Configure Workspace**
   - Under **Advanced**, select **Use custom workspace** and specify the local directory.

3. **Build Steps**
   - Add **Invoke top-level Maven targets** under **Build Steps**.
   - Configure your build steps and save the job.

4. **Build the Project**
   - Click **Build Now** to run the job.

### For GitHub Repository

1. **Generate GitHub Token**
   - In GitHub, generate a personal access token with the required permissions.
   - Copy the token for use in Jenkins.

2. **Create New Job**
   - Click **New Item**, enter a name, and select **Maven Project**.

3. **Configure GitHub Repository**
   - Under **Source Code Management**, choose **Git** and provide the repository URL.
   - Under **Credentials**, select **Secret text** and paste the GitHub token.

4. **Set Branch Name**
   - Specify the branch name (e.g., `*/main`).

5. **Configure Build Triggers**
   - Select **GitHub hook trigger for GITScm polling** and save the job.

## GitHub Webhook Setup

Since webhooks cannot trigger Jenkins running on localhost, use [ngrok](https://ngrok.com/) for temporary public hosting.

### Using ngrok for Public URL

1. **Sign Up and Install ngrok**
   - Sign up at ngrok and download the application.
   - Authenticate ngrok with your account in the command line.

2. **Expose Jenkins**
   - Run:
     ```sh
     ngrok http http://localhost:9090
     ```
   - Copy the public URL provided by ngrok.

### Configure Jenkins Webhook URL

1. **Get Jenkins Webhook URL**
   - Go to **Manage Jenkins** -> **Configure System**.
   - Scroll to the **GitHub** section and click **Advanced**.
   - Copy the URL (e.g., `http://localhost:9090/github-webhook/`).

2. **Configure Jenkins Job**
   - Navigate to your job configuration.
   - Under **Build Triggers**, check **GitHub hook trigger for GITScm polling** and save the job.

### Set Up GitHub Webhook

1. **Configure Webhook**
   - Go to your GitHub repository settings.
   - Navigate to **Webhooks** and click **Add webhook**.
   - Paste the ngrok public URL and append `/github-webhook/` to the end.
   - Select **Just the push event**.
   - Disable SSL verification if necessary.

2. **Save the Webhook**
   - Click **Add webhook** to complete the setup.

## Parameterizing Jenkins Jobs

1. **Enable Parameterized Builds**
   - Go to the Jenkins job configuration.
   - Check **This project is parameterized**.

2. **Add Parameters**
   -  Add a **Choice Parameter** for options such as browser names (e.g., Chrome, Firefox).
   - Add another **Choice Parameter** for pom.xml profile IDs in your Selenium code. 

4. **Configure Build Commands**
-  Add goals according to what kind of code you are configuring to run such as - API, Selenium, Cucumber, Cypress etc.


   - Use the parameters in your Build Goals command to run Selenium tests:
     ```sh
     test -P"$Profile" -Dbrowser="$browserName"
     ```
   - Use the parameters in your Build Goals command to run Cucumber tests:
     ```sh
     test -Dcucmber.filter.tags="@OffersPage" -Dbrowser="$browserName"
     ```

6. **Save and Build**
   - Save the job configuration.
   - Run the job and select parameters as needed.

## Scheduling Builds in Jenkins

1. **Configure Schedule**
   - Go to the job configuration.
   - Under **Build Triggers**, select **Build periodically**.

2. **Set Schedule**
   - Use cron syntax to define the schedule, e.g., `* * * * *` for every minute.

### Breakdown of cron syntax:

- **Minute** (0-59)
- **Hour** (0-23)
- **Day of the month** (1-31)
- **Month** (1-12)
- **Day of the week** (0-7, where 0 and 7 are Sunday)

---

By following these steps, you will have a fully functional CI/CD pipeline integrated with Jenkins and GitHub, complete with webhook triggers, parameterized builds, and scheduling. This setup will help streamline your development process and automate testing and deployment.

For more details, refer to the [Jenkins documentation](https://www.jenkins.io/doc/) and [GitHub documentation](https://docs.github.com/en/github).
