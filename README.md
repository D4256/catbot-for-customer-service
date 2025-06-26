
customer_service_chatbot/
│
├── app.py                    # Main chatbot backend (Flask)
├── templates/
│   └── index.html            # Simple web UI
├── static/
│   └── style.css             # (Optional styling)
├── faqs.json                 # FAQ database (Q&A)
├── chatbot.py                # NLP and matching logic
├── requirements.txt
└── README.md

[
    {
        "question": "What is your return policy?",
        "answer": "You can return items within 30 days for a full refund."
    },
    {
        "question": "How do I track my order?",
        "answer": "Log in to your account and click on 'My Orders' to track your shipment."
    },
    {
        "question": "Do you offer international shipping?",
        "answer": "Yes, we ship worldwide. Shipping charges apply."
    },
    {
        "question": "How can I contact customer service?",
        "answer": "You can contact us at support@example.com or call 1800-123-456."
    }
]

import json
import string
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import nltk
nltk.download('punkt')

# Load FAQs
with open('faqs.json') as f:
    faq_data = json.load(f)

questions = [faq["question"] for faq in faq_data]
answers = [faq["answer"] for faq in faq_data]

# NLP preprocessing and matching
def get_answer(user_query):
    corpus = questions + [user_query]
    vectorizer = TfidfVectorizer().fit_transform(corpus)
    similarity = cosine_similarity(vectorizer[-1], vectorizer[:-1])
    best_match = similarity.argsort()[0][-1]
    if similarity[0][best_match] > 0.3:
        return answers[best_match]
    else:
        return "Sorry, I didn’t understand your question. Please contact support."

from flask import Flask, render_template, request
from chatbot import get_answer

app = Flask(__name__)

@app.route("/")
def home():
    return render_template("index.html")

@app.route("/chat", methods=["POST"])
def chat():
    user_input = request.form["message"]
    bot_response = get_answer(user_input)
    return render_template("index.html", user_input=user_input, bot_response=bot_response)

if __name__ == "__main__":
    app.run(debug=True)

<!DOCTYPE html>
<html>
<head>
    <title>Customer Service Chatbot</title>
    <style>
        body { font-family: Arial; padding: 50px; background: #f9f9f9; }
        .chat-box { max-width: 600px; margin: auto; background: white; padding: 20px; border-radius: 8px; }
        input[type=text] { width: 80%; padding: 10px; }
        input[type=submit] { padding: 10px 20px; }
    </style>
</head>
<body>
    <div class="chat-box">
        <h2>Customer Service Chatbot</h2>
        <form action="/chat" method="post">
            <input type="text" name="message" placeholder="Ask a question..." required>
            <input type="submit" value="Send">
        </form>
        {% if user_input %}
            <p><strong>You:</strong> {{ user_input }}</p>
            <p><strong>Bot:</strong> {{ bot_response }}</p>
        {% endif %}
    </div>
</body>
</html>
Flask
scikit-learn
nltk


# 🤖 Customer Service Chatbot

This is a simple NLP-based chatbot for answering basic customer support questions.

## 🛠 Features
- Rule-based question matching
- NLP via TF-IDF and cosine similarity
- Simple Flask-based web UI

## 🚀 How to Run

1. Install requirements:
