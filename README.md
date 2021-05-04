# issoverhead-api
## Problem: Email me when the International Space Station is right above my location
## Solutions
1. Your latitude and longitude (Google it!) and make a boolean function whether your position is within +5 or -5 degress of the ISS position
```
MY_LAT = 5.262291 # Your latitude
MY_LONG = -12.692162 # Your longitude

def is_overhead():
    """
    Your position is within +5 or -5 degrees of the ISS position
    Create a function T or F whether my position is within 5 degrees
    """
    response = requests.get(url="http://api.open-notify.org/iss-now.json")
    response.raise_for_status()
    data = response.json()

    iss_latitude = float(data["iss_position"]["latitude"])
    iss_longitude = float(data["iss_position"]["longitude"])

    if MY_LAT - 5 <= iss_latitude <= MY_LAT + 5 and MY_LONG - 5 <= iss_longitude <= MY_LONG + 5:
        return True
```
2. Create a boolean function whether your current location is after sunset and before sunrise
```
def is_dark():
    parameters = {
        "lat": MY_LAT,
        "lng": MY_LONG,
        "formatted": 0,
    }

    response = requests.get("https://api.sunrise-sunset.org/json", params=parameters)
    response.raise_for_status()
    data = response.json()
    sunrise = int(data["results"]["sunrise"].split("T")[1].split(":")[0])
    sunset = int(data["results"]["sunset"].split("T")[1].split(":")[0])
    time_now = datetime.now().hour
    if time_now >= sunset or time_now <= sunrise:
        return True

```
3. Run these two functions every 60 seconds if the ISS is near my location and is dark; Then email me to look up the sky
```
while True:
    # Run the code every 60 seconds.
    time.sleep(60)
    # If the ISS is close to my current position and it is currently dark, then send me an email to tell me to look up.
    if is_overhead() and is_dark():
        # ***WARNING***: Make sure you use a dummy email account to test this out!
        my_email = "MY_EMAIL"
        password = "PASSWORD"

        with smtplib.SMTP("YOUR EMAIL PROVIDERS SMTP ADDRESS", port=587) as connection:
            connection.starttls()
            connection.login(user=my_email, password=password)

        # Write messages
        connection.sendmail(
            from_addr=my_email,
            to_addrs="TO_SEND_EMAIL",
            msg=f"Subject:Look up!\n\nThe Internationa Space Station is right above you!"
        )
```
