from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import hashlib

app = FastAPI()

# Define the request body model
class TextRequest(BaseModel):
    text: str

# Define the generate function
@app.post("/generate")
async def generate(request: TextRequest):
    """
    Generate a checksum for the given text.
    
    Args:
        request (TextRequest): A request containing the text.

    Returns:
        dict: A dictionary with the checksum of the text.
    """
    # Calculate checksum
    checksum = hashlib.md5(request.text.encode()).hexdigest()
    return {"checksum": checksum}

from fastapi.testclient import TestClient

client = TestClient(app)

def test_generate_valid_text():
    response = client.post("/generate", json={"text": "Hello, World!"})
    assert response.status_code == 200
    assert "checksum" in response.json()

def test_generate_empty_text():
    response = client.post("/generate", json={"text": ""})
    assert response.status_code == 200
    assert response.json()["checksum"] == "d41d8cd98f00b204e9800998ecf8427e"  # MD5 for empty string
