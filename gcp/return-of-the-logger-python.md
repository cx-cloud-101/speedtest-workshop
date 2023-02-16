[Home](../) > [GCP](index) > Return of the Logger
=======================================================
_The GCP Speedtest Logger is an application that runs a connection speedtest to speedtest.net for a device and publishes the results to [gcp-speedtest-api](love-the-smell-of-http)._ 
You should be able to run the application with `user` and `device` as arguments such that the application can be run on different devices.
See the reference implementation [here](https://github.com/cx-cloud-101/gcp-speedtest-logger).
See "[Love the smell of HTTP in the morning](love-the-smell-of-http)" for a description of the API that the Logger will publish results to.

Implementation
------------------------
You can implement the application in any language you prefer, though you should consider if there are speedtest.net library implementations for 
that language and whether the results you get from the library comply with the GCP Speedtest API. We recommend using Python as it have such a library:

* [speedtest-cli](https://github.com/sivel/speedtest-cli)


Versioning the code
-------------------
You'll probably want to create a new repo named gcp-speedtest-logger so you can version your logger.

Installing dependencies
-----------------------
You'll need to specify the dependencies for your application. Create a `requirements.txt` file in the root of your repository. For this application we 
need the speedtest library linked above and a library to send requests to the API. We'll use the `requests` library for this. Your 
`requirements.txt` file should look like this:

```bash
speedtest-cli
requests
```

Install these to your Python environment running: 

```bash
pip install -r requirements.txt
```

Implementing the logger
-----------------------
Start by creating a new file called `logger.py` in the root of your repository. Make a new function called `main` and call it from the entry point:

```python
import speedtest
import requests
def main():

    # Main user (yourself)
    user = 'sfl' # Replace with your username
    api_url = "https://speedtest-api-raxcsdlzxq-ew.a.run.app/speedtest" # Replace with your API URL


if __name__ == '__main__':
    main()
```

### Do a speed test
The first thing the logger should do is to run a speedtest. We'll use the speedtest library we installed earlier. Make a new function to
run the speedtest:

```python
def speed_test():

    # Do the speed test
    s = sp.Speedtest()
    s.download()
    s.upload()

    # Get the results
    result = s.results.dict()
    return result
```

### Build the request body
The dictionary containing the results from this function should have everything we need to send to the API. Make a new function to build the 
request body from the results.

```python
def build_body(res, user, device):
    body = { 
        "user": user,
        "device": device,
        "timestamp": ,
        "data": {
            "speeds": {
            },
            "client": {
            },
            "server": {
            }
        }
    }
    return body
```

Use the API specification found in [Love the smell of HTTP in the morning](love-the-smell-of-http) to fill in the rest of the body with the correct names.
`datetime` is a built in Python Library for time and date handling that you can use to get the current timestamp.

### Post results to API
Now we have everything we need to send the results to the API. Make a new function to do this:

```python
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    r = requests.post(api_url, json=body, headers=headers)
    
    # Print the status code of the response.
    print(r.status_code)
```

### Putting it all together
Now we have all the functions we need. Call them from the `main` function:

```python
def main():

    # Main user (yourself)
    user = 'sfl' # Replace with your username
    api_url = "https://speedtest-api-raxcsdlzxq-ew.a.run.app/speedtest" # Replace with your API URL

    # Do speed test
    raw_res = speed_test()

    # Format body to API spec
    body = build_body(raw_res, user, 1)

    # Post to API
    post_to_api(body, api_url)
```

Run your speedtest logger application using:

```bash
python logger.py
```

What's next?
------------
When you have a logger capable of sending speedtest results to the API, we can move forward and start [analyzing it all](analyzing-it-all).
