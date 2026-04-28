# Interview Prep Cheat Sheet

## 1. Getting to know / Build comfort

### Q: What do you like to do outside of work for fun?
**My Keywords/Notes:**
- [Hiking, outdoor activities] 
- [Family time] 
- [projects, building things, taking apart] 

**GPT Example Answer:**
> "Outside of work I spend a lot of time with my family, and I also enjoy building small technical projects and learning new tools. Lately I’ve been especially interested in AI-assisted development and how it can help with productivity, automation, and organizing complex information. I like working on things where I can take a messy problem and turn it into something useful."

---

## 2. Motivation

### Q: What motivates you in a work environment? What makes it a great day?
**My Keywords/Notes:**
- [building tools, learning how systems work, sharing with team] 
- [solving problems] 

**GPT Example Answer:**
> "I’m motivated by solving practical problems that make the team faster or more confident. A great day for me is when I either remove a blocker, improve a test or tool that saves people time, or help the team understand quality earlier instead of finding issues late. I like seeing automation and testing become part of how the whole team works, not something owned by one person at the end."

### Q (Kate): If I were to ask your peers, what would they tell me as your top three strengths?
**My Keywords/Notes:**
- [problem solving root cause]
- [persistence unrelenting] 
- [helping takes time out of day, anytime] 

**GPT Example Answer:**
> "They would probably say my top strengths are problem solving, persistence, and helping others. I’m good at digging into unclear issues and finding the real cause. I’m persistent when something is flaky, confusing, or hard to reproduce. And I’ve spent a lot of time mentoring other QA engineers and helping developers get more comfortable contributing to automation."

### Q (Kate): What is one thing they would say you could improve on?
**My Keywords/Notes:**
- [communicating progress and tradeoffs]
- [balance exploratory time which often yields value, inform team if exceeding timebox]

**GPT Example Answer:**
> "One thing I’ve worked on is making sure I communicate progress and tradeoffs earlier when I’m deep in a technical problem. I can get very focused on solving the issue, especially with automation or tooling, so I’ve learned to pause and give the team a quick update: what I found, what is still unknown, and whether the risk or timeline has changed."

---

## 3. Testing

### Q (Steve): Are you familiar with Test-driven development? What value does that provide?
**My Keywords/Notes:**
- [TDD, great goal but not always followed] 
- [help define missing features, clarify requirements] 

**GPT Example Answer:**
> "Yes. My understanding is that TDD means defining the expected behavior through tests before or alongside implementation. The value is that it forces clarity around requirements, edge cases, and design before the code gets too far along. In practice, even when a team is not doing pure TDD, I think the same principle is valuable: define testable behavior early, clarify acceptance criteria, and build confidence as the feature develops."

### Q: How do you feel about unit tests?
**My Keywords/Notes:**
- [ essential, faster resolution ]
- [ costly QA time, reduce wasted effort] 

**GPT Example Answer:**
> "I think unit tests are essential because they catch problems closest to the code and are usually faster and more stable than UI tests. As an SDET, I may not own every unit test, but I care that the team has a good test pyramid. I want unit tests covering logic and edge cases, API or integration tests covering service behavior, and UI tests focused on the most important user workflows."

### Q: When would be a bad time to automate?
**My Keywords/Notes:**
- [ features not fully defined, brittle environment low stability, expensive to maintain ] 
- [ ] 

**GPT Example Answer:**
> "A bad time to automate is when the feature is still changing heavily, the expected behavior is unclear, or the test would be brittle and expensive without giving much value. I also avoid automating one-off scenarios that are unlikely to repeat. I usually ask: will this test catch meaningful regressions, will it be stable, and will the maintenance cost be worth it?"

### Q: What do you understand about white box testing and black box testing, differentiate between them?
**My Keywords/Notes:**
- [ blackbox = does the features work as expected, user tests ] 
- [ whitebox = testing code or API, more complex testing logic and data, even more importent in a world with AI gen code] 

**GPT Example Answer:**
> "Black box testing validates behavior from the outside, based on requirements and expected user or API behavior, without needing to know the internal implementation. White box testing uses knowledge of the code, architecture, logs, data paths, or service behavior to design deeper tests. I think both matter. Black box helps verify the product does what it should; white box helps target risk and understand why something failed."

### Q: Can you walk me through a real example of how you designed an automation testing strategy for a new application?
**My Keywords/Notes:**
- [ on a new project I would create a smoke test and get a basic framework set for the environment. Then work with the team on our features and prioritize smoke/regression tests with api, ui and accessibility] 
- [ reduce painful or time consuming manual testing ] 

