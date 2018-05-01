from bs4 import BeautifulSoup
import pandas as pd
import requests
import xml.etree.ElementTree as ET
import smtplib
from email.MIMEMultipart import MIMEMultipart
from email.MIMEText import MIMEText
import sys

# Account I monitor
michalis = ["GOGROOPIE", "GOGROOPIE_I", "GOGROOPIEVIP", "HOF", "SELFRIDGES"]
emmet = [""]
rimantas = [""]

# Email config
fromaddr = "TODO"
toaddr = "TODO"
msg = MIMEMultipart()
msg['From'] = fromaddr
msg['To'] = toaddr
msg['Subject'] = "SSL Expiry Notice"
server = smtplib.SMTP('smtp.gmail.com', 587)
my_pass = "TODO"


url = "http://platformsupport.emv2.com/l2support/get_ssl_status.php"
page = requests.get(url)
soup = BeautifulSoup(page.content, "lxml")

# The followings are required since you have two tbodies.
for body in soup("tbody"):
    body.unwrap()

df = pd.read_html(str(soup), header=0, converters={'client_id': str, 'days': int})[0]
required = df.loc[df['client_name'].isin(michalis)]

#Days to notify
filtered = required.loc[required['days'] < 25]

body = "The followings will expire in less than 25 Days: \n\n\n"
body += "POD\t Subdomain\t Client\t ExpiryDate \n"

for index, row in filtered.iterrows():
        body += '\t'.join([row['POD'], row['sub_domain'], row['client_name'], str(row['days'])]) + "\n"

print (body)

msg.attach(MIMEText(body, 'plain'))
server.starttls()
server.login(fromaddr, my_pass)
text = msg.as_string()
server.sendmail(fromaddr, toaddr, text)
server.quit()

