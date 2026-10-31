```python
from openai import OpenAI

client = OpenAI()

prompt=input("prompt:")
system_prompt="Limit your answer to one sentence.Pre"

response =client.responses.create(
	input=user_prompt,
	instruction=system_prompt,
	model="gpt-5"
)

print(response.output_text)
```
binary digit->bit :either 0 or 1