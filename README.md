# CI/CD

### What is CI/CD?

CI/CD, or Continuous Integration/Continuous Delivery, is a practice in DevOps that aims to improve software development and deployment processes, mainly by making them much faster.

#### Continuous Integration (CI)

Developers frequently merge their code changes into a central repository. Each merge triggers an automated build and testing process, providing immediate feedback to the team. This enables us to identify bugs early and save time through automation.

#### Continuous Delivery (CD)

Continuous Delivery is about getting the updated program to users quickly and smoothly. With Continuous Delivery, the program is always ready to be sent to users, but someone has to decide when to do it. With Continuous Deployment, the program is sent to users automatically whenever it passes all the tests.

### Why use CI/CD?

People use CI/CD when they want to improve their programs regularly without causing problems for users. It's commonly used by companies that make apps or websites.

### How does CI/CD work?

You can setup a simple CI/CD system with Jenkins, GitHub, AWS, and your own computer (localhost).

-   Developers write code on their computers and send it to GitHub, which stores the code online.
-   When new code is sent to GitHub, Jenkins (running on your computer or AWS) automatically takes the code and runs tests to make sure it works.
-   If the tests pass, Jenkins can then send the updated code to AWS, which makes it available to users online.
-   If the tests fail, Jenkins lets the developers know so they can fix the problems.
-   This way, the program is constantly being improved, tested, and made available to users, all with minimal manual work. It helps teams work together better and keeps the quality of the program high.