**GPT Example Answer:**
> "At HMH and NWEA, my approach was to first understand the highest-risk workflows and where the team had the most manual regression pain. I would look at the user flows, the APIs behind them, the test data needs, and the environments. Then I’d decide what belonged at the API level versus the UI level. For UI, I’d focus on critical path workflows and keep the tests readable and maintainable. For API, I’d validate data setup, state changes, and edge cases more directly. I also think about how tests will run in CI, how data gets reset, how failures are reported, and what the team needs in order to trust the results."

### Q (Geoff): Tell me about a time in your current job when a Developer said they cannot reproduce your issue on their machine?
**My Keywords/Notes:**
- [ usesualy when i didn tprovide repeatable steps, best case is you can record a video and provide that with written steps] 
- [ pair on the reproducing ] 

**GPT Example Answer:**
> "I’d first assume there may be an environment, data, config, timing, or browser difference. I would gather the exact steps, test data, environment, build version, logs, screenshots or video, network details if needed, and whether it reproduces in another environment. If it still only happens in one place, I’d try to isolate what is different. My goal is not to prove the developer wrong; it’s to make the issue reproducible enough that the team can act on it."

---

## 4. Behavior

### Q: Scenario: The team is adding a new feature to the product. We work in two-week sprints here. What is the ideal point that you want to get involved as an SDET?
**My Keywords/Notes:**
- [ ASAP, edge cases, test data needs, automation strategy] 
- [ prevent rework, early feedback, automation supported features] 

**GPT Example Answer:**
> "As early as possible. Ideally during refinement or planning, before implementation starts. That lets me help clarify acceptance criteria, identify edge cases, understand test data needs, and suggest where automation should live. If SDET involvement starts only after the feature is coded, the team usually finds preventable issues too late."

### Q (Steve): Tell me about a time you had a disagreement with a developer regarding a defect severity or a testing approach. How did you resolve it?
**My Keywords/Notes:**
- [ automation isnt needed, manual testing is enough or evenm unit tests] 
- [ unit test dont cover customer experence, we need to user flow  ] 

**GPT Example Answer:**
> "I try to make those conversations evidence-based. If there is a disagreement about severity, I bring it back to user impact, frequency, risk, workaround, and release timing. If it is about testing approach, I focus on what gives the team the best confidence for the least maintenance cost. I’ve found that when the conversation is framed around risk and customer impact instead of opinion, it becomes much easier to resolve."

---

## 5. Agile

### Q: Tell me about your experience in an agile or scrum environment. What do you think is valuable about it? What do you not like about it?
**My Keywords/Notes:**
- [ slices work into smaller pieces, faster customer feedback, ] 
- [ edtech schedule limitations] 

**GPT Example Answer:**
> "I’ve worked in agile/scrum environments for many years. The best part is the regular feedback loop: planning, refinement, daily communication, demos, and retrospectives give the team many chances to adjust before problems get too large. What I do not like is when scrum becomes just a schedule process instead of a collaboration process. If testing is still treated as a final phase, then the team is not getting the full value."

---

## 6. Communication

### Q: If things are not going as planned in a sprint, who would you communicate with and why?
**My Keywords/Notes:**
- [ daily sync and or PO scrum master] 
- [ escalate to product manager if needed] 

**GPT Example Answer:**
> "I’d communicate with the people who can help make a decision: usually the developer, product owner, scrum master, and QA/SDET lead if there is one. I’d explain the risk clearly: what changed, what is blocked, what testing is impacted, and what options we have. The goal is to make the problem visible early enough that the team can adjust scope, priority, or timing."

### Q (Steve): Tell me about a time when you found a serious problem and how to communicate that information, what was the outcome?
**My Keywords/Notes:**
- [sanity check with team member, follow process] 
- []

**GPT Example Answer:**
> "When I find a serious issue, I try to communicate it quickly and clearly. I provide impact, reproduction steps, environment, evidence, and risk to release. If it is blocking or high risk, I do not just leave it in a ticket; I bring it to the team’s attention directly. The outcome I want is fast shared understanding: do we fix it now, mitigate it, or make an informed release decision?"

### Q: What are some things you have done that you feel are important to keep people informed on progress, coverage and status for the automation testing effort?
**My Keywords/Notes:**
- [CICD pipeline with test results] 
- [ ] 

