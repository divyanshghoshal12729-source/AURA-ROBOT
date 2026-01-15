# AURA-ROBOT
# 1. Initialize the Engine ONCE at the top
engine = pyttsx3.init()
engine.setProperty("rate", 175) 

def speak(text: str):
    if not text: return
    print(f"Aura: {text}")
    engine.say(text)
    engine.runAndWait()

def listen(recognizer):
    try:
        with sr.Microphone() as source:
            print("\nListening...")
            audio = recognizer.listen(source, timeout=5, phrase_time_limit=8)
        
        text = recognizer.recognize_google(audio)
        print(f"You: {text}")
        return text.lower()
    except Exception:
        return None

def main():
    recognizer = sr.Recognizer()
    
    # 2. Calibrate Microphone ONCE at the start
    with sr.Microphone() as source:
        print("Calibrating for room noise...")
        recognizer.adjust_for_ambient_noise(source, duration=1)
        
    speak("Aura Robot is online. How can I help you today?")

    while True:
        query = listen(recognizer)

        if not query:
            continue

        if any(word in query for word in ["exit", "stop", "quit"]):
            speak("Powering down. Goodbye!")
            break

        # Think & Speak
        response = ollama.chat(model="llama3.2:1b", messages=[{"role": "user", "content": query}])
        speak(response["message"]["content"])

if __name__ == "__main__":
    main()
Aura is a voice-controlled AI assistant built using Python and Ollama.
