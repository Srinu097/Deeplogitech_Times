"""
Assignment DeepLogiTech
"""

from http.server import HTTPServer, BaseHTTPRequestHandler
import json
import requests
from bs4 import BeautifulSoup

url = 'https://time.com/'
r = requests.get(url)

stories = []

# Getting the html content of "Time.com"
soup = BeautifulSoup(r.content, 'html5lib')

# Getting the latest news section
table = soup.findAll('section', attrs={'class': "homepage-module latest", 'data-module_name': "Latest Stories"})

# Targeting Title and URL of latest five news each
for i in table:
    link = i.findChildren("a", recursive=True)
    for data in link:
        story_dict = dict()
        story_dict['title'] = data.text
        story_dict['url'] = data['href']
        stories.append(story_dict)


# print(table)    # Table output all the html components regarding latest stories

# print(stories)   # Prints the stories in Python list format

jsonFile = json.dumps(stories)   # List to JSON format

print(jsonFile)   # Print the JSON


# Defining a HTTP request Handler class
class ServiceHandler(BaseHTTPRequestHandler):
    # sets basic headers for the server
    def _set_headers(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/json')
        # reads the length of the Headers
        length = int(self.headers['Content-Length'])
        # reads the contents of the request
        content = self.rfile.read(length)
        temp = str(content).strip('b\'')
        self.end_headers()
        return temp


    # GET Method Defination
    def do_GET(self):
        # defining all the headers
        self.send_response(200)
        self.send_header('Content-type', 'text/json')
        self.end_headers()
        # prints all the keys and values of the json file
        self.wfile.write(json.dumps(jsonFile).encode())


    # VIEW method defination
    def do_VIEW(self):
        # dict var. for pretty print
        display = {}
        temp = self._set_headers()
        # check if the key is present in the dictionary
        if temp in jsonFile:
            display[temp] = jsonFile[temp]
            # print the keys required from the json file
            self.wfile.write(json.dumps(display).encode())
        else:
            error = "NOT FOUND!"
            self.wfile.write(bytes(error, 'utf-8'))
            self.send_response(404)


    # POST method defination
    def do_POST(self):
        temp = self._set_headers()
        key = 0
        # getting key and value of the data dictionary
        for key, value in data.items():
            pass
        index = int(key) + 1
        jsonFile[str(index)] = str(temp)
        # write the changes to the json file
        json.dump(jsonFile)


    # PUT method Defination
    def do_PUT(self):
        temp = self._set_headers()
        # seprating input into key and value
        x = temp[:1]
        y = temp[2:]
        # check if key is in data
        if x in jsonFile:
            jsonFile[x] = y
            # write the changes to file
            json.dump(jsonFile)
        else:
            error = "NOT FOUND!"
            self.wfile.write(bytes(error, 'utf-8'))
            self.send_response(404)


    # DELETE method defination
    def do_DELETE(self):
        temp = self._set_headers()
        # check if the key is present in the dictionary
        if temp in jsonFile:
            del jsonFile[temp]
            # write the changes to json file
            json.dump(jsonFile)
        else:
            error = "NOT FOUND!"
            self.wfile.write(bytes(error, 'utf-8'))
            self.send_response(404)


# Server Initialization
server = HTTPServer(('127.0.0.1', 8000), ServiceHandler)
print('Server initialized at 127.0.0.1:8000')
server.serve_forever()
