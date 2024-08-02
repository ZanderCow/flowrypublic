## Usage

To get started with Flowry, You need to have access to the repo. I won't be giving out the source repo code for security reasons. However, I will make the `README.MD` file public to help show legitimacy on some of the techical details. If you are a recruiter coming from LinkedIn want acutal proof of the code, you can dm me on LinkedIn, And I'll give you a `Fine-grained personal access token` to the repo code. 





## Prerequisites

Before proceeding, please ensure that you have the following:

- [Dev Container Visual Studio Code Extension](https://code.visualstudio.com/docs/devcontainers/containers)
- [Docker](https://www.docker.com/)
- A computer with a minimum of 6-core GPU and 8GB of RAM (Docker consumes significant resources, especially when running multiple containers)

Please make sure you have a Docker account set up before continuing.





## Workflow

This section gives you a overview of my workflow for developing the app. 

### 1. Feature Development

- Conceptualize a feature.
- Define the details of the feature (its function, capabilities, etc.).
- Consider edge cases (ex: , for a sign-in feature, consider scenarios like incorrect password, invalid email format, etc.)
- Write up a GitHub Project backlog ticket to remind yourself, what you're actually doing.

### 2. Backend Development

- Identify the necessary code to implement (REST API code, database query code, authentication, etc.).
- Determine if your feature requires creating new tables or adding columns to an existing table. If so, update the schema.sql file accordingly.
- Write functions for the feature that will make SQL calls to the database. Place these functions in the respective .js files in the /db folder. If there isn’t a suitable file because you created a new table, you can create a new one.
- Write unit tests for the SQL calls using Jest. For simplicity, we will use the mock PostgreSQL database created by Docker Compose during setup instead of mocking the database calls. The downside is that the connection pools must open and close for each test. If you use an object, such as a user named John Doe, for each unit test, you must clear that object from the database after each test run. This ensures that running npm test multiple times won’t result in test failures due to leftover data.
- Write the routes for the feature in the /routes folder that call the respective function in the .js file from the /db folder. Handle errors and edge cases in these routes.
- Write integration tests for the routes. Mock the functions that make calls to the database (e.g., createNewUser). This allows you to focus on ensuring that errors are correctly thrown for various inputs, as the database interactions are already tested by unit tests.
- Run the tests and ensure they all work.

### 3. Frontend Development

- Refer to the UI mockup in Figma for the app and design your new feature. Ensure it follows the same color scheme as the rest of the app and scales appropriately on various devices (follow the setup in other parts of the app).
- Convert the UI mockup components into `React/Next.js` and `CSS` code.
- If you are creating new components, write unit tests for them. Ensure they render correctly on the page and that their features work properly.
- Write integration tests for your new features. Mock the connection to the backend if your feature involves backend interactions, and use placeholder data for testing. Currently, you don't need to worry about edge cases involving form submissions, as the backend handles those. However, this document may be updated in the future to include handling excessive error cases on the frontend to avoid high API call costs.
- Run your tests to ensure they work correctly.

### 4. End-to-End (e2e) Testing

- Write tests that simulate actual user interactions with the feature using Python and Selenium. Use the `./e2e.sh` script instead of `./dev.sh`. Ensure that your unit and integration tests from earlier all work, as the setup process will run them beforehand and then start the application.
- To simplify debugging, im using a special version of Selenium Chromium that includes a VNC. This allows you to view what Selenium is doing on the screen, making it easier to identify and debug errors.
- Run your e2e tests and ensure they cover important edge cases and scenarios. Verify that all tests pass.

### 5. Run `docker-compose.test.yml`

- run the command 
 ```bash
   docker-compose -f docker-compose.test.yml up --build -d && docker-compose -f docker-compose.test.yml logs -f
 ```
 Ensure that no errors happen with your tests. They shouldn't if you make sure you're in the e2e, unit, and integration tests all work.

### 6. Push Code to Repo

- Push your code to the repository.
- Currently, I am pushing directly to the main branch as this is a solo project. If this becomes a collaborative effort in the future, I will use branches and pull requests (PRs).
- Whenever code is pushed to the repository, GitHub will run a workflow action to ensure the code works as intended. This action essentially runs the ```docker-compose.test.yml``` following command on a GitHub-hosted server:

### 7. Make a Version Tag and deploy

- Once you have enough features that satisfy a quality update, make a new version tag.
- When it's made, GitHub Actions will automatically run the `docker-compose.test.yml` to ensure everything still works, then it will use a Terraform script to set up production-ready code and deploy it.
- I am considering setting up a staging environment, that replicates the production-ready one.


## Automated Developer Environment Setup

The set up process for this app is vastly simplified thanks to a Visual studio code extension called Dev Containers. It lets you use a docker containers as VSCode development Environment. This Gets rid of needing to worry about pain in the ass issues such as package manager Version Compatibility, and other stuff. There is a catch though: setting up Git to work inside of the windows is extremely cumbersome, So you need to run get from the main Flowry window.

## Dev Setup Instructions

This set up process lets you get started on working on the app. 

1. Run the following commands to set up the project:
   ```bash
   chmod +x dev.sh
   ./dev.sh
   ```
   This will create Docker images for the frontend and backend and build their containers.

2. Your computer will open up two new Visual Studio Code tabs, one called `frontend` and one called `backend`. These are your code environments for the respective parts of the app. Follow the instructions in [Frontend Setup Instructions](#frontend-setup-instructions) and [Backend Setup Instructions](#backend-setup-instructions) to complete the setup.

You are now ready to start developing within the Dev Container environment!





## End to End Dev Setup Instructions

Once you have written unit integration tests for whatever feature you wanted to put in the app on both the backend and frontend and ensured that the tests have passed (see Workflow.md for more info), you can now start writing the end to end tests.

1. Run the following commands to set up the project:
   ```bash
   chmod +x e2e.sh
   ./e2e.sh
   ```
The setup will be quite similar to the developer environment, with a key difference: only one VSCode window will open, dedicated to end-to-end testing. Unlike the developer setup, the frontend and backend are not running in dev containers but are instead running with `npm run dev` and `npm start`, respectively. Additionally, the environment variables have been adjusted, particularly those directing the frontend to the backend. This adjustment is necessary because Docker Compose handles hostnames for containers internally, making the use of `localhost` unsuitable.

2. Follow the instructions in [Frontend Setup Instructions](#frontend-setup-instructions) and [Backend Setup Instructions](#backend-setup-instructions) to complete the setup for the end-to-end testing environment.

You are now ready to start developing within the Dev Container environment!





## End To End Setup

1. **Install the Dev Containers Extension**
   - Make sure you have the VSCode extension "Dev Containers" installed. You can find it in the VSCode marketplace.

2. **Open the Command Palette**
   - Press `Command + Shift + P` on macOS or `Ctrl + Shift + P` on Windows/Linux.

3. **Reopen in Container**
   - Type in `Reopen in Container` and select `Dev Containers: Attach to Running Container`.

4. **Attach to Container**
   - Click on the option that appears.
   - The VSCode window will restart and will be running inside the container.

5. **Automatic Setup**
   - VSCode will automatically install the correct dependencies and configure the environment for you.

6. **Start Backend and Frontend Containers**
   - Make sure the backend container is running `npm start` and the frontend container is running `npm run dev`.

### Running the tests

- Make sure you run `npm run dev` in the frontend and `npm start` in the backend. Both containers need to be running so Selenium can run the tests.
- Run `pytest`.

### Viewing Selenium Actions

- If you want to view what Selenium is doing when `pytest` is run, you can go to `http://localhost:7900/`, click the connect button, and enter `secret` as the password.

### Possible Issues with Selenium Tests

- There is a possibility that the Selenium tests will sometimes break. This is because the container can run out of resources since it is heavy on your computer. Restart the `selenium-chromium` container in Docker Desktop to resolve this issue.

### Important Note

- **Using Git**
  - Use Git in the original window where you opened VSCode (referred to as the "host" window) and not in the container instance window. This is because the IDE running in the container does not have access to your Git credentials.





## Frontend Setup Instructions

1. **Install the Dev Containers Extension**
   - Ensure you have the "Dev Containers" extension installed from the VSCode marketplace.

2. **Open the Command Palette**
   - On macOS, press `Command + Shift + P`.
   - On Windows/Linux, press `Ctrl + Shift + P`.

3. **Reopen in Container**
   - Type `Reopen in Container` and select `Dev Containers: Attach to Running Container`.

4. **Attach to Container**
   - Click on the option that appears.
   - VSCode will restart and run inside the container.

5. **Automatic Setup**
   - VSCode will automatically install the necessary dependencies and configure the environment.

6. **Verify Setup**
   - Ensure your terminal output looks like the following:
     ```shell
     Whats next?
     Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug 03fbd0d1caa32d676c4b225172a05e554c8d57ffa22d6c6ff1b90c1436fae23c
     Learn more at https://docs.docker.com/go/debug-cli/
     Done. Press any key to close the terminal.
     ```
   - Press `Enter`, and your terminal should look like this:
     ```shell
     root@03fbd0d1caa3:/app#
     ```

7. **Run the Frontend**
   - Once the setup is complete, run `npm run dev` in the terminal.
   - The frontend should now be running on `http://localhost:3000`.

### Important Note

- **Using Git**
  - Use Git in the original VSCode window (referred to as the "host" window) and not in the container instance window. The IDE running in the container does not have access to your Git credentials.





## Backend Setup Instructions


1. **Install the Dev Containers Extension**
   - Ensure you have the "Dev Containers" extension installed from the VSCode marketplace.

2. **Open the Command Palette**
   - On macOS, press `Command + Shift + P`.
   - On Windows/Linux, press `Ctrl + Shift + P`.

3. **Reopen in Container**
   - Type `Reopen in Container` and select `Dev Containers: Attach to Running Container`.

4. **Attach to Container**
   - Click on the option that appears.
   - VSCode will restart and run inside the container.

5. **Automatic Setup**
   - VSCode will automatically install the necessary dependencies and configure the environment.

6. **Verify Setup**
   - Ensure your terminal output looks like the following:
     ```shell
     Whats next?
     Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug 03fbd0d1caa32d676c4b225172a05e554c8d57ffa22d6c6ff1b90c1436fae23c
     Learn more at https://docs.docker.com/go/debug-cli/
     Done. Press any key to close the terminal.
     ```
   - Press `Enter`, and your terminal should look like this:
     ```shell
     root@03fbd0d1caa3:/app#
     ```

7. **Run the Backend**
   - Once the setup is complete, run `npm start` in the terminal.
   - The frontend should now be running on `http://localhost:4000`.

### Important Note

- **Using Git**
  - Use Git in the original VSCode window (referred to as the "host" window) and not in the container instance window. The IDE running in the container does not have access to your Git credentials.


