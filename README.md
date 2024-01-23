# RUSA ACP Brevet Control Time Calculator

## Overview
This web application is designed for calculating and storing control times for RUSA (Randonneurs USA) ACP (Audax Club Parisien) brevets. It replicates the functionality of the official RUSA ACP control time calculator, enabling users to input distances and receive the opening and closing times of controls for long-distance cycling events as per RUSA rules.

## Author Information
- **Name:** Deem Alowairdhi
- **QU ID:** 411214706
- **Email:** 411214706@qu.edu.sa

## Features
- **Control Time Calculation:** Facilitates input of distance in kilometers or miles and computes opening and closing times following ACP rules.
- **Data Storage:** Stores control data such as location, distance, and start time in a MongoDB database.
- **User Interface:** Offers a user-friendly interface for data input and displays calculated times.

## Project Structure
- `.gitignore`: Ignores untracked files like environment files or compiled Python files in Git.
- `acp_times.py`: Python module for calculating control times based on RUSA guidelines.
- `config.py`: Configurations for Flask and MongoDB connection.
- `docker-compose.yml`: Defines and runs multi-container Docker applications.
- `Dockerfile`: Contains instructions for building and running the Flask app in a container.
- `flask_brevets.py`: Main Flask application file with route definitions and logic.
- `requirements.txt`: Lists Python package dependencies.
- `run.sh`: Shell script for starting the application or setting up the environment.
- `static/`: Contains static files like JavaScript libraries, CSS, and images.
  - `js/`: JavaScript files for frontend functionalities.
- `templates/`: HTML templates rendered by Flask.
  - Custom HTML templates for various pages.

## Technology Stack
- **Frontend:** HTML, JavaScript, jQuery, Bootstrap.
- **Backend:** Python, Flask framework.
- **Database:** MongoDB.

## Frontend-Backend Communication
- Utilizes Ajax for asynchronous data exchange between the frontend and Flask backend.
- Flask processes control time calculation requests and interacts with MongoDB.


## Flask Application Routes

In the `flask_brevets.py` file, specific routes are defined to handle different functionalities of the RUSA ACP Brevet Control Time Calculator application. Two key routes are `@app.route("/submit_controls")` and `@app.route("/display_controls")`. 

### `@app.route("/submit_controls")`
This route is responsible for handling the submission of control times to the database. It is a critical part of the application, enabling users to store control time data.

#### Function: `submit_controls`
- **Method:** POST
- **Functionality:**
  - **Data Reception:** Receives control time data from the user via a POST request.
  - **Data Processing:** Extracts and processes this data, ensuring only controls with non-empty values are considered.
  - **Database Interaction:**
    - Initially clears existing controls in the database to prevent duplication.
    - Inserts the processed control times into the MongoDB database.
  - **Validation:** Filters and stores only valid controls based on criteria like non-empty 'km', 'open_time', and 'close_time' fields.
  - **Response:** Returns a JSON response indicating the success or failure of the operation.

#### Error Handling:
- If no valid control times are provided, it returns an error message prompting the user to enter at least one control time.

### `@app.route("/display_controls")`
This route is designed to fetch and display control times stored in the database. It plays a vital role in allowing users to view the control times they have entered.

#### Function: `display_controls`
- **Method:** GET
- **Functionality:**
  - **Database Query:** Retrieves all control time entries from the 'controls' collection in the MongoDB database.
  - **Data Presentation:** Renders these control times on a webpage using the `display_controls.html` template.


## MongoDB Schema
- **Collection Name:** `controls`
- **Document Structure:**
  ```json
  {
    "km": Number,
    "miles": Number,
    "location": String,
    "open_time": String,
    "close_time": String,
    "distance": Number,
    "begin_date": String,
    "begin_time": String
  }
  ```

## Building the Project
1. **Project Directory:**
   ```bash
   cd path/to/project-5-brevets-db/brevets
   ```
2. **Environment Variables:**
   Set up in `Dockerfile` and `docker-compose.yml`.
   - `docker-compose.yml`: Defines services like web and database with ports and volumes.
   - `Dockerfile`: Sets up the Python environment and installs dependencies.

3. **Build Docker Images:**
   ```bash
   docker-compose up --build
   ```

4. **Access the Application:**
   Open `http://localhost:5000` in a web browser.

5. **Manage the Application:**
   - Stop with `docker-compose down`.
   - View logs using `docker-compose logs`.

6. **Updates and Changes:**
   Rebuild Docker images and restart containers for any application changes.


## Troubleshooting: Docker Configuration Issue

### Problem Description
When building and running the Docker container for the RUSA ACP Brevet Control Time Calculator, an error stating "cannot open file brevets/python3" may occur. This issue is due to the conflicting use of `ENTRYPOINT` and `CMD` in the Dockerfile combined with the `command` directive in `docker-compose.yml`.

#### Understanding the Issue
- **Dockerfile Configuration:**
  - Contains `ENTRYPOINT ["python"]` and `CMD ["flask_brevets.py"]`, intending to run `python flask_brevets.py`.
- **docker-compose.yml Configuration:**
  - Includes `command: python3 flask_brevets.py`.
- **Conflict:**
  - Using both files together results in a command like `python python3 flask_brevets.py`, leading to the error.

### Resolving the Issue
Two options are available to resolve this conflict:

#### Option 1: Commenting Out `ENTRYPOINT` and `CMD`
- Modify the Dockerfile by commenting out the `ENTRYPOINT` and `CMD` lines.
- This allows the `command` from `docker-compose.yml` to execute correctly.
- The application starts as intended with the `python3 flask_brevets.py` command.

  ```Dockerfile
  # Dockerfile adjustments for compatibility
  ...
  WORKDIR /brevets

  # Commented out for docker-compose compatibility
  # ENTRYPOINT ["python"]
  # CMD ["flask_brevets.py"]
  ```

#### Option 2: Adjusting `CMD` in Dockerfile
- Change the Dockerfile to use only `CMD ["flask_brevets.py"]`, removing `ENTRYPOINT`.
- This lets the `command` in `docker-compose.yml` override the Dockerfile's `CMD`, ensuring the correct Flask application startup command.

  ```Dockerfile
  # Dockerfile adjustments for direct command
  ...
  WORKDIR /brevets

  CMD ["flask_brevets.py"]
  ```

### Implementation Note
Choose one of these options based on your project setup and requirements. Both solutions aim to streamline the startup command for the Flask application within the Docker environment. 


## Preview
[![Video Thumbnail](https://img.youtube.com/vi/5lbEPcKSTdY/maxresdefault.jpg)](https://youtu.be/5lbEPcKSTdY)


### Reference 
- [Docker Compose GitHub Issue #1616](https://github.com/docker/compose/issues/1616)