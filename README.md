# Volume-Control-through-Hand-Gestures
PROJECT REPORT
On
Volume Control through
Hand Gesture


1.	ABSTRACT
This project uses Mediapipe and OpenCV libraries to track hand landmarks in a live video stream from the user's webcam. The distance between two specific points on the user's hand (the tips of the thumb and index finger) is then used to adjust the system's volume, with the volume level being set to a value between the minimum and maximum values allowed by the system's audio output device. The code also displays the video stream with the hand landmarks and the line between the thumb and index finger overlaid on the video. The project demonstrates the capabilities of computer vision techniques in controlling audio output and offers potential for applications in accessibility and user interface design.

2.	INTRODUCTION
In this study, we investigate the use of computer vision techniques to control a system's audio output via hand gestures. To track hand landmarks in a live video feed from the user's webcam, we utilise the Mediapipe and OpenCV packages. The distance between two specified places on the user's hand (the tips of the thumb and index finger) is then utilised to modify the system's volume, with the volume level adjusted to a value between the system's audio output device's minimum and maximum settings.
This technology has applications in accessibility and user interface design since it allows users to regulate audio output using intuitive and natural hand motions. Furthermore, the study highlights the possibilities of computer vision techniques in enabling fresh and innovative user-technology interactions. We intend to improve the user experience and make technology more accessible to a larger variety of consumers by offering a more direct and straightforward manner for users to manage audio output.

3.	DESCRIPTION OF THE IMPORTED MODULES
Brief description of the imported modules:
1.	OpenCV: This is a widely-used computer vision library that provides a range of functions for processing images and videos, detecting objects, and tracking movement. In this project, it is used to capture video from the user's camera and process frames to detect the user's hand and track its movements.
2.	Mediapipe: This is a cross-platform framework that offers a set of customizable building blocks for building applied machine learning pipelines. It includes pre-built modules for hand detection, landmark tracking, and gesture recognition, which are used in this project for tracking the user's hand movements.
3.	Math: This is a built-in Python module that provides a set of mathematical operations and functions for numerical processing. In this project, it is used to calculate the distance between two points, which is used to track the distance between the user's fingers.
4.	ctypes and comtypes: These are two Python modules that provide access to C libraries and Windows COM and OLE automation libraries, respectively. In this project, they are used to access the Windows Core Audio API and control the system's audio output devices.
5.	Pycaw: This is a Python module specifically designed for working with the Windows Core Audio API, allowing Python code to control the system's audio output devices. In this project, it provides a high-level interface to the Windows Core Audio API for controlling the volume of the audio output devices.
6.	NumPy: This is a popular Python library for numerical processing, providing support for arrays, matrices, and other mathematical operations. In this project, it is used for interpolating the volume level based on the distance between the two fingers being tracked.
4.	EXPLANATION OF THE CODE

1.	Importing necessary libraries:
import cv2
import mediapipe as mp
from math import hypot
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume
import numpy as np

In this section, we import the project's libraries. OpenCV, which is used for video processing, and Mediapipe, which provides pre-built building pieces for applications like hand tracking, are two examples. We also import other libraries to manage the system's audio output and perform mathematical functions.

2.	Initializing variables and objects:
cap = cv2.VideoCapture(0)
mpHands = mp.solutions.hands
hands = mpHands.Hands()
mpDraw = mp.solutions.drawing_utils
devices = AudioUtilities.GetSpeakers()
interface = devices.Activate( IAudioEndpointVolume._iid_, CLSCTX_ALL, None)
volume = cast(interface, POINTER(IAudioEndpointVolume))
volMin, volMax = volume.GetVolumeRange()[:2]

In this section, we initialise variables and objects that will be utilised throughout the project. Using OpenCV, we establish a video capture object, as well as objects for hand tracking and sketching landmarks on the hand. We also specify variables to govern the audio output, such as acquiring the audio devices, enabling the audio endpoint, and adjusting the minimum and maximum volume levels.
3.	Processing the video stream:
while True:
success, img = cap.read()
imgRGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
results = hands.process(imgRGB)

Here, we begin a loop that will process the video feed from the user's webcam indefinitely. We read each frame of the video stream with the video capture object and transform the colour space from BGR to RGB. The hands object is then used to analyse the RGB picture and detect the landmarks on the user's hand.
4.	Drawing landmarks and detecting hand gestures:

lmList = []
if results.multi_hand_landmarks:
for handlandmark in results.multi_hand_landmarks:
for id, lm in enumerate(handlandmark.landmark):
h, w, _ = img.shape
cx, cy = int(lm.x * w), int(lm.y * h)
lmList.append([id, cx, cy])
mpDraw.draw_landmarks(img, handlandmark, mpHands.HAND_CONNECTIONS)

In this case, we use hand tracking findings to recognise and draw landmarks on the user's hand. We loop through each identified hand and landmark on the hand, appending the landmark ID and coordinates to a list. The landmarks are then drawn on the picture using the mpDraw object.

