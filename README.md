# HandAI
import cv2
import mediapipe as mp
import pyautogui as py
import math

mp_hands = mp.solutions.hands 
mp_drawing = mp.solutions.drawing_utils

hands = mp_hands.Hands(max_num_hands=1,min_detection_confidence=0.7)

cap = cv2.VideoCapture(0)

screen_width, screen_heigth = py.size()

def calculate_distance(p1,p2):
        return math.sqrt((p1.x - p2.x)**2 + (p1.y - p2.y)**2 + (p1.z - p2.z)**2)

while True:
    ret, frame = cap.read()

    if not ret:
        print("Câmera não encontrada:(")
        break
    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    result = hands.process(frame_rgb)

    frame_height, frame_width, _ = frame.shape

    if result.multi_hand_landmarks:
        for hand_landmarks in result.multi_hand_landmarks:
            index_finger_tip = hand_landmarks.landmark[8]
            thumb_tip = hand_landmarks.landmark[4]

            distance = calculate_distance(index_finger_tip, thumb_tip)

            x = int(index_finger_tip.x * frame_width)
            y = int(index_finger_tip.y * frame_height)

            screen_x = screen_width - (screen_width / frame_width * x)
            screen_y = screen_heigth / frame_height * y

            py.moveTo(screen_x )

            if distance < 0.05:
                py.click()

            mp_drawing.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)

            cv2.imshow('Tracking da mão', frame)
            
            if cv2.waitKey(1) & 0xFF == ord ('q'):
                break
       
cap.release()
cv2.destroyAllWindows()
       
