# PersonalAssistant
A personal assistant to help me recollect things, remember events, summarize various events- small and big.

Personal AI Assistant
Red -> Data collection completely manual. Either through entry on phone or through shortcut automation
Green -> Possible to get it automatically
Orange -> Partially automatable


As a user I want to be able to:
Must haves:
Be able to set goals effortlessly.
See my progress with these goals. Preferably a dashboard with the ability to easily add new data. Major types of goals I am interested are:
Sleep goals - apple watch
Habit tracker - manual feed through apple watch shortcut
Health goals - Health app and shortcut manual feed
Exercise goals - health app
Food tracking
Learning goals - manual through watch
Be able to manage my calendar and remind me.
Remember things for me.
To-do lists

Good to haves:
Based on my data, personalised recommendations of what I should do, help build a calendar for me directly. – (end goal)
Favourite songs, movies etc
Summaries of things I have read. (point 4)
Favourite quotes (point 4)




Implementation strategy:

To set goals -> Capture goals in a document and feed it. Completely manual, done every few weeks/months.
Sleep goals -> explore automation of exporting key sleep data automatically.
Habit tracker -> Set manually in a csv file. Habit occurrence tracked by manual addition into a csv using apple watch automation.
Health goals -> eg weight, bmi, body fat percentage etc
Food tracking ->
Master csv with all recipes, macros and calories
Another csv with each food item entered through apple watch automation
Learning goals -> manual tracking into a csv file.
Manage calendar -> look for ways to integrate google calendar with APIs.


![alt text](image.png)

![alt text](image-1.png)