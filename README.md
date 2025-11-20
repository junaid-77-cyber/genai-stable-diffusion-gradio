## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:
To develop a simple web application that generates images from text prompts using the Stable Diffusion model through the Hugging Face API and displays the output using Gradio.

### DESIGN STEPS:

#### Step 1: 
Import required libraries such as requests, gradio, dotenv, PIL, and base64.
#### Step 2: 
Load the Hugging Face API key securely using the .env file.
#### Step 3:
Create a POST request function to send the user prompt to the text-to-image model API.
#### Step 4:
Write a helper function to convert the received base64 image from the API into a PIL image format.
#### Step 5: 
Build a generate() function that sends the prompt and parameters (steps, size, guidance) to the API and returns the generated image.
#### Step 6: 
Design a Gradio interface with a textbox for prompts, optional advanced parameters, and an image output area.
#### Step 7: 
Launch the application using demo.launch() to generate and display AI-created images.

### PROGRAM:
```
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper function
import requests, json

#Text-to-image endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }   
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

import gradio as gr 

# Convert base64 to PIL image
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

def generate(prompt, negative_prompt, steps, guidance, width, height):
    params = {
        "negative_prompt": negative_prompt,
        "num_inference_steps": steps,
        "guidance_scale": guidance,
        "width": width,
        "height": height
    }
    
    output = get_completion(prompt, params)
    pil_image = base64_to_pil(output)
    return pil_image

with gr.Blocks() as demo:
    gr.Markdown("# Image Generation with Stable Diffusion - Done By Junaid Sardar S")
    with gr.Row():
        with gr.Column(scale=4):
            prompt = gr.Textbox(label="Your prompt")
        with gr.Column(scale=1, min_width=50):
            btn = gr.Button("Submit")
    with gr.Accordion("Advanced options", open=False):
            negative_prompt = gr.Textbox(label="Negative prompt")
            with gr.Row():
                with gr.Column():
                    steps = gr.Slider(label="Inference Steps", minimum=1, maximum=100, value=25,
                      info="In many steps will the denoiser denoise the image?")
                    guidance = gr.Slider(label="Guidance Scale", minimum=1, maximum=20, value=7,
                      info="Controls how much the text prompt influences the result")
                with gr.Column():
                    width = gr.Slider(label="Width", minimum=64, maximum=512, step=64, value=512)
                    height = gr.Slider(label="Height", minimum=64, maximum=512, step=64, value=512)
    output = gr.Image(label="Result")
            
    btn.click(fn=generate, inputs=[prompt,negative_prompt,steps,guidance,width,height], outputs=[output])

demo.launch(share=True)
```

### OUTPUT:
![alt text](<Screenshot 2025-11-20 114710.png>)
![alt text](<Screenshot 2025-11-20 115450.png>)
The result image is added.

### RESULT:
The application successfully generates images based on user text prompts and displays them through a user-friendly Gradio interface, allowing customization of image settings like size, steps, and guidance scale.