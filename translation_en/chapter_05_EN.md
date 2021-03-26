# Refactoring

After a few years on the road, developers realize they spend much of their time reading code and, most of the time, code written by other developers. It’s easily noticeable that this time is greater than the time spent writing new code lines. After all, it is necessary to understand the functioning of the current code, make changes, and, mainly, which classes or files will be impacted.

No wonder a simple change in the code, like adding an `if`, may take hours or even days. Generally, this high cost of maintaining the software is a consequence of the poor quality code; after all, the greater the difficulty in reading and understanding a piece of code, the greater the time spent to change it. And make no mistake, even the developer who implemented the system’s functionality may have trouble reading the code after a few months.

To reduce the cost of system changes, it is necessary to **invest in the quality and clarity of the code produced**, either in the current code or introducing new code lines. In other words, the developer must improve the production-ready code without changing its behavior. This technique, known by a large number of professionals, is called **refactoring**.

One of the most accepted definitions in the industry for "refactoring" is the one by **Martin Fowler** in his book **Refactoring: Improving the Design of Existing Code**:

> "Refactoring is a controlled technique to restructure an existing code snippet, **altering its internal structure without changing its external behavior**. It consists of a series of small transformations that preserve the initial behavior. Each transformation (called refactoring) reflects a small change, but a sequence of transformations may produce a significant restructuring. Since each refactoring is small, an error is less likely to be introduced. Besides, the system remains fully functioning after each small refactoring, reducing the chances of the system to be seriously damaged during the restructuring." -- Martin Fowler

Based on Fowler's words, we can understand that refactoring is the process of modifying a piece of code already written, using **baby steps** without changing the system’s current behavior. It is a technique used to improve some aspects of the code, such as improvements in the code clarity to make it easier to read or adjust the class’s design to bring the system greater flexibility.

## Fear of changing someone else's code

At some point in their career, developers have gotten into or will get into the middle of a software project. Out of the blue, this person started developing, correcting, and maintaining functionalities on top of an existing codebase, which can be from 6 months to 20 years ago.

This codebase has probably passed through several other developers, from novice to expert, usually under pressure and unreal and tight deadlines. To make matters worse, the team that started the project and made important architectural and design decisions will hardly be in the company. In other words, a 5-year-old system has probably seen changes in the whole team 2 or 3 times - at least.

This high turnover brings countless losses to the company, especially the system code. That is because certain critical code pieces have been altered, maintained, and evolved by several professionals, from those who dominate the business to those who don’t, from those who master technologies and frameworks used to those who have no idea how they work. Also, some have used the project as a laboratory in the short time they stayed at the company. It is not surprising that a good part of this code is full of kludges and duplicated routines, outdated comments, little clarity in business logic, high coupling, and low cohesion. Don’t be surprised by having to maintain a class or method with more than 5,000 macaroni and unreadable code lines.

Situations like this are more common than you might think. Dealing with problematic systems of this magnitude brings frustration and, mainly, **fear** to the developer who has just joined the company, who will have to work on the "someone else’s" code, left in the dark, without having the slightest idea if their last change impacts on other parts of the system or, even worse, how significant that impact is.

## The importance of automated testing when refactoring

The less knowledge about the system the developer has, the greater their insecurity when changing code and the lesser the guarantees that their changes won’t cause further damage to the system, which is already fragile over time. Even small refactorings to improve the clarity or simplicity of the code might generate new bugs, reintroduce old ones, or impact the system negatively.

If refactoring carries risks, how can the developer ensure no errors will be introduced?

The truth is that refactoring without a concrete guarantee that current behavior won’t change is a reckless act, as there is no point in improving the code if something that already exists is broken. This guarantee can be obtained in several ways, such as with a QA team (testers) or a testing environment so that customers can validate the change. However, one of the best alternatives at a fair price is **automated testing**. A battery of regression tests would quickly show whether a particular change (or refactoring) in the code modified the functionality or even impacted other parts of the system. Consequently, any error introduced would be immediately pointed out, facilitating the correction at each refactoring step.

