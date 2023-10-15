# Continuous improvement machine learning project

## Background

I recently watched the movie [The Mill (2023)](https://en.wikipedia.org/wiki/The_Mill_(2023_film)) in which a man is kept in a cell by his employer called Mallard. We later find out that the main protagonist Joe has been locked in the cell due to poor performance and is being held captive as part of an *" advanced career training"*  to become the best version of yourself.

> *"Your company for life"* - Mallard

Mallard is an artificial intelligence (AI) that monitors employee performance and places them in a cell so they "[d]on't undervalue [themselves], [as they] are a crucial part of [the] team" and Mallard is there to help them continue to grow.

Mallard philosophy is good enough, is not good enough. Humans should always push for more and shouldn't settle for what they can achieve. 

## Context

Every company is using some form of employee key performance indications (KPIs), which are objective performance standards that set benchmarks for success. It is often used to gauge if an employee is on track to meet the goals set. These goals are supposed to motivate and engage employees to do their best work with clear, attainable goals.

I can't speak for everyone as I have no evidence for this, but often times I set these goals with my line manager and forget about it until it's the time of year again to review how these goals have been accomplished. This behavior may not be the same for all companies, but might be evident in large companies (10,000+) in which line managers have multiple employees to look after and there might be an assumption that the employee is actively working towards these goals.

Poor performance in companies can lead to being fired from your job - a team is only as good as its leader [^1]. But how much support is an employee being provided and how can we actively push employees to continue to grow.

## Example

In big consultancy companies such as McKinsey & Co, Bain & Co, Boston Consulting Group (BCG) etc. That hire over 10,000+ employees, those not in upper-management, are often at the mercy of their manager who will use feedback received to determine how well an employee is performing against KPIs set. In these large consultancy companies, metrics is everything - if you're not performing as expected - then you're under performing.

In consultancy firms, you're left to your own device to find your next project after the other, the down time between project means you are not billable and as mentioned earlier metrics is everything. These firms would like to see high utilisation of their employees.

If a KPI has been set for an employee they should be actively supported in reaching these goals. For example you might have a Software Engineer at BCG whose goal is to have hands on experience with a cloud provider. But what is done to help them achieve this goal?

- Is their line manager actively trying to find them a new project to help achieve this?
- How easy is it to find a project that provides this exposure? Are they reliant on someone else to help them.

## Idea

The idea is to use readily available organisation data and employee feedback, to help employees achieve their goals. Rather than having an employee think of goals to set for the year, they should be provided with suggestions that align with the feedback they received from others and that will help them accomplish their KPIs successfully and help them grow.

Note that I have specifically used the word 'suggest' rather than 'recommend'. As suggest[^2] means:

> _Suggest_
> to mention an idea, possible plan, or action for other people to consider

While recommend[^3] means: 

> _Recommend_
> to suggest that someone or something would be good or suitable for a particular job or purpose, or to suggest that a particular action should be done

The choice of word is very important here, as we do not want to *force* objectives that people do not want to set or give any impression they *must* do this to perform well. The employee should be the one to make the final choice.

Once a objective has been set, available data will be used to assist the employee achieve their KPI. A couple of examples have been listed below:

> Employee A
> Goal: Participate in a company environmental, social and corporate governance (ESG) event.

Employee A goal is to demonstrate ESG within their organisation. They would be suggested ESG projects that are aligned with having an impact on sociability - for example creating a platform to help farmers increase their crop yields etc. It may not necessary be a project, could potentially be an event that their organisation has arranged that they could volunteer to take apart of. 

> Employee B
> Goal: Learn to code in Python

Employee B goal is to learn a new programming language. If we were to hypothetically look into the employees' record we could see that their proficient programming is language C++. And the goal they set is to broaden their language set. They could be provided with projects that are using the Python language, but we must understand the skill set already available on the project.

We want to enable people and not unknowingly hinder them. The idiom "The blind leading the blind" is very important here as we want to give people the best opportunity to learn. Understanding the different skill levels on a project is detrimental, a team with only junior engineers may not know the most efficient way to write clean code in Python, in comparison to a Senior Python engineer who has in-depth knowledge of Python.

Employee B ideal environment should have a good balance of people with years of experience with Python, so they can learn the standards and if they make any mistakes, have someone more familiar who is able to direct them on the right path. 

## Proposed solution

With the large amounts of data available, from human resource (HR) records containing each employees skill set and certifications, to how their performed each year. We can highlight room for improvements within each individual and suggest ways to help them continuously improve.

Through machine learning we can identify trends and patterns, to able to distinguish the difference between junior members to senior leadership. And answer questions, such as Junior employees being more willing to take on more opportunity in comparison to senior employees? 

### Machine learning

To begin with, [word2vec](https://www.tensorflow.org/text/tutorials/word2vec)  algorithm can can be used on the learn word associates from the large amount of text held within the organisation. This would be through unsupervised learning as data would be coming from all aspects of the organisation. Perhaps this might be too much to train the model and potentially too time consuming?

Additionally, we can use [Matrix factorization](https://developers.google.com/machine-learning/recommendation/collaborative/matrix) which is used in existing recommendation systems. This is a good starting point, as the model can help discover new interests amongst employees and make suggestions based on similar employees.


> [!NOTE]
> My understanding of machine learning is still in the early stages, I have mentioned these these processes as this what I have come across so far. 

### Artificial intelligence

These forms of AI can be beneficial below are a couple identified and what they can help achieve.

- Sentiment analysis
	- Identify if employee feedback was positive or negative.
- Named entity recognition (NER)
	- Categories text.
- Entity linking
	- Helpful to link with existing data within the company.


## Further questions

Below are a list of questions that would be interesting to see answered, if a something like this every implemented:

- Is encouraging people to always improve, breed a competitive company culture?
- How do you measure the success of an employee taking on a suggestion? Not all improvements can be measured, especially those that build peoples character.
- As no one is being forced into doing things they don't want to, how will performance compare to employees who are actively incorporating suggestions provided vs those who do not wish to use any of the suggestions.
- Will a skill ceiling still exist, if we're always striving for self improvement?


[^1]: A Team Is Only As Good As Its Leader by [PeopleTek](https://peopletekcoaching.com/2022/08/02/a-team-is-only-as-good-as-its-leader-yes-or-no/)
[^2]: Suggest definition by [Cambridge Dictionary](https://dictionary.cambridge.org/dictionary/english/suggest)
[^3]: Recommend definition by [Cambridge Dictionary](https://dictionary.cambridge.org/dictionary/english/recommend)

---
#ml #ai #ideas