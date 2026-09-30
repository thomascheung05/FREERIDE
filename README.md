# FREERIDE

FREERIDE is a virtual indoor cycling application that simulates real-world routes using Google Street View. It works by performing Optical Character Recognition (OCR) on another application's window (such as a trainer app or video) to read your distance and speed, then displays the corresponding point-of-view from a GPX route file.

The application features a Flask-based web interface that shows the rider's current position on a map and the corresponding Street View imagery, creating an immersive "free ride" experience.

## Features

*   **Virtual Route Simulation:** Ride any route you have a GPX file for, with visual feedback from Google Street View.
*   **Screen-Capture OCR:** Uses Tesseract OCR to read distance and speed from any application window on your screen. No special hardware integration is required.
*   **GPX File Processing:** Automatically processes `.gpx` files to create navigable routes, calculating cumulative distance and headings for image orientation.
*   **Configurable Presets:** Save and load different configurations for various training apps or screen layouts.
*   **Interactive Web UI:** A simple web interface displays the live Street View image and a Leaflet map tracking your progress along the route.

## How It Works

1.  **Route Processing:** A user-provided GPX file is processed into a GeoDataFrame, densified with additional points, and enriched with cumulative distance and heading information for each point. The result is saved as an efficient Parquet file.
2.  **Configuration:** The user defines a configuration preset by specifying the window title of the target application (e.g., a trainer software) and identifying the screen areas (bounding boxes) where distance and speed are displayed.
3.  **Data Capture & OCR:** When a ride is started, the application periodically captures screenshots of the specified bounding boxes from the target window. Tesseract OCR extracts the numerical distance value from these images.
4.  **Position Calculation:** The extracted distance is used to find the closest corresponding point along the pre-processed route.
5.  **Image Fetching & Display:** The application fetches a Google Street View image for the rider's current coordinates and heading. This image is sent to the web front end and displayed, creating the a virtual ride experience. The rider's position is also updated on a map in the UI.

## Setup

### Prerequisites

*   Python 3.x
*   A Google Street View API Key

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/thomascheung05/freeride.git
    cd freeride
    ```

2.  **Install Python dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

3.  **Add Google API Key:**
    Create a file named `googleapikey.txt` in the root directory of the project and paste your Google Street View API key into it.

## Usage

1.  **Add GPX Routes:**
    Place your `.gpx` route files in the `usersaves/routes/unprocessed/` directory.

2.  **Run the Application:**
    ```bash
    python app/main.py
    ```
    This will start the Flask web server.

3.  **Open the Web Interface:**
    Navigate to `http://127.0.0.1:5000` in your web browser.

4.  **Configure a Ride:**
    *   Click the **Config Settings** button to open the configuration modal.
    *   **Process a Route:** Select a file from the "Unprocessed Route" dropdown and click **Process Route**. The route will now be available for selection on the main screen.
    *   **Create a Capture Preset:**
        1.  In the "Screen Cap" section, enter the exact window title of the application you want to capture data from. You can use the **Show Windows** button to list all currently open windows.
        2.  In the "BBOX" section, click **Measure a bbox**. Follow the on-screen instructions: move your mouse to the top-left corner of the distance or speed number and wait 3 seconds, then do the same for the bottom-right corner. The coordinates will be captured and can be pasted into the input fields.
        3.  Enter a name for your preset in the "Config Preset" field and click **Save Preset**.

5.  **Start Your Ride:**
    *   From the main sidebar, select your saved **Config Preset** and the **Processed Route**.
    *   If you are resuming a ride, enter the distance you have already covered in the "Start Distance" field.
    *   Click **START**.
    *   The application will now display your position on the route. Use the **Swap Views** button to toggle between the map and Street View as the main display.

## Project Structure

```
├── app/
│   ├── main.py               # Flask application, API endpoints, and main logic.
│   └── data.py               # Data processing, OCR, and image fetching functions.
├── static/
│   ├── logic.js              # Frontend JavaScript for the web UI.
│   ├── style.css             # Stylesheet for the web UI.
│   └── web.html              # The main HTML file for the interface.
├── Tesseract/
│   └── ...                   # Bundled Tesseract OCR engine and data.
├── usersaves/
│   ├── config/               # Stores user-created configuration presets (.csv).
│   ├── rides/                # Logs of saved rides.
│   └── routes/
│       ├── processed/        # Stores processed route files (.parquet).
│       └── unprocessed/      # Location for raw user .gpx files.
├── requirements.txt          # Python dependencies.
└── googleapikey.txt          # (User-created) Stores the Google API key.
