# Hamza-AI-Personal-Assistant
Hamza: AI Personal Assistant


Now that you have created the image, you can use it as part of your presentation or as a visual element in your GUI for the Hamza Voice Assistant. Here’s how you can integrate the image into your GUI application:

Steps to Integrate the Image into Your GUI Application
Save the Image:

Ensure your newly created image is saved in a directory within your project. For this example, let's say you save it as hamza_assistant.png in the images directory of your project.
Modify the GUI Code:

Update the GUI code to display the new image.
Here’s the updated GUI code to include your new image:

from tkinter import *
from PIL import Image, ImageTk
import action
import spech_to_text
import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import requests
import sys

# Initialize the voice engine
engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)

# OpenAI API key (use environment variable for security)
API_KEY = os.getenv('OPENAI_API_KEY')  # Ensure you set this environment variable

def speak(audio):
    engine.say(audio)
    engine.runAndWait()

def greetUser():
    hour = int(datetime.datetime.now().hour)
    if hour >= 0 and hour < 11:
        speak("Good Morning!")
    elif hour >= 11 and hour < 18:
        speak("Good Afternoon!")
    else:
        speak("Good Evening!")
    speak("My name is Hamza, I am your voice assistant. Please tell me how may I help you?")

def takeCommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)

    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-UK')
        print(f"User said: {query}\n")
    except sr.UnknownValueError:
        print("Sorry, I did not understand that. Please speak again.")
        return "None"
    except sr.RequestError:
        print("Sorry, my speech service is down.")
        return "None"
    return query

def getChatGPTResponse(prompt):
    if not API_KEY:
        return "API key is not set. Please set the OPENAI_API_KEY environment variable."

    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'Content-Type': 'application/json'
    }
    data = {
        "model": "gpt-4",
        "messages": [{"role": "user", "content": prompt}]
    }
    try:
        response = requests.post('https://api.openai.com/v1/chat/completions', headers=headers, json=data)
        response.raise_for_status()
        return response.json()['choices'][0]['message']['content'].strip()
    except requests.RequestException as e:
        return f"Sorry, I am unable to get a response from ChatGPT at the moment. {e}"

def executeCommand(query):
    query = query.lower()
    if 'wikipedia' in query:
        speak('Searching in Wikipedia...')
        query = query.replace("wikipedia", "")
        results = wikipedia.summary(query, sentences=2)
        speak("According to Wikipedia")
        print(results)
        speak(results)
    elif 'open youtube' in query:
        webbrowser.open("youtube.com")
    elif 'open google' in query:
        webbrowser.open("google.com")
    elif 'open university website' in query:
        webbrowser.open("bahria.edu.pk")
    elif 'open stack overflow' in query or 'open stackoverflow' in query:
        speak("Here you go to Stack Overflow. Happy coding!")
        webbrowser.open("stackoverflow.com")
    elif 'play music' in query:
        music_dir = 'F:\\MP3'
        try:
            songs = os.listdir(music_dir)
            if songs:
                os.startfile(os.path.join(music_dir, songs[0]))
            else:
                speak("No music files found in the directory.")
        except Exception as e:
            speak(f"Unable to play music. {e}")
    elif 'the time' in query:
        strTime = datetime.datetime.now().strftime("%H:%M:%S")
        speak(f"Sir, the time is {strTime}")
    elif 'open chrome' in query:
        codePath = r"C:\Program Files\Google\Chrome\Application\chrome.exe"
        os.startfile(codePath)
    elif 'open git' in query:
        codePath = "C:\\Program Files\\Git\\git-bash.exe"
        os.startfile(codePath)
    elif 'quit' in query or 'shut' in query:
        speak("Thanks for giving me your time. Goodbye!")
        sys.exit()
    else:
        response = getChatGPTResponse(query)
        print(response)
        speak(response)

def waitForActivation():
    while True:
        query = takeCommand()
        if query is None:
            continue
        if 'hi hamza' in query.lower() or 'hello hamza' in query.lower() or 'hamza' in query.lower():
            greetUser()
            break

