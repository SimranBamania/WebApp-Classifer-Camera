
# Real-Time Image Predictor

The Real-Time Image Predictor is a web application that uses a pre-trained machine learning model to predict the content of images captured through a webcam. The app displays the prediction results in real-time.

## Features
  - Real-time video feed from your webcam.
  - Image prediction using TensorFlow.js.
  - User-friendly interface with start/stop prediction functionality.
  - Smooth animations and responsive design.

## Code Explanation

#### HTML
  The HTML structure includes a container for the video feed and buttons for controlling the prediction. It also links to external resources like Google Fonts and TensorFlow.js.


#### CSS
The CSS styles the application with a gradient background, centered content, and hover effects for interactive elements.

#### JavaScript
The JavaScript code handles:

- Accessing the webcam and displaying the video feed.
- Loading the TensorFlow model.
- Making predictions based on the video frames.
- Displaying the prediction results in real-time.

### Key Functions
  - setupCamera(): Initializes the webcam.
  - loadModel(): Loads the pre-trained TensorFlow model.
  - predict(): Performs image prediction on the video frames.
  - togglePrediction(): Toggles the prediction process on and off.
  - main(): Main function to start the application.


Here's an explanation of the key functions in your JavaScript code for the Real-Time Image Predictor application:


###  1. setupCamera()

This function initializes the webcam and displays the video feed.

```python
async function setupCamera() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    video.srcObject = stream;
    return new Promise(resolve => {
      video.onloadedmetadata = () => resolve(video);
    });
  } catch (error) {
    console.error('Error accessing camera: ', error);
    alert('Error accessing camera. Please ensure you have granted permission.');
  }
}
```
  - **Purpose:** To access the user's webcam and stream the video feed to the video element.
  - **Steps:**
    
        1. Request Camera Access: navigator.mediaDevices.getUserMedia({ video: true }) prompts the user for permission to use their camera.

        2. Stream Video: If permission is granted, the video stream is set as the source of the video element (video.srcObject = stream).

        3. Load Metadata: The video element waits for metadata to be loaded before resolving the promise, ensuring the video feed is ready for use.

### 2. loadModel()
This function loads the pre-trained TensorFlow.js model.

```python
async function loadModel() {
  try {
    model = await tf.loadGraphModel('./model.json');
  } catch (error) {
    console.error('Error loading model: ', error);
    alert('Error loading model.');
  }
}
```
  - Purpose: To load the machine learning model that will be used for image prediction.
  - Steps:

          1. Load Model: tf.loadGraphModel('./model.json') loads the model from the specified path.

          2. Error Handling: If loading the model fails, an error message is displayed in the console and an alert is shown to the user.


### 3. predict()
This function performs image prediction on the video frames and displays the result.

```python
async function predict() {
  if (predicting) {
    let example = tf.browser.fromPixels(video).cast('float32');
    example = tf.image.resizeBilinear(example, [224, 224]); // resize to model's expected input size
    example = example.reshape([1, 224, 224, 3]);

    let prediction = await model.predict(example);
    let class_scores = await prediction.data();
    let max_score_id = class_scores.indexOf(Math.max(...class_scores));
    let classes = ["Pattern", "Solid"];

    result.innerHTML = classes[max_score_id].toString();

    requestAnimationFrame(predict);
  }
}
```
  - Purpose: To capture video frames, make predictions, and update the displayed result.
  - Steps:

        1. Capture Frame: tf.browser.fromPixels(video) captures the current frame from the video element.
    
        2. Preprocess Frame: The frame is cast to float32, resized to the expected input size (224x224), and reshaped to match the model's input dimensions.
    
        3. Make Prediction: model.predict(example) runs the model on the preprocessed frame to get the prediction.
    
        4. Process Prediction: The prediction data is extracted, and the class with the highest score is identified.
    
        5. Display Result: The predicted class is displayed in the result element.
    
        6. Repeat Prediction: requestAnimationFrame(predict) schedules the next prediction frame if predicting is still true.
    
### 4. togglePrediction()
This function toggles the prediction process on and off.
```python
function togglePrediction() {
  predicting = !predicting;
  if (predicting) {
    toggleButton.textContent = "Stop Prediction";
    predict();
  } else {
    toggleButton.textContent = "Start Prediction";
  }
}
```
  - Purpose: To start or stop the prediction process based on the current state.
  - Steps:

        1. Toggle State: The predicting variable is toggled between true and false.
    
        2. Update Button Text: The button text is updated to indicate the current state ("Start Prediction" or "Stop Prediction").
    
        3. Start Prediction: If predicting is true, the predict() function is called to begin the prediction loop.
### 5. main()
This is the main function that initializes the application.

```python
async function main() {
  await setupCamera();
  await loadModel();
  toggleButton.addEventListener('click', togglePrediction);
  togglePrediction();
}
```
  - Purpose: To set up the camera, load the model, and initialize event listeners.
  - Steps:

        1. Setup Camera: Calls setupCamera() to initialize the video feed.
    
        2. Load Model: Calls loadModel() to load the machine learning model.
    
        3. Event Listener: Adds an event listener to the togglePredictionButton to handle clicks.
    
        4. Start Prediction: Calls togglePrediction() to start the prediction process immediately upon loading.
    
