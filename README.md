import random
import re
import json
from datetime import datetime

class SimpleChatbot:
    def __init__(self):
        # Predefined responses for different categories
        self.responses = {
            'greeting': [
                "Hello! How can I help you today?",
                "Hi there! What's on your mind?",
                "Hey! Nice to meet you!",
                "Greetings! How are you doing?"
            ],
            'farewell': [
                "Goodbye! Have a great day!",
                "See you later! Take care!",
                "Bye! It was nice talking with you!",
                "Farewell! Come back anytime!"
            ],
            'thanks': [
                "You're welcome!",
                "Happy to help!",
                "No problem at all!",
                "Glad I could assist you!"
            ],
            'how_are_you': [
                "I'm doing well, thanks for asking! How about you?",
                "I'm great! Thanks for asking. How are you?",
                "I'm doing fine! How's your day going?",
                "All good here! What about you?"
            ],
            'name': [
                "I'm a simple chatbot created to help you!",
                "You can call me ChatBot. What's your name?",
                "I'm your friendly AI assistant!",
                "I'm a chatbot here to chat with you!"
            ],
            'weather': [
                "I don't have access to real-time weather data, but I hope it's nice where you are!",
                "I can't check the weather, but I hope you're having a pleasant day!",
                "I don't know the current weather, but I hope it's beautiful outside!"
            ],
            'time': [
                f"The current time is {datetime.now().strftime('%H:%M:%S')}",
                f"It's {datetime.now().strftime('%I:%M %p')} right now",
                f"The time is {datetime.now().strftime('%H:%M')}"
            ],
            'default': [
                "That's interesting! Tell me more.",
                "I see. Can you elaborate on that?",
                "Hmm, I'm not sure I understand completely. Can you explain?",
                "That's a good point. What do you think about it?",
                "I'd love to hear more about that!",
                "Could you tell me more details?",
                "That sounds intriguing!"
            ]
        }
        
        # Patterns to match user input
        self.patterns = {
            'greeting': r'\b(hello|hi|hey|greetings|good morning|good afternoon|good evening)\b',
            'farewell': r'\b(bye|goodbye|see you|farewell|exit|quit)\b',
            'thanks': r'\b(thank you|thanks|thank)\b',
            'how_are_you': r'\b(how are you|how do you do|how\'s it going)\b',
            'name': r'\b(what is your name|who are you|what are you called)\b',
            'weather': r'\b(weather|temperature|rain|sunny|cloudy)\b',
            'time': r'\b(time|what time|current time)\b'
        }
        
        # Store conversation history
        self.conversation_history = []
        
    def preprocess_input(self, user_input):
        """Clean and preprocess user input"""
        return user_input.lower().strip()
    
    def match_pattern(self, user_input):
        """Match user input against predefined patterns"""
        for intent, pattern in self.patterns.items():
            if re.search(pattern, user_input, re.IGNORECASE):
                return intent
        return 'default'
    
    def generate_response(self, user_input):
        """Generate a response based on user input"""
        processed_input = self.preprocess_input(user_input)
        intent = self.match_pattern(processed_input)
        
        # Get random response from the matched category
        response = random.choice(self.responses[intent])
        
        # Store conversation
        self.conversation_history.append({
            'user': user_input,
            'bot': response,
            'timestamp': datetime.now().isoformat()
        })
        
        return response
    
    def chat(self):
        """Main chat loop"""
        print("🤖 Simple Chatbot")
        print("=" * 50)
        print("Hi! I'm your friendly chatbot. Type 'quit' or 'exit' to end the conversation.")
        print("=" * 50)
        
        while True:
            try:
                user_input = input("\nYou: ").strip()
                
                if not user_input:
                    continue
                
                # Check for exit conditions
                if user_input.lower() in ['quit', 'exit', 'bye', 'goodbye']:
                    print("Bot:", random.choice(self.responses['farewell']))
                    break
                
                # Generate and display response
                response = self.generate_response(user_input)
                print("Bot:", response)
                
            except KeyboardInterrupt:
                print("\nBot: Goodbye! Have a great day!")
                break
            except Exception as e:
                print(f"Bot: Sorry, I encountered an error: {e}")
    
    def get_conversation_history(self):
        """Return the conversation history"""
        return self.conversation_history
    
    def save_conversation(self, filename="chat_history.json"):
        """Save conversation history to a file"""
        with open(filename, 'w') as f:
            json.dump(self.conversation_history, f, indent=2)
        print(f"Conversation saved to {filename}")