5.	Controlling audio volume based on hand gestures:

if lmList != []:
x1, y1 = lmList[4][1], lmList[4][2]
x2, y2 = lmList[8][1], lmList[8][2]

cv2.circle(img, (x1, y1), 4, (255, 0, 0), cv2.FILLED)
cv2.circle(img, (x2, y2), 4, (255, 0, 0), cv2.FILLED)
cv2.line(img, (x1, y1), (x2, y2), (255, 0, 0), 3)

length = hypot(x2 - x1, y2 - y1)

vol = np.interp(length, [15, 220], [volMin, volMax])

This part estimates the distance between two spots on the hand and then changes the computer's volume depending on that distance. The length of the hand is computed with the math module's hypot() function and then transferred to a volume value with the numpy interp() method. The volume value is subsequently sent to the Windows Audio API for volume adjustment.

6.	Displaying the image:
print(vol, length)
volume.SetMasterVolumeLevel(vol, None)
# Hand range 15 - 220
# Volume range -63.5 - 0.0
cv2.imshow('Image', img)
if cv2.waitKey(1) & 0xff == ord('q'):
break

The picture with the hand landmarks and the line connecting the two spots on the hand is shown in this part. When the user taps the 'q' key, the programme ends and the picture is shown using the OpenCV imshow() function.

7.	DEMO OF WORKING
When you run the code, it will open a window that shows the live video stream from your webcam. As you move your hand in front of the webcam, the code will detect and track the positions of different hand landmarks in real-time. These landmarks are represented by small dots and connected by lines, showing the various parts of your hand that are being tracked.

 
Figure 1
To use your hand gestures to control the audio volume, you need to position your hand in front of the webcam such that your index and middle fingers are visible and separated from each other. As you move your fingers closer together or further apart, the code will calculate the distance between them and map that distance to the system's audio volume range, adjusting the volume accordingly.
       
Figure 2

You can see the current volume level printed on the console as you adjust your hand gesture. The volume changes in real-time and you can use this method to control the system's audio volume without having to use a keyboard or mouse.
 
Figure 3

8.	LIMITATION 
This code may have the following limitations:
1)	Because the code utilizes the Pycaw library to access the Windows Audio API, it may not work with other operating systems.
2)	Because the code only detects one hand at a time, it may not be appropriate for applications that need tracking of numerous hands.
3)	Dependence on excellent lighting circumstances: Because the code depends on recognizing landmarks on the hand, poor lighting conditions may impair hand detection accuracy.
4)	Only the motion of extending the index and middle fingers to adjust the volume is recognized by the code. Other hand movements may conflict with the estimation of hand length and result in inaccurate volume adjustments.
5)	Limited volume control range: Because the volume range is mapped from a specific range of hand lengths, the range of levels that may be controlled using hand gestures may be limited.
6)	The code requires the user to learn a specific hand gesture to adjust the volume. If the user is not familiar with this gesture or has difficulty performing it, the code may not be effective for controlling the volume.
7)	The code is designed to detect the hand and track its movement based on certain assumptions about the shape and size of a typical hand. If the user's hand deviates significantly from these assumptions (e.g., due to disability or injury), the code may not function as intended.


9.	FUTURE PLANS
1.	Improving accuracy: Future updates could focus on improving the accuracy of hand detection and gesture recognition, for example, by using more advanced computer vision techniques or machine learning models.
2.	Supporting multiple hands: A future version of the code could be updated to support tracking multiple hands simultaneously.
3.	Adding support for more gestures: In addition to adjusting volume, the code could be updated to support other hand gestures for controlling different aspects of the computer or other devices.
4.	User interface improvements: The code currently displays the camera output with landmarks overlaid, but a more user-friendly interface could be developed to make it easier for users to interact with the system.
5.	Portability: As mentioned earlier, the code currently only works on Windows operating systems. Future plans could include porting the code to other operating systems or developing a cross-platform version.
6.	Accessibility features: The code could be updated to include accessibility features for individuals with disabilities, such as voice control or alternative input methods.
7.	Integration with other software: The code could be integrated with other software programs, such as media players or presentation software, to provide seamless control of different aspects of the computer or other devices.

REFERENCES 
•	OpenCV. (n.d.). OpenCV. Retrieved from https://opencv.org/
•	Mediapipe. (n.d.). Mediapipe. Retrieved from https://mediapipe.dev/
•	Python Software Foundation. (n.d.). math - Mathematical functions. Python Documentation. Retrieved from https://docs.python.org/3/library/math.html
•	Python Software Foundation. (n.d.). ctypes - A foreign function library for Python. Python Documentation. Retrieved from https://docs.python.org/3/library/ctypes.html
•	comtypes. (n.d.). comtypes. Retrieved from https://github.com/enthought/comtypes
•	Pycaw. (n.d.). Pycaw. Retrieved from https://github.com/AndreMiras/pycaw

•	For research https://github.com/pratham-bhatnagar/Gesture-Volume-Control and  https://youtu.be/9iEPzbG-xLE


