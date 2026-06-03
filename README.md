# MapAnimation: Geospatial Telemetry Visualization Engine

MapAnimation is a Java Swing-based desktop application designed to process, analyze, and animate chronological GPS tracking data. The engine parses geospatial telemetry points from flat-file structures, implements logical hardware stop-detection heuristics, and renders real-time animated vector paths on top of interactive OpenStreetMap layers.

The application leverages JMapViewer for mapping tiles, utilizing background worker threads to ensure responsive UI frames during intensive rendering calculations.

---

## Technical Highlights and Implementation Details

The implementation demonstrates several core software engineering and computational concepts:

### Non-Blocking Multithreading (SwingWorker API)
To prevent the user interface from freezing during animation loops, the rendering pipeline executes asynchronously using Java's `SwingWorker` API. The intensive iterative calculations, rendering delays (`Thread.sleep`), and data state changes run in an isolated background thread (`doInBackground`), safely updating the main event dispatch thread (EDT) without frame degradation.

### Geospatial Heading and Vector Calculations
The animation tracking element implements real-time dynamic marker orientation. Using a mathematical transformation pipeline (`Math.atan2` and `Math.toDegrees`), the application computes directional headings between sequential coordinates ($c_1$ and $c_2$). This scalar value is fed into an `AffineTransformOp` pipeline using nearest-neighbor interpolation to rotate directional arrow assets on the fly.

### Data Partitioning and Heuristic Analysis
The logic interfaces with a secondary processor layer (`TripPoint`) capable of handling conditional data subsets. By querying a custom stop-detection heuristic (`h2StopDetection`), the application allows users to dynamically toggle the context payload between an absolute comprehensive timeline (`getTrip`) and an abstracted dataset containing strictly moving states (`getMovingTrip`).

### Interpolated Timing and Frame Pacing
The runtime pacing engine dynamically divides user-allocated animation thresholds down to precise millisecond intervals (`(seconds * 1000) / trip.size()`). This creates a normalized frame delay across fluctuating dataset sizes, ensuring predictable playback timelines regardless of the path density or point frequency.

---

## System Architecture and Workflow

1. Data Source Layer: Parses localized CSV telemetry logs (`triplog.csv`) into structured object models containing latitude, longitude, and temporal attributes.
2. Interface Layer: Built on Java AWT and Swing frameworks using a structured `BorderLayout` design that integrates controls directly with an interactive `JMapViewer` viewport.
3. Rendering Layer: Leverages OpenStreetMap tile repositories (`OsmTileSource.TransportMap`) to download, cache, and draw custom path vectors and image-based map markers.

---

## Prerequisites and Local Setup

### System Requirements
* Java Development Kit (JDK) 8 or higher
* `JMapViewer` library jar added to your project's build path
* Assets: A source coordinate log named `triplog.csv` and an arrow icon named `arrow.png` present in the root directory

### Local Compilation and Execution

To compile and run this application from your terminal, execute the following commands within the project directory:

```bash
# 1. Compile the source files along with your JMapViewer dependency
javac -cp ".;JMapViewer.jar" Driver.java TripPoint.java

# 2. Run the compiled class file
java -cp ".;JMapViewer.jar" Driver
