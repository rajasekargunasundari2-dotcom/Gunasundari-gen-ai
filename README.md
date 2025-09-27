# ✅ SmartSDLC - AI Powered Software Development
# Uses IBM Granite model (Hugging Face) + Gradio in Google Colab

# Install dependencies (only needed in Colab)
!pip install gradio transformers huggingface_hub gitpython datasets

# ------------------------------
# Import libraries
# ------------------------------
import gradio as gr
from transformers import pipeline
from huggingface_hub import login
import os
from git import Repo

# ------------------------------
# Hugging Face Authentication
# ------------------------------
# 🔑 Replace with your Hugging Face token
login("your_huggingface_token_here")

# ------------------------------
# Load IBM Granite Model
# ------------------------------
granite_model = pipeline("text-generation", model="ibm-granite/granite-13b-instruct")

# ------------------------------
# SmartSDLC Functions
# ------------------------------
def generate_code(requirement):
    response = granite_model(requirement, max_length=200, do_sample=True)
    return response[0]['generated_text']

def fix_bug(code_snippet):
    prompt = f"Fix the bugs in the following Python code:\n{code_snippet}"
    response = granite_model(prompt, max_length=200)
    return response[0]['generated_text']

def write_doc(code_snippet):
    prompt = f"Write detailed documentation for the following code:\n{code_snippet}"
    response = granite_model(prompt, max_length=200)
    return response[0]['generated_text']

# ------------------------------
# Gradio User Interface
# ------------------------------
with gr.Blocks() as demo:
    gr.Markdown("## 🚀 SmartSDLC - AI Powered Software Development")
    
    with gr.Tab("Generate Code"):
        requirement = gr.Textbox(label="Enter Requirement")
        output_code = gr.Textbox(label="Generated Code")
        btn1 = gr.Button("Generate")
        btn1.click(generate_code, requirement, output_code)
    
    with gr.Tab("Fix Bugs"):
        bug_code = gr.Textbox(label="Paste Buggy Code")
        fixed_code = gr.Textbox(label="Fixed Code")
        btn2 = gr.Button("Fix")
        btn2.click(fix_bug, bug_code, fixed_code)
    
    with gr.Tab("Documentation"):
        code_input = gr.Textbox(label="Enter Code")
        doc_output = gr.Textbox(label="Documentation")
        btn3 = gr.Button("Generate Doc")
        btn3.click(write_doc, code_input, doc_output)

# ------------------------------
# Launch Application
# ------------------------------
demo.launch()

# ------------------------------
# (Optional) Upload Project to GitHub
# ------------------------------
repo_url = "https://github.com/yourusername/SmartSDLC.git"  # replace with your repo
local_path = "/content/SmartSDLC"

if not os.path.exists(local_path):
    Repo.clone_from(repo_url, local_path)

# Save main file
with open(local_path + "/app.py", "w") as f:
    f.write("print('SmartSDLC project setup complete!')")

# Commit & push
repo = Repo(local_path)
repo.git.add(all=True)
repo.index.commit("SmartSDLC initial commit")
origin = repo.remote(name="origin")
origin.push()