**GPT Example Answer:**
> "I like to keep automation visible through clear tickets, PR notes, test run results, and regular updates on what coverage was added, what risks remain, and what failures mean. For automation, it’s important that people understand not just the number of tests, but what areas are covered, what is still manual, what is flaky, and whether the suite is giving reliable release signal."

### Q (Geoff): Describe to me in detail how to make your favorite Sandwich
**My Keywords/Notes:**
- [PB&J - never start with jam as it will get into the peanut butter, each side should have a consistant layer of peanut butter or jam covering the slice completely] 
- [ ] 

**GPT Example Answer:**
> "My favorite would probably be a simple toasted turkey sandwich. I’d start with good bread, lightly toast it so it has structure, add turkey, cheese, lettuce, tomato, and a little mayo or mustard. The important part is balancing it so it is not overloaded and does not fall apart. I’d layer the dry ingredients near the bread and keep the wetter ingredients toward the middle so the bread does not get soggy."

---

## 7. Automation

### Q: Have you integrated your tests into a pipeline? What are some considerations to think about when doing that?
**My Keywords/Notes:**
- [yes, dockerized and stability, with valid results, on stable ENV] 
- [clean results help and good logs for debugging] 

**GPT Example Answer:**
> "Yes. I’ve worked with automated tests running through CI/CD pipelines and Dockerized test execution. The main considerations are stability, speed, test data, environment readiness, reporting, and failure diagnosis. A pipeline test should give useful signal. If failures are hard to understand or mostly flaky, the team will stop trusting it."

### Q (Steve): What is your process for creating an automated test?
**My Keywords/Notes:**
- [good PO, understand feature, know risks, ] 
- [ ] 

**GPT Example Answer:**
> "I start by understanding the behavior and the risk. Then I decide the right test level: unit, API, integration, or UI. I identify the test data, setup and cleanup needs, and the expected assertions. Then I write the test in a way that is readable, stable, and maintainable. After that I run it repeatedly, review failure output, and make sure it can run reliably in the target environment."

### Q: When do you use a wait or sleep?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I avoid fixed sleeps unless there is truly no better option, and even then I treat it as temporary. I prefer explicit waits based on a real condition: element visible, API response complete, network idle, state changed, or expected data available. Fixed sleeps slow the suite down and often hide timing problems instead of solving them."

### Q: How do you identify which tests are high priority for automation?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I prioritize tests that cover critical user workflows, high-risk areas, frequent regressions, business-critical behavior, and scenarios that are expensive or error-prone manually. I also look for stable functionality where automation will be reliable. Repetition matters, but risk and value matter more."

### Q: Walk me through the structure of your most recent automation framework
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "A typical structure I’ve used separates test specs from reusable page objects or workflow helpers, API utilities, test data setup, config, fixtures, and reporting. For Cypress or Playwright, I want the test itself to read like a clear user workflow, while setup, selectors, API calls, and repeated actions live in reusable layers. I also care about environment config, Docker or pipeline execution, and making failures easy to debug."

### Q: How do you pick your page object models?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I do not force page objects everywhere. I use them when they reduce duplication and improve readability. Usually I model stable pages, components, or workflows that are reused across tests. I avoid giant page objects that become dumping grounds. The goal is to make tests easier to understand and maintain, not to add abstraction for its own sake."

### Q: If an automation suite is created and running into flaky tests, what are three things you look at to make them more stable?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "First, I look at timing and synchronization: are we waiting on real conditions or guessing? Second, I look at test data and environment state: are tests isolated, or are they depending on shared data? Third, I look at selectors and application behavior: are we targeting stable elements, and are there animations, async calls, or race conditions causing failures? I’d also check whether the flakiness points to a real product issue."

### Q: Given a new system with an unknown mix of UI + APIs + auth, how do you determine what to test and what requests need to be hit?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I’d start by mapping the main user workflows and identifying the APIs involved through documentation, dev conversations, logs, or browser network tools. Then I’d separate what needs UI validation from what can be tested more directly at the API level. For auth, I’d understand login flow, token/session behavior, roles, permissions, and test data setup. Then I’d build coverage around the highest-risk workflows first."

### Q: Explain Explicit or implicit wait.
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "An implicit wait tells the driver to wait a general amount of time when trying to find elements. An explicit wait waits for a specific condition, like an element becoming visible or clickable. I prefer explicit waits because they are more intentional and easier to reason about. In Playwright and Cypress, a lot of waiting is built into the framework, but you still need to understand what condition the test is really waiting for."

