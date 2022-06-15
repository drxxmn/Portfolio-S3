# Learning Outcomes GP
Learning Outcomes for the Group Project
## Table of contents
* [1. Web application](#1-web-application)
* [3. Agile method](#3-agile-method)
* [5. Cultural differences and ethics](#5-cultural-differences-and-ethics)
* [6. Requirements and design](#6-requirements-and-design)
* [7. Business processes](#7-business-processes)
---

### 1. Web-application
#### User Friendly:
I did research on what the best way to display the temperature and humidity is for our application, and thus looked into all sorts of heatmaps and sliders. I go into this more in my [Individual Project](https://github.com/drxxmn/Portfolio-S3/tree/main/IP) and in the [full-stack](#full-stack) section

#### Full-stack:
##### Heatmap:
I was responsible for more front-end aspects for our project, which included the heatmap and the way of displaying data to the fron-end user.
First, I did research into what type of map would best suit our application.
I will give a brief summary of why I didn't end up choosing each of the sources and how we ended up using [Highcharts](https://www.highcharts.com/demo/heatmap-canvas).
Here's a list of a few sources I looked into:

* [disruptive technologies](https://developer.disruptive-technologies.com/docs/other/application-notes/generating-a-room-temperature-heatmap)
    * although it would be perfect for our application, it seemed to be a paid and closed up service
* [mazemap](https://www.mazemap.com/solutions/heat-maps)
    * it had the same issues as disruptive technologies and seemed too complex for what we wanted with our project
* [skypack](https://www.skypack.dev/view/bootstrap-vue-heatmap)
    * due to the way our sensors work it would be better to have a seamless transition between them on a map/ image, and this heatmap is locked behind blocks.
* [v-charts](https://github.com/ElemeFE/v-charts)
    * the documentation is quite hard to parse and it seemed a bit too overdone and complicated for our project, it's also more suited for world maps rather than our office map.
* [plotly](https://plotly.com/javascript/)
    * the same issue we had with other heatmaps, it mainly is suited for world maps rather than custom image maps, it also doesn't quite accurately track points after further testing with some other maps.
* [mapbox](https://www.mapbox.com/)
    * as with plotly, it is mainly suited for geography, wolrdmaps and navigation rather than small office buildings with custom images.


Thus, we ended up settling on [Highcharts](https://www.highcharts.com/demo/heatmap-canvas). At first, we had some issues with highcharts because we don't have sensors on each corner of the room, we can't calculate the temperature with this heatmap. But because other heatmaps didn't end up working we instead wrote something in the back-end to calculate simple corner temperatures based on the sensors nearby, so we wouldn't have gaps in the heatmap.
<!-- ![image](https://user-images.githubusercontent.com/69758492/171030577-f9ba5e30-a549-40d3-ae25-1be14bdf563b.png) -->
| <img src="https://user-images.githubusercontent.com/69758492/171030577-f9ba5e30-a549-40d3-ae25-1be14bdf563b.png" width="1000" height="500" /> |
| :--: |
| _Broken heatmap without corner sensors_ |

Using the mock sensors we calculate we can get a nice full view of the room with all the temperature data.

<!-- ![image](https://user-images.githubusercontent.com/69758492/171031126-1ec7b81c-6f71-47d9-8cb8-6b2331d44cd8.png) -->
| <img src="https://user-images.githubusercontent.com/69758492/171031126-1ec7b81c-6f71-47d9-8cb8-6b2331d44cd8.png" width="1000" height="500" /> |
| :--: |
| _Fixed heatmap using mock sensors_ |

##### Sliders & Date-time:
After finishing up the heatmap, I worked on a date-time field input where you can input the current date and a slider with which you can slide through the data in a day and see how the heatmap changes.

For the date time input field we used a default date time selector, I did make sure to set the limit of the date time to today, so you wouldn't be able to click to the future to prevent issues regarding sensor data.
```javascript
let todayDate = new Date().toISOString().slice(0, 10);
document.getElementById('dateNow').value = todayDate;
document.getElementById('dateNow').max = todayDate;
```

As for the slider, it was a bit more complicated to find out the best way to set that up, but we ended up choosing the default bootstrap slider and continued to improve and develop it.

With the bootstrap slider we first set it up in seconds in a day, this way we can best link it with our sensor data which updates in 30 second intervals.

But because we were using seconds and had to display things in HH:mm:ss format, I had written some code to convert seconds into HH:mm:ss.

```javascript
function convertHMS(value) {
    const sec = parseInt(value, 10); // convert value to number if it's string
    let hours = Math.floor(sec / 3600); // get hours
    let minutes = Math.floor((sec - (hours * 3600)) / 60); // get minutes
    let seconds = sec - (hours * 3600) - (minutes * 60); //  get seconds

    // add 0 if value < 10; Example: 2 => 02
    if (hours < 10) { hours = "0" + hours; }
    if (minutes < 10) { minutes = "0" + minutes; }
    if (seconds < 10) { seconds = "0" + seconds; }
    return hours + ':' + minutes + ':' + seconds; // Return is HH : MM : SS
}
```

But because the sensor data only comes in once every 30 seconds I ran into the issue where I can't retrieve data for anything that isn't 00 or 30 seconds, so I wrote an extra snippet of code in between the HH:mm:ss converter to round everything down to the lower 30-second intervals.

```javascript
function convertHMS(value) {

    //rounded down by 30 seconds, synced with data update speed
    if (seconds < 30) {
        seconds = 0;
    }
    else {
        seconds = 30;
    }

}
```

The slider itself also had a major issue where you would be able to slide to the future, but because we didn't have any data in the future, this would make the user experience worse. Thus, I wrote some code to limit it to the current time.

```javascript
//set slider limit according to time NOW
function setTimeLimit() {
    let checkDate = new Date().toISOString().slice(0, 10);
    if (document.getElementById("slider").value > seconds && document.getElementById("dateNow").value == checkDate) {
        document.getElementById("slider").value = seconds
        SelectValue.innerHTML = convertHMS(seconds);
        document.getElementById("error").style.visibility = "visible";
        document.getElementById("errormsg").innerHTML = "de tijd kan niet in de toekomst liggen!";
    }
    else {
        document.getElementById("error").style.visibility = "hidden";

    }
}
```

### 3. Agile method
In our group project we work using the Agile method and we chose the Scrum agile methodology. 

#### ***What is Agile Scrum?***
Agile scrum methodology is a project management system that relies on incremental development. Each iteration consists of two- to four-week sprints, where the goal of each sprint is to build the most important features first and come out with a potentially deliverable product. More features are built into the product in subsequent sprints and are adjusted based on stakeholder and customer feedback between sprints.

Whereas other project management methods emphasize building an entire product in one operation from start to finish, agile scrum methodology focuses on delivering several iterations of a product to provide stakeholders with the highest business value in the least amount of time.

Agile scrum methodology has several benefits. First, it encourages products to be built faster, since each set of goals must be completed within each sprint’s time frame. It also requires frequent planning and goal setting, which helps the scrum team focus on the current sprint’s objectives and increase productivity. 

#### ***Why Agile Scrum?***
We had to select an Agile work methodology for this semester and we were free to choose whichever we wanted. Our group ended up picking Scrum for multiple reasons. Most of us have been working together for the last 2 semesters and have always adapted a Scrum style workflow, because we're familiar with how it works and what must be done we can also improve upon the workflow with every sprint.

Because we focus on delivering several iterations of a product to provide stakeholders throughout the entire semester, we were able to identify what exactly the stakeholder wanted from us and discuss what must be done.

At first when we just started this semester we lacked the ability to thoroughly question the stakeholder on what must be made and what we had available and ended up making a mock application of something that the stakeholder wouldn't be able to use or even want to use. But because we managed to communicate better and identify the issue we were able to quickly scrap everything and start again from scratch, this time knowing what needed to be done and communicating more frequently.

#### ***The difference between Kanban and Scrum***
Kanban and Scrum are project management methodologies that complete project tasks in small increments and emphasize continuous improvement. But the processes they use to achieve those ends are different. While Kanban is centered around visualizing tasks and continuous flow, Scrum is more about implementing timelines for each delivery cycle and assigning set roles.
In Kanban you work in shorter cycles and focus on finishing your tasks while Scrum floats a bit more.

Kanban works well when used alongside Scrum or any other Agile method. Basically, Kanban can be applied to visualize and improve the flow of work, regardless of the methodology being used to do the work.

Scrum is an iterative, incremental work method that provides a highly prescriptive way in which work gets completed. Scrum teams have defined processes, roles, ceremonies and artifacts.

Kanban can be customized to fit the processes and work systems your team and / or company already has in place. Once a work method has been either adopted or developed based on Agile principles, your team can begin using Agile tools like Kanban boards and project forecasting tools to help manage projects, workflows and processes in a way that works best for everyone.

So it is possible to still integrate it with other workflows, our group didn't exactly work with only Agile Scrum. The stakeholder also slightly helped us push more towards a different workflow because it's also what they use within their company. We did use Kanban a bit because we visualised the work in a similar way on a roadmap.

#### ***What did we do?***

We kick off every day with a daily stand-up and end every day with a stand-down. 
In the stand-up, we discuss what we have to do during the day and which tasks every person will take upon themselves. We also discuss any issues we've run into or any new things we have learned that would benefit the entire group. We also discuss the current state of the project and how we are progressing and if we're still on schedule.

Furthermore, we have a [Notion](https://notion.so) page in which we track all our documents and progress using a Trello board. Everything we have in the [Notion](https://notion.so) workspace: 

* Roadmap
* Meeting Notes
* Backlog
* Documents


We work in an agile process in our group project. Every morning we start with the daily stand-up. Here we will discuss what everyone will be doing for the day and where we currently are in the project (in terms of what is completed and what still needs to be done). We will grab our notion board and start assigning tasks (see picture below). At the end of the day, we will perform the daily stand-down. Here we will discuss how everything went this day. Think about problems that may have occurred throughout the day, or things that just didn't go as planned.   

| <img src="https://user-images.githubusercontent.com/69758492/171034808-c9dccd31-1226-4ca2-afd4-75326829378d.png" width="800" height="320" />| 
| :--: |
| _Image of our [Notion](https://notion.so) Roadmap_ |

At the end of every sprint, we hold a sprint review and a retrospective. The sprint review is held with our product owner, (in this case [ISAAC](https://www.isaac.nl/en/index.html)). With the product owner we discuss our backlog and what is the most important to them, we also gather some feedback to improve our product and what we can work on more. We hold a meeting with the product owner every week in which we deliver our current progress and what we will focus on in the next week.

After the preview we hold a retrospective as a group, we ask each other questions about how the sprint went, what we can improve in the next sprints and what we did well/ badly. If there are any issues, we will immediately discuss and fix it.

| <img src="https://www.flowsphere.ch/wp-content/uploads/2017/02/ScrumFramework_17x11-1024x663.png" width="800" height="500" />| 
| :--: |
| _The scrum workflow we apply in our project_ |

#### ***Things we can improve upon***
There's many things that we could also improve upon in the group, one of which is working with the Agile Scrum method. We didn't keep up the backlog well enough and ended up not really adding any "definition of done" requirements.
As a group we had adapted the method in a slightly different way, albeit maybe not as effective. In the future we definitely know better how to start and keep up a project and to set the right requirements.

#### ***My opinion on our workflow***
Personally I really liked our workflow and how we handled things. Using the Agile Scrum method we could always tell what the other members were doing at any given moment, we can also see what still needs to be done and pick up those tasks.

Because we do a review every sprint, we can organize what needs to be done and what we can show to the stakeholder so they can give their opinions too. This allows us to make sure we're making the right thing and not just making anything that we deen to be fun or interesting to do.

All in all it was a very nice and enjoyable workflow, it's very flexible and you can adapt it to your own group in different ways.

### 5. Cultural differences and ethics
Cultural:
---
**What is culture?** <br>
Culture is the characteristics and knowledge of a particular group of people, encompassing language, religion, food, social habits, arts and music. This applies to ethnicities, countries, but also in the workspace.

**Can you give examples of cultural differences that you have experienced in your study or life? How do you explain these differences?**   <br>
As an Asian born in The Netherlands, I've definitely experienced cultural differences in my life. Because you look different to other people the way you'll be treated will always be somewhat different, there'll always be some prejudice, whether good or bad.

Your mindset will also be different depending on the way you've been raised, be it a different countries' culture or a different religion. But having differences in culture is what makes things interesting, as long as it's nothing negative or if there's discrimination.

**What is your culture?** <br>
The culture I've grown up with is the Dutch culture, Chinese culture and I would even say internet culture. Although the different cultures can sometimes clash, it is what makes me, *me*.

**What did you do to improve the group communications with respect to cultural differences?** <br>
We all have similar cultures, so it wasn't really needed for us to change and adapt to each other. But we do often play some games or eat out with the group members to deepen our ties.

Ethics:
---
**What is ethics in software engineering?** <br>
Ethics is a set of moral principles that govern the behavior of a person or the conduction of an activity. Ethics are used to measure the righteousness of an action, acting as criteria for judging whether something is right or not. In software engineering, ethics sets the values a software engineer should stick to when creating software and in the work environment. 

**Why is ethics important in software engineering?** <br>
There are many perspectives on ethics in software engineering. One of them is the development of a product. The impact your software can have on the end-user and what can be done with it has to be thought about in an ethical way. The ethical values adhered to during the production of a product define the workability and comfortability of the environment. A lack of proper ethical values or a lack of enforcing them will eventually create a working environment where discrimination, bullying, favoritism among other things are allowed. For this reason, it is important for proper, unanimously agreed upon ethical values to be present.
The ethical values the product adheres to at it’s core define how an application could impact its users. An application built with strong ethical values at its core should result in a product that will affect users in a positive manner, whereas one that lacks this core may result in the opposite reaction. For this reason, it is important to consider how users could be negatively impacted by something, and what can be done to minimize (or negate) this reaction.

**An example of how important ethics is in software engineering** <br>
In the world currently most multimillion dollar companies have questionable ethics and only care about profit, our data is never truly secure.
I personally wanted to dig into it more and did a research on a specific social media platform. [Here](https://github.com/drxxmn/Portfolio-S3/tree/main/IP/Research/Security%20online) is a link to my research.

**What do you have to do as a software engineer to address ethical aspects in your work?** <br>
Within the software engineering space, a number of widely used ethical guidelines can be found on the internet. One such guideline is the [ACM Software Engineering Code of Ethics](https://ethics.acm.org/code-of-ethics/software-engineering-code/), which states that software engineers should strife to analyze, design, develop, test and maintain software in a truthful, upfront and professional manner for the better of public health, safety and welfare of humanity, according to the following eight principles: 
1. PUBLIC – Software engineers shall act consistently with the public interest.
2. CLIENT AND EMPLOYER – Software engineers shall act in a manner that is in the best interests of their client and employer, consistent with the public interest.
3. PRODUCT – Software engineers shall ensure that their products and related modifications meet the highest professional standards possible.
4. JUDGMENT – Software engineers shall maintain integrity and independence in their professional judgment.
5. MANAGEMENT – Software engineering managers and leaders shall subscribe to and promote an ethical approach to the management of software development and      maintenance.
6. PROFESSION – Software engineers shall advance the integrity and reputation of the profession consistent with the public interest.
7. COLLEAGUES – Software engineers shall be fair to and supportive of their colleagues.
8. SELF – Software engineers shall participate in lifelong learning regarding the practice of their profession and shall promote an ethical approach to the practice of the profession.

**How do you know that your ethical considerations match with those of other software engineers?** <br>
In order to verify all software engineers’ ethical values match up, it is important for all parties involved to converse about the topic. Allowing everyone to share their ethical values and boundaries with one another makes it possible to create a working environment in which all parties involved can work comfortably.

**Which ethical aspects play a role in your project?** <br>
One of the ethical aspects in our group project that we faced was the licensing of third-party libraries. As our project lives in a gray area between commercial and non-profit (the organization is a business, while the application is strictly for internal use without any direct link to generating profit), we contemplated whether purchasing a license for these libraries would be necessary.

**Do you foresee ethical conflicts caused by your software? What kind of?** <br>
I do not expect any ethical conflicts within our project.

**Can you do something to avoid or minimize these conflicts?** <br>
Communicating regularly with one another is key in preventing (or solving) conflicts in a professional setting. Expressing one’s concerns and problems spreads awareness to all parties involved and allows them to seek out a solution together. It also minimizes the amount of misunderstandings that could potentially happen.

### Game
We (Me, Rens, Maikel and Rick) played the game in the Canvas course. These are the scores we achieved at the end of the game:

|    Name    |Score|
|------------|-----|
| **Maikel** | 121 |
| **Rick**   | 114 |
| **Liang**  | 113 |
| **Rens**   | 112 |

I think the game was not the most useful for me personally to understand cultural differences more, but I definitely learned some small things about different cultures that I can keep in mind for the future. For example how in some Asian countries it is acceptable to just hang up the phone out of nowhere, or how different countries prefer their interviews. Using this information I can improve my communication and relations with future colleagues and friends.

### 6. Requirements and design
To better our product, we try to get as much feedback as possible from our stakeholder. We try our best to meet up every week, so we can discuss what we have done and what still needs to be done/ done better.

At the beginning of our project, we created a list of user stories, which would act as our requirements. Each user story would be dissected into smaller tasks, which would then get implemented. Later on we created a sort of "definition of done" for each task, so the group could quickly see what needed to be done and what has already been done, including the progress of each task.
As I mentioned [earlier](#things-we-can-improve-upon) we did not do the definition of done in the conventional way. Instead we made it a sort of check list of things that needed to be completed and work for us to deem it finished.

Discussions with the stakeholder started off a bit weak, but we quickly realized that we didn't have a good direction to work towards and started communicating much better with the stakeholder. 

In the beginning we made a program for the workers at ISAAC, but the program is supposed to be made for the upper management so they can see that the temperature within the building is not ideal for working, and that they need to get it fixed. 

We bounced a lot of ideas with the stakeholder and ended up making a heatmap system that would accomodate everyone in the building.

As for design I went into it in the [heatmap](#heatmap) and [slider](#sliders--date-time) section.

### 7. Business processes
For our group project, we don't really have a business project we can analyze. So we discussed this problem with our teacher (Marc) and came to the conclusion that we should analyze any relevant business project. So we as a group analyzed the business process of order picking. Web shops have huge warehouses filled with products that can be ordered and shipped. Advanced algorithm's create picking lists for employees to pick. We visualized this process for multiple reasons.

We visualized the process in Engage Process Modeler. The Engage Process Modeler makes it possible to visualize business processes. These processes can be made as advanced as one can imagine. We choose to keep it simple and clear. As a group, we set down and discussed the process until we all had a clear view of the process and everyone shared the same opinion. See picture below for the visualized process.
| ![Picking](https://user-images.githubusercontent.com/58734636/169815706-b2c2f944-5276-49b0-8d67-84d7c27e853d.png)|
| :--: |
| _Order-picking business process_ |

A business process is a series of steps performed by a group of stakeholders to achieve a concrete goal. Each step in a business process denotes a task that is assigned to a participant. It is the fundamental building block for several related ideas such as business process management, process automation, etc.

#### ***What can I do with a business process in the future?***
- Identify what tasks are important to your larger business goals
- You can improve efficiency
- You have a better insight of the user, and thus can understand the process better.
- It prevents chaos in the process and streamlines things.
- Only have things that really matter in your project.

Next time I would have to work in a group project I could start a conversation with my teammates to see if we can make a business process in the beginning to really understand the stakeholder and users.