---
description: >-
  Here is a sample of a web server written in Python to receive new message
  events.
---

# Python Web Server

```python
import json
import requests

from time import sleep
from http.server import HTTPServer, BaseHTTPRequestHandler

# Your BlueBubbles local address + port
server_addr = 'http://localhost:1234'
server_password = 'your-server-password'

# Create a class to handle a POST request on port 8000
class PostHandler(BaseHTTPRequestHandler):

    def return_bad_request(self, error="Bad Request"):
        """
        A function to return a 400 error.

        Args:
            error (str): The error message to return
        """

        self.send_response(400)
        self.end_headers()
        self.wfile.write(error.encode('utf-8'))

    def return_ok(self, message="OK"):
        """
        A function to return a 200 response.

        Args:
            message (str): The message to return
        """

        self.send_response(200)
        self.end_headers()
        self.wfile.write(message.encode('utf-8'))

    def do_POST(self):
        """
        A POST request handler. This is called when a POST request is received.
        This function does some validation around "valid" requests relative to
        what the BlueBubbles server will emit via Webhooks.
        """

        print("Received POST request")

        # Ignore any request that isn't JSON
        if self.headers["Content-Type"] != "application/json":
            return self.return_bad_request()

        # Read the data
        content_length = int(self.headers["Content-Length"])
        post_data = self.rfile.read(content_length)

        try:
            # Convert the data to a JSON object and pass it to the handler
            data = json.loads(post_data)
            self.handle_json(data)
        except ValueError as ex:
            return self.return_bad_request(ex.message or "Invalid JSON received")

        self.return_ok()

    def handle_json(self, data):
        """
        Handles a generic JSON object. This function will check the type of the
        event and handle it accordingly.

        Args:
            data (dict): The JSON data        
        """

        print("Received JSON data: ", data)

        if data.get('type') == 'new-message':
            self.handle_new_message()
        else:
            print("Unhandled event type: ", data.get('type'))

    def handle_new_message(self, data):
        """
        Handles a new-message event.
        This is a general handler that will respond to any new message with "Hello World!"

        Args:
            data (dict): The JSON data        
        """

        if not isinstance(data.get('data'), dict):
            return
        
        # Ignore messages that I sent
        if data.get('data').get('isFromMe'):
            return
        
        # Extract the chat guid and message text
        chats = data.get('data').get('chats', [])
        if not chats:
            raise ValueError('No chats found in data')

        chat_guid = chats[0].get('guid')
        print("Detected incoming message... Dispatching response...")
        self.send_text(chat_guid, "Hello World!")
        print("Response dispatched")

    def send_text(self, chat_guid, text, method='private-api'):
        """
        Sends a text message to a chat via the BlueBubbles server.

        Args:
            chat_guid (str): The chat guid to send the message to
            text (str): The text to send
            method (str): The method to use to send the message. Defaults to "private-api"
        """
        params = {'password': server_password}
        data = {
            'chatGuid': chat_guid,
            'text': text,
            'method': method
        }

        requests.post(
            '{}/api/v1/message/text'.format(server_addr),
            json=data,
            params=params,
            headers={'Content-Type': 'application/json'}
        )


# Create a server on port 8000
server = HTTPServer(("", 8000), PostHandler)
print("Server started on port 8000")
server.serve_forever()
```
