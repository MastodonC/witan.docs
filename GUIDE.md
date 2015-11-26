# User Guide for Witan
#### Current Version: MVP 1
Witan is a tool for planners and policy makers to help form a better understanding of the data that drives their city.

---------------------
## Prerequisites
* You require a login for Witan. Currently, the best way to obtain one is to [contact us](theteam@mastodonc.com) and we can facilitate, or alternatively, ask your manager.
* ***Please understand that Witan is currently in an ALPHA state and is prone to glitches and bugs. If you encounter such an event, please [contact us](theteam@mastodonc.com) and let us know what happened.***

## Signing in
![Witan login](imgs/login00.png)
Enter your email address and password and press 'Sign In'. If you've lost your password, click the 'forgotten password' link and it will allow you to send us an email message, asking for your password to be reset.

## Dashboard
![Witan login](imgs/dash00.png)
After signing in you will see a dashboard which lists all of the *Projections* that you have access to.

Witan pivots around a core set of primitives which should be familiar to most people who've worked in city planning:

#### Data
Data is the foundation substance of Witan and can represent a vast array of different information, from statistics to sensor outputs. Data can enter the system in one of two ways: 
* *Uploaded and used as an **Input** for a Projection*
* *Produced as an **Output** (result) from a Projection*.
```html
ALPHA: We only accept uploads in CSV format and projections will only output CSVs.
```

#### Models
Models represent the transformation process under which Data is processed in the pursuit of a particular outcome. For example, given an *Input* of a city's population figures a Model could manipulate those figures using a particular assumption about migration, to predict growth over the next 10 years. A different Model could use a different assumption, or could focus on an entirely different prediction altogether.  

#### Projections
In Witan we use the term 'Projection' as an umbrella term to refer to the combination of a Model, it's configuration, a fixed set of Input Data and the resulting Output Data. We use this term because when thinking about a projection, in statistical terms, all of these parts are important to consider when evaluating the results. 
