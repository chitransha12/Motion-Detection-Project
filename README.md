# Motion-Detection-Project
import cv2
import datetime

cap = cv2.VideoCapture(0)

ret, frame1 = cap.read()
ret, frame2 = cap.read()

while cap.isOpened():
    diff = cv2.absdiff(frame1, frame2)
    gray = cv2.cvtColor(diff, cv2.COLOR_BGR2GRAY)
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    _, thresh = cv2.threshold(blur, 20, 255, cv2.THRESH_BINARY)
    dilated = cv2.dilate(thresh, None, iterations=3)
    _, contours, _ = cv2.findContours(dilated, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

    for contour in contours:
        if cv2.contourArea(contour) < 900:
            continue
        x, y, w, h = cv2.boundingRect(contour)
        cv2.rectangle(frame1, (x,y), (x+w, y+h), (0,255,0), 2)
        timestamp = datetime.datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
        cv2.imwrite(f"motion_{timestamp}.jpg", frame1)

    cv2.imshow("Motion Detection", frame1)
    frame1 = frame2
    ret, frame2 = cap.read()

    if cv2.waitKey(10) == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
exit()


# CODE EXPLANATION:
1. Importing Libraries
CopyEdit
import cv2
import datetime

cv2 → OpenCV library, used for image/video processing.


datetime → Used to get the current date & time (for naming saved images).

2. Starting the Camera
CopyEdit
cap = cv2.VideoCapture(0)
Opens the default webcam (index 0).

4. Reading Initial Frames
CopyEdit
ret, frame1 = cap.read()
ret, frame2 = cap.read()
Reads two consecutive frames from the camera.


These will be compared to detect motion.

5. Main Loop (until camera is closed)
CopyEdit
while cap.isOpened():
Runs while the camera is open.

6. Calculating the Difference Between Frames
CopyEdit
diff = cv2.absdiff(frame1, frame2)
Finds pixel-by-pixel difference between the two frames.


If there’s movement, the difference will be larger in that area.

7. Converting to Grayscale
CopyEdit
gray = cv2.cvtColor(diff, cv2.COLOR_BGR2GRAY)
Makes the image black & white for easier processing.

8. Reducing Noise
CopyEdit
blur = cv2.GaussianBlur(gray, (5,5), 0)
Smooths the image to reduce random noise.

9. Thresholding
CopyEdit
_, thresh = cv2.threshold(blur, 20, 255, cv2.THRESH_BINARY)
Turns the image into black & white regions:


White = possible movement


Black = no movement

10. Dilating the Image
CopyEdit
dilated = cv2.dilate(thresh, None, iterations=3)
Makes the white regions (movement areas) bigger, so contours are easier to find.

11. Finding Moving Areas
CopyEdit
_, contours, _ = cv2.findContours(dilated, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
Finds outlines of white regions (movement).

12. Loop Over Each Detected Movement
CopyEdit
for contour in contours:
    if cv2.contourArea(contour) < 900:
        continue
Skips small areas (less than 900 pixels) to ignore small noise.

13. Drawing a Box & Saving Image
CopyEdit
x, y, w, h = cv2.boundingRect(contour)
cv2.rectangle(frame1, (x,y), (x+w, y+h), (0,255,0), 2)
timestamp = datetime.datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
cv2.imwrite(f"motion_{timestamp}.jpg", frame1)

Draws a green rectangle around movement.


Gets current time → timestamp.


Saves the frame as "motion_YYYY-MM-DD_HH-MM-SS.jpg".

14. Show the Live Video
CopyEdit
cv2.imshow("Motion Detection", frame1)
Displays the camera feed with green boxes around movement.

15. Update Frames
CopyEdit
frame1 = frame2
ret, frame2 = cap.read()
Moves to the next frame pair for comparison.

16. Exit Condition
CopyEdit
if cv2.waitKey(10) == ord('q'):
    break
Press q to exit.

17. Cleanup
CopyEdit
cap.release()
cv2.destroyAllWindows()
exit()
Releases the camera and closes all windows.

💡 In simple words:
 This code detects motion using two consecutive frames from the camera.
 If it sees movement, it draws a rectangle around it and saves a picture with the timestamp.
