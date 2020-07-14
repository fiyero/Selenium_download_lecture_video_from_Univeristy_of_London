# Selenium, beautiful soup and regex to download lecture videos from University of London :smile:
## https://medium.com/@patrickhk/selenium-beautiful-soup-and-regex-to-download-lecture-videos-from-university-of-london-42e7f4011f56

Currently I am studying the Msc of Data Science from University of London and we can download the lecture videos through student portal for offline learning. This article will share how to use python to automatically download all the learning videos from UoL. Full code will be provided at the end.


## 1. Use Selenium to login the student portal 
To scrape information from website requiring login, we can use requests.session().postor Selenium. 
In this sharing I will use Selenium to help me login the student portal because it is easy and convenient. 
Go to https://learn.london.ac.uk/login/index.php and we want Selenium to input the Username and Password field and click the Log in button for us.

![p1](https://cdn-images-1.medium.com/max/1800/1*rdwb3NiJw1rghHIzEfEkbg.png) <br/>

First check the elements of the webpage and we can see the element_by_id are username, password and loginbtn respectively. Therefore we can use the follow code to get the job done.<br/>

```import requests
from bs4 import BeautifulSoup
import re
from selenium import webdriver

driver=webdriver.Chrome('D:/Google/Learning/chromedriver.exe')
driver.get("https://learn.london.ac.uk/login/index.php")
username=driver.find_element_by_id("username")
password=driver.find_element_by_id("password")
username.send_keys("USER_NAME")
password.send_keys("PW")
driver.find_element_by_id("loginbtn").click() 
``` 

By running the above code in jupyter notebook, a chrome browser will be created and login the student portal automatically. <br/>

## 2. Use beautiful soup to scrape and regex to get the video link 
We have different courses in one semester and each course contains several topics, each topic have several mini lectures. Therefore my goal is to scrape the link of each mini lecture and extract the link for the lecture video then download it. <br/>

![p2](https://cdn-images-1.medium.com/max/1200/1*Fkt2I-CnHBxEWTELnG0ZcQ.png) <br/>

First we get the html content from selenium and process through beautiful soup. Play around with the soup object you will discover the link of each mini lecture is under the progressEventInfo class, to be accurate, in the a['href'], therefore we create a list scraping all the link for the material using the below code<br/>

```
sub_topic=driver.page_source
sub_topic_content=BeautifulSoup(sub_topic,'lxml')

subcontent_link= [iii.a['href'] for iii in sub_topic_content.find_all(class_='progressEventInfo')]
subcontent_title= [i.a.text for i in sub_topic_content.find_all(class_='progressEventInfo')]
```

![p3](https://cdn-images-1.medium.com/max/1200/1*Mlg_HlXi_N-SCbij1FM8Lg.png) <br/>
So now we have a list called subcontent_linkfor all the course materials. We set a condition to only scrape the link involving lecture video, which is if the title containing keyword lecture summary and webinar<br/>

```
for k,v in zip (subcontent_title,subcontent_link):
    if 'lecture' in k or 'summary' in k or 'webinar' in k:
   ```
   
Then we feed these link and get their content through Selenium then process in soup object. However the link for the lecture video is hidden through the Javascript video player and cannot be scraped directly.<br/>

![p4](https://cdn-images-1.medium.com/max/1800/1*cgnqYbakaOntx3on_PNgLA.png) <br/>
If we manually click the download play button in the Javascript video player, we can discover the link is always with this pattern.<br/>

```http://dr3vr6XXXXXXX.cloudfront.net/videostore/dsm020/mp4/dsm020_topic02_intro01_720.mp4```
While in the webpage we can get similar link in this form<br/>
```dr3vr6XXXXXXX.cloudfront.net/videostore/dsm020/rss/dsm020_topic02_intro01.rss```

(I masked the first part dr3vr6XXXXXXX as I am not sure if this is some sort of authentication and unique to user)
Great! We can apply regex to get the semi-link and edit the link through replace rss with mp4 and add _1080p then we will obtain the final video link. <br/>

```
pattern=r'(dr3vr6XXXXXXX)(.+)(\.rss)'  
#rmb the base is unique and should change to yr own 

#given condition, only get video of certain title
for k,v in zip (subcontent_title,subcontent_link):
    if 'lecture' in k or 'summary' in k or 'webinar' in k:
        #if 'lecture' or 'summary' or 'webinar' in k: is wrong
        driver.get(str(v))
        #InvalidArgumentException: Message: invalid argument 
        #need to string the v
        
        lecture_content=driver.page_source
        lecture_content=BeautifulSoup(lecture_content,'lxml')
        
        video_link=re.search(pattern,lecture_content.text).group(0)
        #need to lecture_context.text becoz re can only use in string
        
        #clean and edit the link
        video_link=video_link.replace('rss','mp4')
        video_link='http://'+video_link[:-4]+'_1080.mp4'
        
        #download the video
        lecture_video = requests.get(video_link,headers=get_random_ua()) 
        with open(f'{k}.mp4','wb') as f:
            f.write(lecture_video.content)
            lecture_video.close() 
        print(f'{k} is downloaded')
        
print('Job is done!')
```
Then we just use request to download the video and rename it by using the title of the lecture. 

## Fullcode
Full code are availabe in jupyter notebook format and html for easier reading

## Why not use request.POST?
Honestly I did try to use request.session().POST instead of Selenium. I spend hours and read several articles but failed. I can successfully login the student portal but encounter redirects error and cannot scrape the webpage content. I trying adjusting the session.max_redirects or allow_redirects=False but still failed to to the job. So if anyone know how to do it, welcome to share!

-------------------------------------------------------------------------------------------------------------------------------------
### More about me
[[💻My Linkedin]](https://www.linkedin.com/in/patrick0123/)<br/>
[[:pencil:My Medium]](https://medium.com/@patrickhk)<br/>
[[:house_with_garden:My Website]](https://www.fiyeroleung.com/)<br/>
[[:space_invader:	My Github]](https://github.com/fiyero)<br/>
