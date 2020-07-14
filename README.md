# Selenium, beautiful soup and regex to download lecture videos from University of London :smile:
Currently I am studying the Msc of Data Science from University of London and we can download the lecture videos through student portal for offline learning. This article will share how to use python to automatically download all the learning videos from UoL. Full code will be provided at the end.


1. Use Selenium to login the student portal 
To scrape information from website requiring login, we can use requests.session().postor Selenium. 
In this sharing I will use Selenium to help me login the student portal because it is easy and convenient. 
Go to https://learn.london.ac.uk/login/index.php and we want Selenium to input the Username and Password field and click the Log in button for us.

![p1](https://cdn-images-1.medium.com/max/1800/1*rdwb3NiJw1rghHIzEfEkbg.png) <br/>

First check the elements of the webpage and we can see the element_by_id are username, password and loginbtn respectively. Therefore we can use the follow code to get the job done.<br/>


<script src="https://gist.github.com/fiyero/c7dfb469d3b45663e210686c80bd66fa.js"></script>