def User_send():
    send = entry1.get()
    bot = executeCommand(send)
    text.insert(END, "Me --> " + send + "\n")
    if bot:
        text.insert(END, "Bot <-- " + str(bot) + "\n")
    if bot == "ok sir":
        root.destroy()

def ask():
    ask_val = takeCommand()
    if ask_val:
        bot_val = executeCommand(ask_val)
        text.insert(END, "Me --> " + ask_val + "\n")
        if bot_val:
            text.insert(END, "Bot <-- " + str(bot_val) + "\n")
        if bot_val == "ok sir":
            root.destroy()

def delete_text():
    text.delete("1.0", "end")

root = Tk()
root.geometry("550x675")
root.title("Hamza - Your Personal AI Assistant")
root.resizable(False, False)
root.config(bg="#6F8FAF")

# Main Frame
Main_frame = LabelFrame(root, padx=100, pady=7, borderwidth=3, relief="raised")
Main_frame.config(bg="#6F8FAF")
Main_frame.grid(row=0, column=1, padx=55, pady=10)

# Text Label
Text_label = Label(Main_frame, text="Hamza - Your Personal AI Assistant", font=("Comic Sans MS", 14, "bold"), bg="#356696")
Text_label.grid(row=0, column=0, padx=20, pady=10)

# Image
Display_Image = ImageTk.PhotoImage(Image.open("images/hamza_assistant.png"))
Image_Label = Label(Main_frame, image=Display_Image)
Image_Label.grid(row=1, column=0, pady=20)

# Add a text widget
text = Text(root, font=('Courier 10 bold'), bg="#356696")
text.grid(row=2, column=0)
text.place(x=100, y=375, width=375, height=100)

# Add an entry widget
entry1 = Entry(root, justify=CENTER)
entry1.place(x=100, y=500, width=350, height=30)

# Add buttons
button1 = Button(root, text="ASK", bg="#356696", pady=16, padx=40, borderwidth=3, relief=SOLID, command=ask)
button1.place(x=70, y=575)

button2 = Button(root, text="Send", bg="#356696", pady=16, padx=40, borderwidth=3, relief=SOLID, command=User_send)
button2.place(x=400, y=575)

button3 = Button(root, text="Delete", bg="#356696", pady=16, padx=40, borderwidth=3, relief=SOLID, command=delete_text)
button3.place(x=225, y=575)

if __name__ == "__main__":
    waitForActivation()
    root.mainloop()




Explanation:
Image Integration:

The ImageTk.PhotoImage object is created using the hamza_assistant.png image you created.
This image is then placed in the Main_frame of the GUI.
Functionality:

The ask, User_send, and delete_text functions handle voice and text input from the user and display the responses from Hamza.
The waitForActivation function waits for the activation command (e.g., "Hi Hamza") to start the assistant.
Running the Program:
Ensure all dependencies are installed:  pip install pyttsx3 speech_recognition requests requests-html Pillow

Set your OpenAI API Key:

Ensure you have set the OPENAI_API_KEY environment variable. You can set it in your terminal session: export OPENAI_API_KEY='your_openai_api_key'

Replace your_program_file.py with the name of your Python file containing the updated code.

This will launch the GUI for your Hamza assistant, and you can interact with it through text and voice commands.




......................................................................................................................


API Key Security: For security reasons, never hard-code your API key directly in your code. Instead, consider using environment variables.
Exception Handling: Enhance exception handling for robustness.
Comments and Documentation: Ensure the comments are clear and helpful.





Here’s the updated and polished version of your voice assistant:

python
Copy code
import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import requests
import sys

# Initialize the voice engine
engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)

# OpenAI API key (use environment variable for security)
API_KEY = os.getenv('OPENAI_API_KEY')  # Ensure you set this environment variable

def speak(audio):
    """
    This method will allow Zira to speak, It takes our voice as an argument.
    """
    engine.say(audio)
    engine.runAndWait()