### Q (Steve): How do you feel about testing some old technology? Some teams are on FoxPro or built on JSP pages.
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I’m comfortable with it. Older technology can be harder to test, but the same quality principles apply: understand the risk, identify stable test points, use whatever hooks are available, and avoid overengineering. With legacy systems, I’d be practical. Sometimes API, database, logging, or workflow-level checks give better value than trying to force a modern UI testing pattern onto an old application."

---

## 8. Technical

### Q: When a page has no reliable IDs, test IDs, or stable attributes, what is your strategy for building locators that will survive UI changes?
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "My first choice is always to ask for better test hooks, like stable data attributes, because that is usually the most maintainable solution for the team. If that is not available, I try to build locators around user-facing behavior rather than fragile layout. For example, accessible roles, labels, button names, form labels, text near the element, or stable parent-child relationships. I avoid long brittle XPath chains or selectors tied to CSS classes that are likely to change.
> 
> If I have to use a more complex locator, I keep it isolated in the page object or helper layer so only one place changes later. I also document why the locator was chosen and, if possible, create a follow-up task to add better test IDs."

### Q: How do you determine whether a flaky test is caused by the application, the environment, or the test code itself? Give me an example.
**My Keywords/Notes:**
- [ ] 
- [ ] 

**GPT Example Answer:**
> "I start by looking for the failure pattern. If it only fails in CI, I look at environment timing, browser differences, resources, or test data. If it fails in one environment but not another, I look at config, services, data, or deployment differences. If it fails everywhere but inconsistently, I look closely at the test logic, waits, selectors, shared state, and async behavior.
> 
> For example, if a test clicks a button and fails because the next page does not load, I would check whether the request actually fired, whether the API returned successfully, whether the UI updated, and whether the test waited on a real condition. If the API is slow or failing, that points to app or environment. If the app behaved correctly but the test clicked too early or used a brittle selector, then it is test code."

### Q: When you have a workflow that spans several pages, how do you structure that in POM without making it messy? Give me an example.
**My Keywords/Notes:**
- [based on page complexity and components] 
- [helper functions for common workflows]

**GPT Example Answer:**
> "I would avoid making one giant object that knows too much. I’d keep each page object focused on the actions and assertions for that page or component, then use a workflow helper when a test needs to move through several pages. That keeps the page objects clean and prevents the test from becoming a long script of low-level clicks.
> 
> For example, if a workflow goes through account setup, settings, review, and confirmation, I might have separate page objects for each area. Then I’d create a helper like `completeAccountSetupWorkflow` that coordinates the common flow. The test can stay readable, while the lower-level page details stay isolated."

### Q: Give me an example of a time you had to learn a new automation tool or framework quickly (Playwright or otherwise). How did you approach it?
**My Keywords/Notes:**
- [get a smoke test running, gets repo tools and PO setup] 
- [Automation team released Selenium Python project for ORG and was disbanded without support. I was unable to get working so I had to learn how to spin up Java Selenium and have a smoke test running the same day.] 

**GPT Example Answer:**
> "When I need to learn a tool quickly, I try to build a real working slice instead of only reading about it. I focus on the core things that matter in automation: locators, waits, assertions, setup and cleanup, auth, config, reporting, and CI execution.
> 
> For example, with a framework like Playwright, I would start by creating a small proof of concept around a real workflow. I’d include login or auth setup, one API-assisted setup step, one UI path, and a useful assertion. That gives me enough to understand how the framework behaves in practice and what patterns I would want the team to follow."

### Q: Give me an example of a change you made to an existing test framework. How did you make sure you didn’t break other tests?
**My Keywords/Notes:**
- [local testing, INT testing then PRD if needed] 
- [] 

**GPT Example Answer:**
> "One example would be changing shared framework utilities or test setup code. I treat that carefully because many tests can depend on the same helper. I would first understand which tests use the code, make the change in a small branch, and run the most directly affected tests locally.
> 
> After that, I would run a broader regression subset in CI and look for failures that might come from hidden dependencies. I’d also update examples or documentation if the team needs to use the new pattern. The main idea is to keep the framework change small, test the impact in layers, and avoid surprising the rest of the suite."

### Q: Give me an example of a mistake you made when working in a new automation framework. What went wrong and what did you learn?
**My Keywords/Notes:**
- [UI only testing, does it need unit, integration or API tests] 
- [push logic down to API or unit test level] 