# Enhanced version with learning capability
class LearningChatbot(SimpleChatbot):
    def __init__(self):
        super().__init__()
        self.learned_responses = {}
    
    def teach_response(self, trigger, response):
        """Allow users to teach the bot new responses"""
        if trigger not in self.learned_responses:
            self.learned_responses[trigger] = []
        self.learned_responses[trigger].append(response)
        print(f"Thanks! I learned that when you say '{trigger}', I should respond with '{response}'")
    
    def generate_response(self, user_input):
        """Enhanced response generation with learning"""
        processed_input = self.preprocess_input(user_input)
        
        # Check if we have learned responses for this input
        for trigger, responses in self.learned_responses.items():
            if trigger.lower() in processed_input:
                response = random.choice(responses)
                self.conversation_history.append({
                    'user': user_input,
                    'bot': response,
                    'timestamp': datetime.now().isoformat(),
                    'learned': True
                })
                return response
        
        # Fall back to default behavior
        return super().generate_response(user_input)

# Demo functions
def demo_basic_chatbot():
    """Demo the basic chatbot"""
    print("🚀 Starting Basic Chatbot Demo")
    bot = SimpleChatbot()
    bot.chat()
    return bot

def demo_learning_chatbot():
    """Demo the learning chatbot with some pre-taught responses"""
    print("🚀 Starting Learning Chatbot Demo")
    bot = LearningChatbot()
    
    # Pre-teach some responses
    bot.teach_response("python", "Python is an amazing programming language!")
    bot.teach_response("machine learning", "ML is fascinating! Are you working on any ML projects?")
    bot.teach_response("google colab", "Colab is great for running Python notebooks in the cloud!")
    
    bot.chat()
    return bot

def interactive_demo():
    """Interactive demo with menu options"""
    print("🤖 Welcome to the Chatbot Demo!")
    print("=" * 40)
    print("Choose an option:")
    print("1. Basic Chatbot")
    print("2. Learning Chatbot")
    print("3. Quick Test")
    
    choice = input("Enter your choice (1-3): ").strip()
    
    if choice == '1':
        return demo_basic_chatbot()
    elif choice == '2':
        return demo_learning_chatbot()
    elif choice == '3':
        return quick_test()
    else:
        print("Invalid choice. Starting basic chatbot...")
        return demo_basic_chatbot()

def quick_test():
    """Quick test of chatbot responses"""
    print("🧪 Quick Test Mode")
    bot = SimpleChatbot()
    
    test_inputs = [
        "Hello!",
        "How are you?",
        "What's your name?",
        "What time is it?",
        "Thank you!",
        "I like programming",
        "Goodbye"
    ]
    
    for test_input in test_inputs:
        response = bot.generate_response(test_input)
        print(f"User: {test_input}")
        print(f"Bot: {response}")
        print("-" * 30)
    
    return bot

# Main execution
if __name__ == "__main__":
    # Run the interactive demo
    chatbot = interactive_demo()
    
    # Optional: Display conversation history
    print("\n📝 Conversation Summary:")
    history = chatbot.get_conversation_history()
    print(f"Total messages: {len(history)}")
    
    # Optional: Save conversation
    save_choice = input("\nWould you like to save the conversation? (y/n): ").strip().lower()
    if save_choice == 'y':
        chatbot.save_conversation()