def greetUser():
    """
    This method will always greet the user at the start.
    """
    hour = int(datetime.datetime.now().hour)
    if hour >= 0 and hour < 11:
        speak("Good Morning!")
    elif hour >= 11 and hour < 18:
        speak("Good Afternoon!")
    else:
        speak("Good Evening!")
    speak("My name is Zira, I am your voice assistant. Please tell me how may I help you?")

def takeCommand():
    """
    It takes microphone input from the user and returns string output, returns 'None' in case of any problem.
    """
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("How can I help?")
        r.pause_threshold = 1
        audio = r.listen(source)

    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-UK')
        print(f"User said: {query}\n")
    except sr.UnknownValueError:
        print("Sorry, I did not understand that. Please speak again.")
        return "None"
    except sr.RequestError:
        print("Sorry, my speech service is down.")
        return "None"
    return query

def getChatGPTResponse(prompt):
    """
    Send the user's query to OpenAI's API and return the response.
    """
    if not API_KEY:
        return "API key is not set. Please set the OPENAI_API_KEY environment variable."

    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'Content-Type': 'application/json'
    }
    data = {
        "model": "gpt-4",
        "messages": [{"role": "user", "content": prompt}]
    }
    try:
        response = requests.post('https://api.openai.com/v1/chat/completions', headers=headers, json=data)
        response.raise_for_status()
        return response.json()['choices'][0]['message']['content'].strip()
    except requests.RequestException as e:
        return f"Sorry, I am unable to get a response from ChatGPT at the moment. {e}"

def executeCommand(query):
    """
    Executes the given command.
    """
    query = query.lower()
    if 'wikipedia' in query:
        speak('Searching in Wikipedia...')
        query = query.replace("wikipedia", "")
        results = wikipedia.summary(query, sentences=2)
        speak("According to Wikipedia")
        print(results)
        speak(results)
    elif 'open youtube' in query:
        webbrowser.open("youtube.com")
    elif 'open google' in query:
        webbrowser.open("google.com")
    elif 'open university website' in query:
        webbrowser.open("bahria.edu.pk")
    elif 'open stack overflow' in query or 'open stackoverflow' in query:
        speak("Here you go to Stack Overflow. Happy coding!")
        webbrowser.open("stackoverflow.com")
    elif 'play music' in query:
        music_dir = 'F:\\MP3'
        try:
            songs = os.listdir(music_dir)
            if songs:
                os.startfile(os.path.join(music_dir, songs[0]))
            else:
                speak("No music files found in the directory.")
        except Exception as e:
            speak(f"Unable to play music. {e}")
    elif 'the time' in query:
        strTime = datetime.datetime.now().strftime("%H:%M:%S")
        speak(f"Sir, the time is {strTime}")
    elif 'open chrome' in query:
        codePath = r"C:\Program Files\Google\Chrome\Application\chrome.exe"
        os.startfile(codePath)
    elif 'open git' in query:
        codePath = "C:\\Program Files\\Git\\git-bash.exe"
        os.startfile(codePath)
    elif 'quit' in query or 'shut' in query:
        speak("Thanks for giving me your time. Goodbye!")
        sys.exit()
    else:
        response = getChatGPTResponse(query)
        print(response)
        speak(response)

if __name__ == "__main__":
    greetUser()
    while True:
        query = takeCommand()
        if query == "None":
            continue
        executeCommand(query)







        
Key Changes:
API Key Security: Use environment variables to store sensitive information like the API key.
Enhanced Exception Handling: Added exception handling for various potential errors.
Robust Music Directory Handling: Added checks and exception handling for playing music.
Improved Comments and Documentation: Clarified comments to make the code more understandable.
Setting Up Environment Variable:
To set the environment variable for your OpenAI API key, you can follow these steps:

On Windows:
set OPENAI_API_KEY=your-api-key


Make sure to replace your-api-key with your actual OpenAI API key.

Running the Assistant:
Install Required Libraries: pip install pyttsx3 SpeechRecognition wikipedia requests

Run the Script: python my_voice_assistant.py

Interacting with the Assistant:
You can now interact with the assistant by speaking commands and questions. The assistant will handle predefined commands directly and forward other queries to ChatGPT for a response.