**GPT Example Answer:**
> "One mistake I made earlier in automation was relying too much on UI-level coverage because it was the most visible and straightforward place to start. That can work at first, but over time it can create a suite that is slower, more brittle, and harder to maintain.
> 
> What I learned was to be more intentional about where the test belongs. Before automating, I ask whether the behavior should be covered at the unit, API, integration, or UI level. UI tests are still important, but I want them focused on critical workflows where the browser experience matters, not every possible validation."

### Q: Give me an example of how you’ve used AI to help write or improve automated tests. What did it generate, and what did you have to fix?
**My Keywords/Notes:**
- [testcase to code, code to testcase conversion] 
- [create PO (page object) files, FIX selectors]
- [generate test data]

**GPT Example Answer:**
> "I’ve used AI as a drafting and acceleration tool, not as something I trust without review. For automated tests, I’ve used it to help generate first-pass test cases, test data ideas, utility functions, and starter code based on acceptance criteria or existing patterns.
> 
> What I usually have to fix is the real project context. AI may guess selectors, data setup, framework conventions, or assertions incorrectly. I still need to verify the behavior, replace brittle selectors, align the code with the framework structure, and make sure the test is maintainable. The benefit is that it helps me move faster from idea to draft, but the engineering judgment still has to come from me."

### Q: Give me an example of how you would integrate AI into a team’s automation workflow. Where would it help most, and where would you put guardrails?
**My Keywords/Notes:**
- [ starter code, test ideas, edge cases ] 
- [ debug failures, explain flaky behavior ] 
- [ review code, verify selectors, assertions, test data ] 
- [ reduce repetion, not replace review or ownership ]

**GPT Example Answer:**
> "I would integrate AI first in areas where it improves productivity without creating a lot of risk. For example, it can help draft test ideas from acceptance criteria, suggest edge cases, generate starter automation code, summarize failures, create test data variations, or help explain a flaky failure pattern.
> 
> The guardrails are important. I would not allow AI-generated tests to go straight into the framework without review. The team still needs to verify selectors, assertions, test data, security concerns, and whether the test belongs at the UI, API, or unit level. I would also follow company policy around source code and sensitive data. AI should speed up the team’s thinking and reduce repetitive work, not replace review or ownership."

### Q: Give me an example of a piece of automation code you wrote that improved reusability or maintainability in the framework. What problem were you solving, what did you build, and how was it used by other tests?
**My Keywords/Notes:**
- [build checkout service for test accounts, tests could run in parallel without collision] NWEA automated ticket-style system for managing test users
- [Int or PRD testing needed isolated test accounts] 

**GPT Example Answer:**
> "At NWEA, one example was an automated ticket-style system for managing test users. The problem was that testers and tests needed unique accounts, but shared users could collide, get locked, or end up in the wrong state. That made test setup slower and less reliable.
> 
> I built a reusable approach where a tester or automation flow could check out a unique account when needed. That improved maintainability because the test data problem was solved in one place instead of every test or tester handling it differently. It also made tests more reliable because they were less likely to interfere with each other."

### Q: Give me an example of a difficult automation problem you solved in code — something like dynamic data, async timing, a custom control, parallel execution, or unreliable setup. Walk me through the actual logic of how you solved it.
**My Keywords/Notes:**
- [MAP tests items, they have click, drag text input etc, automation needed to handle all of these]
- [build easy UI interface to manage 2 databases API sources managing cleanup processes ] 

**GPT Example Answer:**
> "One difficult problem I worked on was around unreliable test setup and application state. At HMH, teams had workflows where data was split across multiple database systems, and QA or developers needed to update, delete, reset, or configure data across environments. The manual process was slow and easy to get wrong.
> 
> The solution was to build internal tooling that made those setup operations repeatable. The logic was: identify the required starting state, expose controlled actions like add, update, delete, reset, or configure settings, validate the input, perform the right backend operations, and then give the user a clear result. That helped remove manual setup steps and made testing more consistent.
> 
> In an automated test framework, I would apply the same logic. The test should create or reserve the data it needs, verify setup succeeded, run the workflow, assert the expected result, and then clean up or reset state when appropriate. That makes the test less dependent on mystery data or manual preparation."

---

## 9. Growth Mindset

### Q: Can you share a time when you have made a mistake and what you learned?
**My Keywords/Notes:**
- [all code in single test file] 
- [hesitated or jumped the gun on a cristical bug, should have spent time investgating also pairing to sanity check my assumptions] 

