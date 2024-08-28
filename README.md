#Building a personal assistant 
##A RAG based AI assistant
###Introduction
I've always dreamt of having a second brain—an ever-present companion that seamlessly supports my every need making me more productive. The idea of having someone (or in this case, something) that always has your back is undeniably fascinating. But aren’t personal assistants just for top-level professionals, business owners, or even superheroes? Why should they have all the fun? That's exactly why I decided to create Jarvis, my very own AI-powered personal assistant, bringing the dream of a second brain to life for me and you.

Let’s get started, shall we?

###Capabilities of the assistant
While the thought of having an all knowing AI being able to do everything for you from day one is fascinating, let’s step back for a second. It is highly unlikely that I will be able to build it in just one iteration. But what I can do is to break down the capabilities the AI should have to be a true personal assistant and iteratively build features towards the requirements like in software development where the concepts of MVPs(minimum viable product) is used.

So here is a list of basic requirements sorted in descending order of importance.

Scheduling my day: 
A place to schedule meetings, plan days.
Automatically figure out and recommend the best time for an event.
Ideas tracking:
A place to note down my ideas and retrieve them effortlessly.
goals
Remember things for me:
To-do lists
Important dates - goals, deadlines etc
Be able to set goals effortlessly:
See my progress with these goals. Preferably a dashboard with the ability to easily add new data. Major types of goals I am interested are:
Sleep goals - apple watch
Habit tracker - manual feed through apple watch shortcut
Health goals - Health app and shortcut manual feed
Exercise goals - health app
Food consumption tracking
Learning goals - manual through watch
Based on my goals and current progress, recommendations to best achieve my goals.


###Initial Research
Now that the requirements are clear, let us come to the how. My idea is to not reinvent the wheel for every single task but somehow use existing tools like google calendar for scheduling, to-do list for tracking and some kind of a database like notion for all personal stuff. I need a way to be able to have an AI that can call these tools and interact with them as and when necessary and retrieve/write to them. But how? After some research, I found a beautiful concept called function calling. 

In the words of openAI, Function calling allows you to connect models like gpt-4o to external tools and systems. This is useful for many things such as empowering AI assistants with capabilities, or building deep integrations between your applications and the models. 

Also looking into langChain, I understood that I can create such functions and the AI will take care of calling the required functions as and when necessary. Reading more about this, I found out about the agentic framework where each of these functions is thought of as an agent (think in terms of a microservice in a microservice architecture). The agent can independently solve a single task like updating a database, reading from a database, calling an API etc. But the real beauty lies in using a central AI that uses these agents at will and performs more complex tasks. Think of this as the AI orchestrating these agents in such a way that it solves a given task. Before we delve deeper into the two kinds of architectures and why I chose the Agentic workflow way, let us look at the tools I want to use.

Base tools to integrate to:
Scheduling - Google Calendar - using an Google Calendar API
Tracking - reminders app - input using any apple device (creating a shortcut)
Goals and thoughts - notion - notion API

These are the current apps I am using and it makes sense for me to continue with them but remember that you can always swap these out for your preferences.
Framework
With this out of the way, I wanted to understand the best architecture to follow here. Is agentic framework the one or should I use anything else.I came across two approaches
Agentic approach
ETL/systematic approach
Let us look more closely at what they mean. For this consider a situation which we want the AI to solve. Consider three theoretical tasks:
Lookup google calendar - An agent whose sole task is to lookup the calendar
Call people - An agent whose sole task is to make calls to people
Place an amazon order - An agent whose sole task is to lookup an item, select an address and place an order.
Suppose one of the tasks I want my assistant to achieve is to remind me when a friend’s birthday is around the corner and let me call them. Also, I want to send a gift to them. 
####Option 1 - Agentic approach:
In this approach, each of these tasks is treated as an agent as follows:
Lookup google calendar - An agent whose sole task is to lookup the calendar
Call people - An agent whose sole task is to make calls to people
Place an amazon order - An agent whose sole task is to lookup an item, select an address and place an order.
 Apart from these three agents, we also have a central piece where what each agent does is written in detail for each agent and fed to the LLM. The LLM then during run-time uses the context provided for each agent and the current problem at hand(user prompt) and figures out how to chain these tasks. (very definition of chaining according to langchain).
Advantages: 
Easier to scale. Just keep writing new functions and adding to context.
No need to think of all use cases beforehand.
Will get better over time.
Can incorporate ETL type design by providing more examples.
Disadvantages:
Very new framework might not work all the time as expected.
Since LLMs are probabilistic models, having the LLM to figure out the steps as well the order of execution and parameters might increase the probability of error.
####Option 2 - ETL or systematic design:
This approach starts off with us defining all the possible tasks we might want to do. In our case, we hard code the steps in order and feed it to the LLM. Similarly, We write down a sequence for each pipeline(think in terms of various transform stages in pipelines).During inference, the LLM picks up which pipeline to use and executes it.
Advantages:
ETL type framework is proven
Since less steps are involved for the LLM to figure out, the model will be more accurate.
Disadvantages:
Significant time investment into writing down use cases.
For every new use case we might think of, we need to create a new pipeline.
As models get more powerful, this becomes less of an issue.
###Final Decision
I decided to go on with the agentic approach since it was more promising. I will use langChain for this project. I am sure as the models get better, the framework will get better and the accuracy will improve as well. The major reason I did not go ahead with the systematic design approach is because I would have to list all possible scenarios I want my AI to work on and there is no scope for innovation there.
Architecture of the AI agent
Below is a swimlane diagram of the basic architecture we are going to build:


I want to create this in a modular way so that in future if anyone wants to add an agent(horizontal scaling), they can just add new actions and plug into the architecture. This is how the flow is going to be. The prompt is fed to the AI Orchestrator which parses the prompt, understands it and creates a plan of action. It creates a list of actions that need to be taken to accomplish the task. This includes a list of agents it must call and the order in which it must call them. Further, it also decides how it is going to use the output from one agent and how it needs to be fed to the next agent. This method of breaking the task into smaller tasks is called chain-of-thought prompting and it helps the AI understand the task better. I will also provide a few examples of how this can be done in the prompt message. In future, I plan to leverage Prompt caching to save these examples in the cache. Once the LLM gives the output, it is parsed to give short answers as responses. In future, I will also add two text to speech models to enable multimodal operations.
Methods of Processing Inputs
An AImodel  is only as good as its data. Garbage in is garbage out. We have heard this a million times. We already know the importance of having clear, well formatted data. In this use case, we would need it more than ever. This is because without clear data that is regularly captured, the AI will not know about what the person is doing. This brings the most important question. How is data being captured? Here is the strategy I plan to follow.

Manage calendar -> look for ways to integrate google calendar with APIs.
To set goals -> Capture goals in notion and feed it. Completely manual capture, done every few weeks/months. The AI will use notion API to use this.
Sleep goals -> I want to explore the automation of exporting key sleep data automatically from apple watch.
Habit tracker -> Set manually in notion. APIs used to periodically get this information.
Health goals -> eg weight, bmi, body fat percentage etc. Use apple health app automation.
Food tracking ->
Master csv with all recipes, macros and calories
Another csv with each food item entered through apple watch automation

This is what I have so far. Next, I plan to research a little more on these strategies and start implementing. I plan to implement the calendar and notion integrations first so stay tuned.

