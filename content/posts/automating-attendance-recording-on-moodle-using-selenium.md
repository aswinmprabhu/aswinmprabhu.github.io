---
title: "Automating Attendance Recording on Moodle Using Selenium"
date: 2021-06-21T13:09:03+05:30
featured_image: "/images/moodle.png"
toc: false
tags:
  - python
  - automation
---

When classes moved online due to the pandemic, we were free to mute the tab and doze off or not attend at all when they turned out to be boring. Although we could get away without attending online sessions, we still had to record our attendance every hour on an online learning management system called [Moodle](https://moodle.org/) to maintain our dreaded minimum attendance requirement. My ever so forgetful and pandemic taxed brain couldn't keep up with this repetitive task during the initial days of online classes. So I decided to do something about it and build an automation tool to take care of this mundane task for me.

![Moodle](/images/moodle.png)

"Automate yourself out of a job" is a quote that I have heard repetedly while working as an SRE. If handing over monotonous jobs to machines is what any good engineer is supposed to do, then where better to start than in an engineering college!

Since moodle is web based and runs in a browser, the tool was built using [Selenium](https://www.selenium.dev/), a browser automation framework. Think of selenium as a robot that you can program to do tasks on a web browser that a human would normally do. These tasks can range from visiting a particular URL to filling in forms and submitting them. For instance, you can tell selenium which button click and it will click for you. 

The two major components required to use selenium are the client library and the browser driver. Selenium can be programmed in multiple languages for which client libraries are available. The program written using the library will talk to the browser driver. The browser driver is a program that can control the web browser and perform automation tasks on it. The browser is a puppet and the driver is the puppet master.

## Basic tutorial

I used python to build the tool. The client library for python can be installed using pip via the command

```bash
pip install selenium
```

Browser drivers for Google Chrome can be found [here](http://chromedriver.chromium.org/downloads). Place the driver file in the same directory as the program file. Let's call the program `attendance.py`.

There might be multiple ways for you to achieve the same task using browser automation. Let's try to express the pseudo code for one of the ways in which you can program it to record your attendance on moodle every hour.

```
Visit the moodle login page (https://moodle.mec.ac.in)
Fill in the email and password in the respective fields and click the login button
After every 20 mins:
    For every URL in the list of attendance page URLs:
        Visit the URL
        Check is `Submit Attendance` button is present on the page
        If button is present:
            Click the `Submit Attendance` button
            On the recording page, click the radio button corresponding to "Present"
            Click the `Submit` button
```

For using selenium to automate tasks on web elements such as form fields and buttons, we need a way to specify the element to be used. Selenium provides the following methods on the driver object to find  web elements on the page,

- find_element_by_id
- find_element_by_name
- find_element_by_xpath

Consider the following HTML page,

```html
<html>
 <body>
  <form id="loginForm">
   <input name="username" type="text" />
   <input name="password" type="password" />
   <input name="continue" type="submit" value="Login" />
   <input name="continue" type="button" value="Clear" />
  </form>
</body>
</html>
```

```python
# returns the form element
login_form = driver.find_element_by_id('loginForm')
# returns the usename field in the form
username = driver.find_element_by_name('username')
# returns the password field in the form
password = driver.find_element_by_name('password')
# returns the "Login" button as it appears before the "Clear" button
continue = driver.find_element_by_name('continue')
```

To find multiple elements, the name and xpath methods can be modified as find_elements_by_name and find_elements_by_xpath respectively. These will return a list of elements. XPATH is a powerful language used for locating elements in XML and HTML. XPATH opens up a wide range of possibilities such as locating the third button on a page. The language is a bit obscure to learn, but there is a way around this hinderance. You can copy the unique XPATH to any element on a page by inspecting the element using browser developer tools and then right clicking on it to select the option "Copy XPATH". XPATH of the login form element can be "/html/body/form[1]" (first form inside the body element) or "//form[@id='loginForm']" (form with the given id).

Now let's convert the pseudo code into a real python program.

In `attendance.py`, 

```python
# Import webdriver module from selenium
from selenium import webdriver

# Import time module for waiting 20 mins
import time

# Create new driver object (change the file name based on your OS)
driver = webdriver.Chrome("./chromedriver.exe")

# Visit the moodle login page and fill in login details
driver.get("http://moodle.mec.ac.in")

# Define Moodle email and password
moodle_mail = "<your moodle mail>"
moodle_pass = "<your moodle password>"

# Find the field element and send the input keys to the element
username_ip = driver.find_element_by_name("username")
username_ip.send_keys(moodle_mail)
pass_ip = driver.find_element_by_name("password")
pass_ip.send_keys(moodle_pass)

# Find the login button using XPATH and click it
login_btn = driver.find_element_by_xpath('//*[@id="login"]/div[4]/input')
login_btn.click()

# Define the list of attendance page URLs
attendance_page_urls = [
    "http://moodle.mec.ac.in/mod/attendance/view.php?id=9022",
    "http://moodle.mec.ac.in/mod/attendance/view.php?id=9002",
    "http://moodle.mec.ac.in/mod/attendance/view.php?id=8961",
]

# Start infinite loop
while 1:
    # For every URL visit the page
    for url in attendance_page_urls:
        driver.get(url)

        # Get the list of all "Submit attendance" buttons on the page using XPATH
        submit_attendance_btns = driver.find_elements_by_xpath(
            "//*[contains(text(), 'Submit attendance')]"
        )

        # If the list is empty, do nothing and continue
        if len(submit_attendance_btns) == 0:
            continue

        # Else visit the link (HTML "href" attribute) specified by the first button
        submit_attendance_btn = submit_attendance_btns[0]
        att_link = submit_attendance_btn.get_attribute("href")
        driver.get(att_link)

        # Find the fist attendance status radio button, ie, the one corresponding to "Present"
        status_btn = driver.find_elements_by_xpath("//input[@type='radio']")[0]

        # Click the status button and then the submit button
        status_btn.click()
        driver.find_element_by_id("id_submitbutton").click()

    # Sleep for 20 mins before trying again
    time.sleep(1 * 60)
```
- `driver.get(url)` is used to navigate the browser to the specified URL
- `send_keys()` method is used to type into an input field 
- `click()` method is used to click on an element 
- `get_attribute()` method returns the value of the specified HTML attribute of an element

## Using the off-the-shelf script

If you have no interest in learning about browser automation and just want to use the damn script to put an end to manual attendance recording, then this section is for you!

The scripts can be found on GitHub at https://github.com/aswinmprabhu/auto-attendance.

### For individuals

The file [individual.py](https://github.com/aswinmprabhu/auto-attendance/blob/master/individual.py) contains the script for automatic attendance marking for one individual as shown in the above tutorial. All configuration is to be done within that single file.

To run the script,

1. Clone the repository using the git command `git clone https://github.com/aswinmprabhu/auto-attendance.git`
2. Ensure that python3 and pip3 are installed
3. Download and place the chromedriver file in the repository folder
4. Edit the individual.py file
    * Modify the `moodle_mail` and `moodle_pass` variables with your credentials
    * Change the chromedriver file location and name if necessary (OS dependent)
    * Edit the `attendance_page_urls` varible with the URLs to all the attendance pages of your subjects
5. Inside the repository folder, execute the following commands
```bash
pip install selenium
python3 ./individual.py
```
(Try using pip3 is pip command does not work)

### For groups of individuals

The file [attendance.py](https://github.com/aswinmprabhu/auto-attendance/blob/master/attendance.py) contains the script for automatic attendance recording for a group of individuals. A separate config file called `config.json` needs to be created for this to work.

To run the script,

1. Clone the repository using the git command `git clone https://github.com/aswinmprabhu/auto-attendance.git`
2. Ensure that python3 and pip3 are installed
3. Download and place the chromedriver file in the repository folder
4. Create a config.json file inside the repository folder. A sample file is given below.

```javascript
{
	"driver": "./chromedriver.exe",
	"interval": 20,
	"users": [
		{
			"name": "user1",
			"email": "email1@mec.ac.in",
			"password": "pass1",
			"courses": [
				"http://moodle.mec.ac.in/mod/attendance/view.php?id=9022",
				"http://moodle.mec.ac.in/mod/attendance/view.php?id=9002",
			]
		},
		{
			"name": "user2",
			"email": "email2@gmail.com",
			"password": "pass2",
			"courses": [
				"http://moodle.mec.ac.in/mod/attendance/view.php?id=9027",
				"http://moodle.mec.ac.in/mod/attendance/view.php?id=9129",
			]
		}
	]
}
```
5. Make the necessary edits to config.json. Change the driver location if necessary and edit the user details. You can add more users by appending to the `users` array in the config.
6. Inside the repository folder, execute the following commands

```bash
pip install selenium
python3 ./attendance.py --config ./config.json
```
(Try using pip3 is pip command does not work)

**Happy Hacking !!**