**GPT Example Answer:**
> "Earlier in my automation work, I sometimes tried to automate too much through the UI because that was the most visible layer. Over time I learned that a large UI suite can become slow and brittle if the test pyramid is not balanced. I became more intentional about pushing coverage lower when possible, using API setup, improving test data control, and keeping UI tests focused on the highest-value workflows."

### Q (Geoff): How do you best learn? Give me an example.
**My Keywords/Notes:**
- [build, using or breaking something to understand it] 
- [video tutorial, docs and examples]

**GPT Example Answer:**
> "I learn best by building something real. For example, when adopting newer tools or AI-assisted development, I do not just watch tutorials. I try to apply the tool to a real workflow, like building an internal UI, improving test data setup, or converting a manual process into something automated. That helps me understand both the tool and where it is actually useful."

---

## 10. Task Management / Prioritization

### Q: How do you manage the unplanned work that comes into your day? For the team?
**My Keywords/Notes:**
- [triage then report to team or manager] 
- [team level we communicate with stakeholders] 
- [dont silently absorb unplanned work]

**GPT Example Answer:**
> "I triage it against sprint goals, release risk, severity, and whether someone is blocked. If it is urgent and high impact, I communicate the tradeoff and adjust priorities with the team. If it is important but not urgent, I capture it and schedule it properly. I try not to silently absorb unplanned work because that hides the true cost from the team."

---

## 11. Team

### Q: What Agile ceremony do you feel is the most important and why?
**My Keywords/Notes:**
- [planning or refinement, SDET provide input early] 
- [organize work, expose risks] 

**GPT Example Answer:**
> "I think refinement is one of the most important because it is where the team can prevent confusion before the sprint starts. That is where QA and SDET input can clarify acceptance criteria, edge cases, test data, risks, and automation strategy. Standup is useful, but refinement is where a lot of quality can shift left."

### Q (Geoff): Tell me of a time at your current job, where the development team is producing faster than you can test, as a team how did you handle this?
**My Keywords/Notes:**
- [QA bottleneck, focus on high risk areas first] 
- [make quality a team effort] 

**GPT Example Answer:**
> "I’d make the risk visible and work with the team to rebalance. That could mean narrowing scope, pairing earlier with developers, moving more checks into unit or API tests, automating the highest-risk regression paths, and agreeing on what must be tested before release versus what can wait. The important thing is not to let testing become an invisible bottleneck at the end."

---

## 12. Process

### Q: What kinds of things do you look for when doing a pull request?
**My Keywords/Notes:**
- [feature match, logic understandable, tests added/updated, edge cases considered, risk in related areas] 
- [ ] 

**GPT Example Answer:**
> "I look for whether the change matches the story, whether the logic is understandable, whether tests were added or updated, whether edge cases were considered, and whether the change creates risk in related areas. For test code specifically, I look for readability, stable selectors, good assertions, clean setup and cleanup, and whether the test will be maintainable in CI."

### Q: How do you go about identifying and prioritizing tests to automate?
**My Keywords/Notes:**
- [remove heavy redundent, focus on value quick to complete and high impact] 
- [type of test, unit, API or UI] 

**GPT Example Answer:**
> "I start with risk and repeatability. I prioritize critical user paths, high-impact business workflows, areas with frequent defects, and tests that are painful or time-consuming manually. Then I choose the right level for each test. I do not want every automated test to be a UI test. The goal is useful confidence, not just a larger test count."

---

## Questions For Us (The Interviewers)


**Good Questions to Ask:**
- "What does success look like for this SDET role in the first three to six months?"
- "What would make someone especially successful on this team?"
- "How is testing currently split between developers, QA, and SDETs?"
- "What are the biggest pain points right now: flaky tests, lack of coverage, test data, CI pipeline speed, legacy systems, or something else?"
- "How much opportunity is there to improve internal tooling or testing infrastructure?"
- "How is the team thinking about AI-assisted development or AI-assisted testing today?"


**Closing Pitch / Why this role:**
> "This role is interesting to me because I enjoy the mix of hands-on automation, improving team productivity, and building better testing infrastructure. I’ve spent a lot of time helping teams move from manual or late-stage testing toward more useful automation and earlier quality ownership. I’m also excited about where AI is changing development and testing, especially when it is used practically to remove bottlenecks and help teams move faster without lowering quality."
