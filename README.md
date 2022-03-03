# Deeplogitech_Times
Times_Code
import json
import requests
from bs4 import BeautifulSoup
import csv

url = 'https://time.com/'

r = requests.get(url)
soup = BeautifulSoup(r.content, 'html5lib')

stories = []
table = soup.findAll('section', attrs={'class': "homepage-module latest", 'data-module_name': "Latest Stories"})
print(table)
for row in table:
    story = dict()
    story['title'] = row.h2.text
    story['url'] = row.a['href']
    stories.append(story)

print(stories)

