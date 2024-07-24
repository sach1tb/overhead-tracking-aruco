# CVTracking v3.07
Python scripts for tracking fiducials via overhead mounted cameras using the OpenCV API

## References: 
A lot of the code written has been pulled from or inspired by various amazing resources from great content creators. 
A few shout outs include:
Adrian Rosebrock from https://www.pyimagesearch.com.
OpenCV Team and Contributors for tutorials, demonstrations, and source code.
QR codes can be generated from here https://github.com/okalachev/arucogen 
More on ArUco markers https://docs.opencv.org/3.2.0/d5/dae/tutorial_aruco_detection.html
...
    
## How to use:
0. Use operate_cameras.py to check that all cameras are connected and streaming to the computer
1. Typically, you must first calibrate the cameras before you start the tracker. The cameras must be calibrated to obtain both intrinsic (e.g. focal length) and extrinsic parameters (e.g. posiion and orientation with respect to a global origin). 
  a. The intrinsic calibration (camera.py set_calib) consists of a 3x3 camera matrix (read as is from the text file), and 5 distortion coefficients (read as a single line from the text file). See https://docs.opencv.org/3.1.0/d9/d0c/group__calib3d.html on how they are used to calculate the position of a 3D point on the camera image.
  a. The extrinsic calibration (camera.py set_calib) consists of a 4x4 SE(3) element consisting of the position and orientation of the camera with respect to a global frame. These are obtained using the get_relations.py script which in turn calls the get_global_relations function from the tracker class
2. To simply run the tracker for an experiment where you don't need information for feedback, use project_tracker.py
3. If you need information for feedback, then use LT_server.py (live tracker)

## Function definitions
Camera.py:

  Two classes are introduced which help to initialize a usb webcamera and to initialize a webcamera stream. 
  
  Class: camObjThreaded:
    A class which takes in the video source (a number from 0 - n) which represents the dev/video# used to initialize an OpenCV capture device.
    The other intialization variable is the width. This defines the width of the stream. This is to allow for smaller resolutions which will increase processing speeds.

    def __init__(): takes in the values mentioned above and then initializes the camera with a user-guided prompt. This checks to make sure the camera is calibrated for intended operations
      (CALIBRATION IS NOT AN OPTION): This design choice was made because all aruco tracking needs the mtx and dist provided from calibration. 
        In the works: This step will be expidited to prompt the user to calibrate if it doesn't find the necessary calibration files.
      Once the camera is initialized, the stream begins in a thread which handles all i/o, and undistortion of the frames being recieved by the camera.

      It is necessary that the folder calibCache is set up as follows:
      This is to provide a storage point for the mtx and dists from calibration for future use (I.E. not needing to calibrate every time)
      badImg and goodImg are directories which contain the good and bad calibration images.

      \ProjectFolder
      ---\calibCache
      ------\cam"ID"
      ---------\badImg
      ---------\goodImg
      ---------\rotationMatrices
      ---------\translationMatrices
      ---------\resolution_mtx.txt
      ---------\resolution_dist.txt
      ---\trialCache

    def calibrate(): runs through an automated process of calibrating which takes images until 20 good images are captured based on the checkerboard
      calibration.

    def showCalibFeed() outputs the thread stream. When closed, the FPS average will be outputted.

    def start(), update() called within the class and shouldnt be called by the user

    def read() returns the undistorted frame from the thread

    def stop() ends the thread and stops the cv.VideoCapture() stream. Class object is rendered useless at this point and should be deleted.

  class FPS:
    Tracks the fps of the stream and can also be used to track the average number of loop iterations per second.
    
  class WebcamVideoStream:
    The base threading class that was used to help implement camObjThreaded. Thanks to pyimagesearch.com for the amazing article on threading:
      source URL: https://www.pyimagesearch.com/2015/12/21/increasing-webcam-fps-with-python-and-opencv/
  
  class camObj = previous iteration of the camera object. This is implemented without the threading and as such was slow and cumbersome in application.
  
Tracker.py:
  README in progress
  
  
