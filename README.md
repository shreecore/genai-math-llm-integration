## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:
Develop a Python application that calculates the volume of a cylinder using the formula:

Volume=πr^2h

The application should use OpenAI Function Calling, allowing the LLM to recognize when the calculation is needed, invoke the appropriate Python function, and return the result in a natural language response.

### DESIGN STEPS:

#### STEP 1:
Import the required Python libraries (openai, json, math, dotenv, and os) and load the OpenAI API key from the environment.

#### STEP 2:
Create a Python function named calculate_cylinder_volume(radius, height) that computes the cylinder's volume using the mathematical formula and returns the result in JSON format.

#### STEP 3:
Define the function schema for the LLM, send the user's query to the Chat Completion API, execute the requested function with the provided arguments, and send the function output back to the model to generate the final response.

### PROGRAM:
```
import os
import json
import math
import openai
from dotenv import load_dotenv, find_dotenv

# Load API Key
_ = load_dotenv(find_dotenv())
openai.api_key = os.environ["OPENAI_API_KEY"]


# Function to calculate cylinder volume
def calculate_cylinder_volume(radius, height):
    """Calculate the volume of a cylinder"""

    volume = math.pi * (radius ** 2) * height

    result = {
        "radius": radius,
        "height": height,
        "volume": round(volume, 2),
        "unit": "cubic units"
    }

    return json.dumps(result)


# Function Definition
functions = [
    {
        "name": "calculate_cylinder_volume",
        "description": "Calculate the volume of a cylinder using radius and height.",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {
                    "type": "number",
                    "description": "Radius of the cylinder"
                },
                "height": {
                    "type": "number",
                    "description": "Height of the cylinder"
                }
            },
            "required": ["radius", "height"]
        }
    }
]


# User Query
messages = [
    {
        "role": "user",
        "content": "Calculate the volume of a cylinder with radius 5 and height 10."
    }
]


# Chat Completion
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages,
    functions=functions
)

print("Chat Completion Response:")
print(response)

response_message = response["choices"][0]["message"]

print("\nAssistant Message:")
print(response_message)

if response_message.get("function_call"):

    function_name = response_message["function_call"]["name"]

    arguments = json.loads(
        response_message["function_call"]["arguments"]
    )

    print("\nFunction Name:")
    print(function_name)

    print("\nArguments:")
    print(arguments)

    observation = calculate_cylinder_volume(
        radius=arguments["radius"],
        height=arguments["height"]
    )

    print("\nFunction Output:")
    print(observation)

    messages.append(response_message)

    messages.append(
        {
            "role": "function",
            "name": function_name,
            "content": observation
        }
    )

    second_response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=messages
    )

    print("\nFinal Answer:")
    print(second_response["choices"][0]["message"]["content"])
```

### OUTPUT:
<img width="1362" height="768" alt="image" src="https://github.com/user-attachments/assets/31a609ce-0ea4-46f2-969b-bd3962c907c2" />
<img width="1366" height="771" alt="image" src="https://github.com/user-attachments/assets/67ed2d9f-d001-4c40-b5e6-91e7954c05b2" />

### RESULT:
The Python function for calculating the volume of a cylinder was successfully integrated with the OpenAI Chat Completion system using LLM Function Calling. The model correctly identified when to invoke the function, passed the required parameters (radius and height), executed the calculation, and returned the computed volume as a natural language response.
