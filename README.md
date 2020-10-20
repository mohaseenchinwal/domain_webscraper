# domain_webscraper
A simple python based script to extract web content from a list of urls and categorize them based on custom labels. For web content analysis

#Import the python libraries

import requests
import os
import re
import sys
import pandas as pd
from requests.packages.urllib3.exceptions import InsecureRequestWarning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
from bs4 import BeautifulSoup

#Intialize the dataframe with max column width to accomadate the web content

pd.set_option('display.max_rows', 10000000
df = pd.DataFrame(columns = ['Url', 'Content', 'wordcount', 'httpstatus', 'status'])
print("Empty Dataframe ", df)

#Read the input from text file fetch html web pages and extract the text from the html.
#compare the contents of objects defined  in the low with predetermined values to catograize with custom lables
#Group them in a  dataframe print on shell output along with saving those contents in a csv file.
#Raise an exception for ssl checks fail skip the line in the input file this exception allows the scripts from crashing.


df.to_csv('webstat.csv', encoding='utf-8')

filepath = 'domains.txt'
with open(filepath) as fp:
   line = fp.readline()
   cnt = 1
   while line:
       headers = {"User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:54.0) Gecko/20100101 Firefox/54.0","Connection":"close","Accept-Language":"en-US,en;q=0.5","Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8","Upgrade-Insecure-Requests":"1"}
       try:
          response = requests.get(line.strip(), headers=headers, timeout=15, verify=False)
          soup = BeautifulSoup(response.text, 'html.parser')
          result = soup.find_all(text=True)
          output = ''
          blacklist = [
          '[document]',
   	  'noscript',
	  'header',
	  'html',
	  'meta',
	  'head', 
	  'input',
	  'script',
          'style',
	  # there may be more elements you don't want, such as "style", etc.
          ]
          for t in result:
	          if t.parent.name not in blacklist:
		          output += '{} '.format(t)
          output = re.sub('\n|[[\d+\]]', ' ', output)           
          print (line.rstrip())
          print (response) 
          cout=print(len(output))                
          if "ERROR: The requested URL could not be retrieved" in output:
              stat='IN ACTIVE'
          elif "This domain is registered through Routedge" in output:
              stat='Routedge PARKING PAGE'
          elif "Error     (Not Found)!!     .   That’s an erro" in output:
              stat='IN ACTIVE'
          elif "Site not found · DreamHost     Site Not Found" in output:
              stat='ACTIVE but not operational'
          elif len(output) == 0:
              stat='Blank PAGE'
          elif "Instra"  in output:
              stat='Instra PARKING PAGE'
          elif "Plesk" in output:
              stat='Active but Domain Reseller Page'
          elif "Domainmonster.com"  in output:
              stat='Active but Domainmonster Reseller Page'
          elif "Not Found Not Found   HTTP Error" in output:
              stat='IN ACTIVE'
          elif "Index of /   Index of /       Name Last modi" in output:
              stat='ACTIVE but not operational'
          elif "Domain Registered at Safenames" in output:
              stat='Safenames PARKING PAGE'
          elif "Page Redirection    Note: don't tell people to" in output:
              stat='IN ACTIVE'
          elif "Protection des marques   Cybers  name shield" in output:
              stat='Active but Name Sheild Reseller Page'
          elif "Forbidden   Forbidden   You don't have per" in output:
              stat='ACTIVE but private'
          elif "domain registered through ooredoo" in output:
              stat='ooredoo  website or its  PARKING PAGE'
          elif "TAG-Domains AGIP" in output:
              stat='AGIP website or its  PARKING PAGE'
          elif "QEPT" in output:
              stat='QEPT website or its  PARKING PAGE'
          else:
              stat='ACTIVE-operational'
          df = df.append({'Url' : line.strip(), 'Content' : output,  'wordcount' : len(output), 'httpstatus' : response, 'status': stat}, ignore_index=True)
          print(df)
          line = fp.readline()
          df.to_csv('webstat.csv', encoding='utf-8')
       except:         
           df = df.append({'Url' : line, 'Content' : 'Exception',  'wordcount' : 'Exception', 'httpstatus' : 'Exception', 'status': 'Exception'}, ignore_index=True)
           line=next(fp)
           df.to_csv('webstat.csv', encoding='utf-8')
           pass



