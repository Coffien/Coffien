from pymino import Client
from requests import get
from bs4 import BeautifulSoup
from json import loads, dumps
from time import sleep

client = Client()

def url_mail(email: str):
    email = email.split("@")
    while True:
        try:
            url = f"https://www.1secmail.com/api/v1/?action=getMessages&login={email[0]}&domain={email[1]}"
            request = loads(get(url).text)
            id = request[0]["id"]
            url = f"https://www.1secmail.com/api/v1/?action=readMessage&login={email[0]}&domain={email[1]}&id={id}"
            request = get(url).text
            soup = BeautifulSoup(request, features="html.parser")
            url = soup.find_all("a")
            return url[0].get("href")[2:-2].replace("\\", "")
        except:
            pass

def register():
    url = "https://www.1secmail.com/api/v1/?action=genRandomMailbox"
    email =  get(url).json()[0]

    client.request_security_validation(
        email = email
    )

    print(url_mail(email))

    name, password =  "kookie", "987654321",

    client.register(
        email,
        password,
        name,
        input("code : ")
    )
    
    account = dumps({
        "email": email,
        "password": password,
        "device": client.device_id
    })
    with open("acc.json", "a") as file:
        file.write(f"{account},\n")
        file.close()

for x in range(5): register()
