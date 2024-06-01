# Hamza-AI-Personal-Assistant
Hamza: AI Personal Assistant



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

