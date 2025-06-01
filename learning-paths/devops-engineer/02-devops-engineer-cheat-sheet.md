# DevOps Engineer Cheat Sheet

AWS cheat sheets were created to give a bird’s eye view of the important AWS services that required to be known by heart to be able to pass the AWS DevOps Professional exam.

The pattern is that utility services are prefixed with AWS, while standalone services are prefixed by "Amazon". The [AWS documentation page](https://aws.amazon.com/documentation/) is a great reference for determining the official name of a service.

# Generating exam questions with Large Language Models (LLMs)

There are many online services that offer exam dumps and access to practice questions for various exams. With the raise of LLMs and their large knowledge base and ability to search the web, I attempt to prompt the LLM to generate exam questions based.

## Prompt

The prompt isn't perfect, but it aims to get the LLM to generate questions and provide an answer and source with each answer. Allowing the user to understand the service and purpose, the prompt below is based on the [official exam guide as of (01/06/2025)](https://d1.awsstatic.com/onedam/marketing-channels/website/aws/en_US/certification/approved/pdfs/docs-devops-pro/AWS-Certified-DevOps-Engineer-Professional_Exam-Guide.pdf).

```markdown

Create 65 sample questions based on past AWS DevOps Professional multiple choice exam questions. Use AWS white papers, case studies, exam dumps from current and past years to formulate these questions. Match the same style of questioning, do not deviate from this.

The exam must cover tasks such as, but not limited to:

- Implement and manage continuous delivery systems and methodologies on AWS.
- Implement and automate security controls, governance processes, and compliance validation.
- Define and deploy monitoring, metrics, and logging systems on AWS.
- Implement systems that are highly available, scalable, and self-healing on AWS.
- Design, manage, and maintain tools to automate operational processes.

Questions generated must cover the domains that need to be covered:

- Domain 1: Software Development Lifecycle automation
- Domain 2: Configuration management and Infrastructure as Code
- Domain 3: Resilient Cloud Solutions
- Domain 4: Monitoring and Logging
- Domain 5: Incident and Event Response
- Domain 6: Security and Compliance

Provide questions one by one and wait for a response to answer. There are two types of questions on the exam:

- Multiple choice: 
 - Has one correct response and three incorrect responses (distractors)
- Multiple response: 
 - Has two or more correct responses out of five or more response options


After provide the correct answer to the question with a source to the answer.

```

At the end of the sample questions provide, the following should be sent to help provide a study guide to cover topics that need improvement.

```markdown
No more questions, provide a score based on questions answered so far. Provide areas of improvements with links and current chance of passing the overall exam.
```

---
#aws #aws-devops #dop-c02 #llms #prompt #ai