As Fowler said, refactoring is the act of applying small transformations to the existing code without changing its external behavior. Like many software engineering techniques, easier said than done. In a complex process, such as software development, we often have to check where we are and, if necessary, correct along the way. When refactoring, we need to verify that everything continues to work as expected in each transformation before moving on to the next step. The opposite is also true: we need to identify whether our last step broke something in the system, then undo it and adopt a different strategy. This frequent verification cycle in which we learn at each step is called a **feedback loop**. In the world of agile methodologies, feedback acts as the core enabler for all four values of the Agile Manifesto and its derivations found in the market.

The shorter and more frequent the feedback loop, the more natural the refactoring process becomes. But how to shorten it? Several practices and tools can help, such as pair programming, continuous integration (CI), or code review, but I want to highlight test coverage among them. When we have code covered by tests, this cycle is shortened so that it is natural for developers to repeat it all the time. As soon as a unit test fails and we know right away what was wrong. Notice that the tests work as a safety net to encourage developers to refactor code and, above all, to maintain the motivation to make this practice a constant in their daily routine.

I’m not going to lie: it is possible to refactor without a single automated test line - many companies and teams do that; however, we can’t ignore that there are significant risks involved in this practice that can introduce bugs or generate losses for the company or the end customer. Code refactoring without testing makes everything more complicated and risky, consumes more time than necessary, discourages code improvements, and still demands much more from the developer and the team. When refactoring without testing, the developer’s experience counts a lot, either to make changes with minor and safe steps, to analyze and measure the impact of their (possible) changes, or simply to decide that a specific piece of code is not worth refactoring.

Although the developer’s experience is an essential factor when refactoring code not covered by tests, depending on the company and its processes, managers are unlikely to allow modifying a code that works and is already in production.

The topic of automated testing is vast and deserves an entire chapter or even a book - and there are some excellent ones, by the way. But in order not to dwell on this topic, I leave the following question: **when refactoring, how far can we go, or how bold can we be without good test coverage?**

## Continuous code refactoring

An important point is that the company or stakeholder is unlikely to create an entire task to carry out a project’s refactoring. After all, the project’s technology or code quality has little impact on end consumers. A good strategy is the famous Boy Scout Rule. This technique is based on refactoring related codes that need improvement and clarity while going through a story that adds value to the product. To be on the safe side, always run tests before doing any refactoring.

## What is your motivation for refactoring the code?

There are several motivations for refactoring the code, and here are some of the main ones:

* **Refactoring for readability and flexibility**: readability brings several benefits to the life cycle of an application, such as agility in maintenance or easier debugging, as well as to the well-being factor within the team. It is worth mentioning that this maintenance will hardly enter as a story or activity; however, if you abstain entirely from this activity, it causes the "broken windows" factor, i.e., as the code worsens readability, the greater the tendency that the next person performing maintenance will give up quality to get rid of this activity as soon as possible until the code is a risk due to the lack of legibility.
* **Refactoring for performance**: performance is always relevant; however, one must consider whether this improvement will result in greater complexity or any other eventual data consistency problem. As Donald Knuth would say, "premature optimization is the root of all evil". Thus, any performance improvement is important, but consider its impacts and whether it makes sense at that moment.
* **Refactoring to remove duplication**: reducing code within the project is a good motivation. When we are juniors, we tend to be happy when we add code, and as we get more experienced, we are pleased to reduce the code we need. One of the strategies to reduce the code is, precisely, to avoid duplication. This will guarantee you a single point for refactoring: ease of testing, as well as performance, primarily if you work within the JVM since JIT exists.
* **Refactoring to use a library**: the best code, indeed, is the one we haven’t written. Reducing the amount of code means reducing the complexity on your side, in addition to reducing errors. This is excellent, especially when this library is already available within your project. It is worth noting that excessive dependencies can also be dangerous. Remember that the greater your dependencies, the greater the strategy for updating the libraries (in addition to the well-known "JAR Hell" problem).

# Concluding

With that, we conclude the topic of refactoring the code. We addressed essential aspects, such as the motivation to code refactoring besides its risks. As good developers, it is always important to balance motivations and avoid future code maintenance and readability problems. As always, common sense is the best tool for the developer.