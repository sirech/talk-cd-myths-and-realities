title: Continuous Delivery: Myths and Realities
class: animation-fade
layout: true


<!-- This slide will serve as the base layout for all your slides -->

---

class: impact full-width
background-image: url(images/background1.jpg)

.impact-wrapper[
# {{title}}
]

???

- this topic interests me a lot
- I did not hear about this when I was studying myself

---

class: transition

## Mario Fernandez
 Lead Developer
 
 **Thought**Works
 
???

- part of the very first class
- worked both in product companies and as a consultant
- have had time to form strong opinions about stuff

---

class: transition

# What is Continuous Delivery?

???

- let's make sure we are all on the same page before getting into details

---

class: middle center

> Continuous Delivery is the ability to get changes of all types—including new features, configuration changes, bug fixes and experiments—into production, or into the hands of users, safely and quickly in a sustainable way.

.bottom-right[
### continuousdelivery.com
]

???

- safely, quickly and sustainable, we want to keep those three words in mind
- seems straightforward

---

class: transition

# Why Continuous Delivery?

???

- every job ad says something about CI/CD
- what do companies get out of this?

---

class: center middle

![accelerate](images/accelerate.jpg)

.bottom-right[
### thoughtworks.com/radar/techniques/four­key­metrics
]

???

- there is research that shows that high performing organizations practice CD in their day to day 
- there is a reason for organizations to invest in CI/CD

---

class: transition

# How to implement it?

???

- abstract level, without tools

---

class: center middle

# 5 Principles

---

class: center middle

## Build quality in

--

class: center middle

## Work in small batches

--

class: center middle

## Automation

--

class: center middle

## Continuous improvement

--

class: center middle

## Shared responsibility

???

- last one is interesting, as often CI/CD is seen as something that is implemented by an external expensive consultant, when it should be something owned by the team

---

class: center middle

# *Let's try to get practical*

???

- not very concrete, can we picture how to apply them, though?
- was talking to a colleague that studied here as well
- recommended a proper practical case from the industry

---

class: impact full-width
background-image: url(images/background2.jpg)

.impact-wrapper[
# Case Study
]

???

- doesn't mean it can't work otherwise, just that this is how we did it in this particular case

---

class: impact full-width
background-image: url(images/background3.jpg)

.impact-wrapper[
# Agile transformation in automotive
]

???

- I'm going to talk about a project is was part of, where I brought these principles to a team
- automotive is an interesting example, as they are used to much longer release cycles

---

class: transition

# Starting point

---

class: center middle

# March, 2018

???

- context: bunch of agile teams working together
- build a platform to offer connected services to car owners

---

class: full-width
background-image: url(images/team.png)

???

- client, us and other consultancies involved

---

class: center middle

# Multiple microservices with frontend and backend

???

- web applications for car owners to check the status of their vehicles basically
- tech: spring boot + angular

---

class: center middle

![deployment-overview](images/deployment-overview.jpg)

???

- claimed to do CI/CD, in practice the picture was grim

---

class: center middle

![deployment-details](images/deployment-details.jpg)

---

class: full-width
background-image: url(images/thisisfine.png)

???

- the situation is best described by this picture
- let's go over some pain points

---

class: center middle

## Deployments every 2-4 weeks

--

## Manual regression testing before each deployment

???

- day and a half in fact

--

## Many open bugs

???

- massively inconsistent ui

--

## Unpredictable cadence

???

- most visible by our inability to size things reliably

--

## Regular delays

???

- same project was partially shipped with two quarters of delay

---

class: transition

# Something had to change

???

- client was not happy
- team members were not happy

---

class: center middle

![venn](images/venn.png)

???

- changes on all three areas were needed

---

class: center middle
![path-to-prod](images/path-to-prod.png)

???

- you'll see me talk about path to production over and over
- how fast can we move from left to right in a safe and sustainable way
- right to left is important as well. How fast can be get feedback if something is not working and fix it

---

class: transition

# Delivery pipeline
.counting[
# 1
]

???

- first area that we touched
- centralized Jenkins
- like every centralized jenkins I've ever seen, it was poorly maintained, unreliable, and didn't meet our needs

---

class: center middle

# The code for the path to production is as important as the regular code

???

- own your pipelines
- if you treat your delivery code as a second class citizen, it will be of second quality

---

class: center middle

# A good pipeline is code

.bottom-right[
### www.gocd.org/2017/05/02/what-does-pipelines-as-code-really-mean/
]

???

- no ui changes
- no XML dumps

---

class: middle

```yaml
- name: test
  serial: true
  plan:
  - aggregate:
    - get: git
      passed: [prepare]
      trigger: true
    - get: dev-container
      passed: [prepare]
  - task: test-js
    image: dev-container
    params:
      <<: *common-params
      TARGET: js
    file: git/pipeline/tasks/tests/task.yml
```

???

- this is a pipeline in concourse, the CI system we migrated to
- tool itself is not important. Most modern ones support similar ideas

---

```yaml
platform: linux
inputs:
  - name: git
caches:
  - path: git/node_modules
params:
  CI: true
  NPM_TOKEN:
  TARGET:
run:
  path: sh
  dir: git
  args:
  - -ec
  - |
    ../shared-tasks/scripts/install-yarn-packages.sh
    ./go test-${TARGET}
```

???

- pipeline itself is pretty simple, just orchestrating things

---

class: center middle

# Small, independent pipelines

???

- see the difference with what I showed before

---

class: center middle
![small-pipelines](images/small-pipelines.jpg)

???

- keyword: smaller batches

---

class: center middle

# Let the tools help you

---

class: center middle

.image-grid[
.img[![eslint](images/eslint.jpg)]
.img[![prettier](images/prettier.png)]
.img[![stylelint](images/stylelint.png)]
.img[![headless-chrome](images/headless-chrome.png)]
]

???

- using better tools save time and discussions
- keyword: automation
---

class: center middle

### thoughtworks.com/insights/blog/modernizing-your-build-pipelines

---

class: transition 

# Infrastructure
.counting[
# 2
]

???

- at this point we had a new tool, and rewritten all our pipelines
- second major area to touch

---

class: center middle
![on-prem](images/on-prem.png)

???

- at this point our apps were still running on the ancient on prem infra
- we could not split FE/BE yet

---

class: center middle
![cloud](images/cloud.png)

???

- what does using the cloud have to do with doing CI/CD?
- in my experience, a lot
- what you build and how you run it is very much related

---

class: center middle

# How do you become faster by *adding* responsibilities to the team?

???

- the migration to the cloud was to be done by our team, with some support
- we would use the "you build it, you run it" principle

---

class: center middle

# **DevOps** mindset

???

- another buzzword
- closing the gap between building software and putting it in front of users
- faster feedback loop

---

class: center middle

# Autonomy

???

- I don't have this autonomy in my current project, and it is making things painful

---

class: center middle

# Agility

???

- cheap experiments

---

class: center middle

# Leverage a larger community

???

- internal solutions tend to have few customers, and thus don't evolve much

---

class: center middle

# *Yeah, but how?*

---

class: center middle
![iac](images/iac.jpg)

.bottom-right[
### infrastructure-as-code.com
]

???

- another book I recommend, written by a colleague
- IaC means provisioning infrastructure through executable code
- Treat that code as regular code -> test, refactor

---

class: center middle
![terraform](images/terraform.png)

---

class: center middle

```hcl
resource "aws_ecs_cluster" "ecs-cluster" {
  name = "${var.ecs-cluster-name}"
}

resource "aws_autoscaling_group" "ecs-autoscaling-group" {
  name                      = "ecs-asg"
  launch_configuration      = "${aws_launch_configuration.config.name}"
  max_size                  = "${var.max-instance-size}"
  min_size                  = "${var.min-instance-size}"
}

resource "aws_launch_configuration" "config" {
  name_prefix          = "ecs-launch-configuration-"
  image_id             = "${data.aws_ami.latest_ecs_ami.id}"
}
```

???

- declarative instead of imperative
- resources are artifacts that need to be provisioned (in AWS)
- tf takes care of the dependencies between resources
- just to give you a taste, this provisions an ECS Cluster

---

class: center middle

![docker](images/docker.png)

???

- define an execution environment for an app
- portable, reusable

---

class: center middle

![infrastructure](images/infrastructure.jpg)

---

class: center middle

# Support hero
![support-hero](images/support-hero.jpeg)

???

- a process change
- rotating responsibility of monitoring and checking the state of the infra through the team
- keyword: shared responsibility

---

class: center middle

# Owning your infrastructure makes it exponentially easier to deliver changes to your application

---

class: transition

# The Code
.counting[
# 3
]

???

- notice how I didn't talk about the code we wrote yet
- you _cannot_ truly implement CI/CD for a crappy legacy app

---

class: center middle

# Design for Testability

???

- observable
- decomposable
- simple
- controllable

---

class: center middle

# TDD

???

- one of our favorite practices at TW
- when I was studying here, there was this student who was a bit older and had some industry experience who was really serious about TDD
- it took me years to understand why that was a good thing

---

class: center middle

![tdd](images/tdd.png)

---

class: center middle
![pyramid](images/pyramid.jpg)

.bottom-right[
### martinfowler.com/articles/practical-test-pyramid.html
]

???

- most people would agree that some sort of automated testing is needed nowadays

---

class: center middle

# TBD

.bottom-right[
### trunkbaseddevelopment.com
]

???

- a less known acronym
- why? In order to move faster
- it *has* to be combined with Pair Programming

---

class: center middle

# A deployment is *not* a release

???

- common misconception
- from the technical standpoint we want to have all our code on prod
- from the business side, certain features can only be released at specific points

---

class: center middle

# Feature Toggles

.bottom-right[
### martinfowler.com/articles/feature-toggles.html
]

???

- can be very complex or very simple

---

class: center middle

```html
<section class="container">
  {{ service.label }}

* <app-feature-toggle-component featureToggleName="priceTag">
    <offer-price
      class="checkbox__label checkbox__label--inverted"
      [offerPrice]="service.price"
      [offerCurrency]="service.currency"
    ></offer-price>

  </app-feature-toggle-component>
</section>
```

???

- visual toggle, the user won't see a particular change

---

class: center middle

```kotlin
@RestController
@RequestMapping(PATH, consumes = [MediaType.APPLICATION_JSON_VALUE])
*@ConditionalOnExpression("\${pact.enabled:true}")
class PactController(val repository: Repository)
```

???

- toggles can (and should!) be also implemented from the BE side

---

class: center middle

# Declarative style

???

- switch the code to a more declarative style
- makes intentions clearer
- what instead of how

---

class: center middle

```java
private ImmutableList<Vin> vinLists(HttpHeaders httpHeaders) {
    return Arrays.asList(
            httpHeaders.getFirst(HEADER_USER_VINLIST),
            httpHeaders.getFirst(HEADER_SECOND_USER_VINLIST),
            httpHeaders.getFirst(HEADER_USER_EMPLOYEE)
        .stream()
        .map(header -> toVinList(header))
        .flatMap(l -> l.stream())
        .distinct()
        .collect(ImmutableList.toImmutableList());
}
```

???

- immutable objects and lists
- declarative operations

---

class: center middle
![kotlin](images/kotlin.png)

???

- as a consequence, we wanted to move from Java to Kotlin because it supported the style we wanted to use better
- keyword: continous improvement

---

class: transition

# Ending point

---

class: center middle
![path-to-prod](images/path-to-prod.png)

???

- I showed you this before, how did we fare?

---

class: center middle

# May, 2019

???

- as you can see, change takes time

---

class: full-width concourse
background-image: url(images/all-pipelines.png)

---

class: full-width concourse
background-image: url(images/parallel.png)

---

class: center middle

.col-6.bad-practice[
### Deployments every 2-4 weeks
]

--

.col-6.good-practice[
### Multiple deployments per day
]

--

.col-6.bad-practice[
### Manual regression testing
]

--

.col-6.good-practice[
### Continuous Deployment
]

--

.col-6.bad-practice[
### Many open bugs
]

--

.col-6.good-practice[
### Zero bug policy
]

--

.col-6.bad-practice[
### Unpredictable cadence
]

--

.col-6.good-practice[
### Fairly predictable
]

--

.col-6.bad-practice[
### Regular delays
]

--

.col-6.good-practice[
### Commitments reached
]

???

- it seems like an infomercial, but the change was truly big and visible

---

class: impact full-width
background-image: url(images/background4.jpg)

.impact-wrapper[
# Conclusion
]

---

class: center middle

# Continuous Delivery can have a huge impact in the performance of a team

---

class: center middle

# It is not free. You have to invest to gain

---

class: center middle

# It is never done. You have to keep working and improving

---

class: impact full-width
background-image: url(images/background5.jpg)

.impact-wrapper[
## JOIN OUR COMMUNITY

<br />

### *26 years* experience
### *42 offices* in 13 countries
### *Thought leader* in agile software development and continuous delivery
### *6000+* thoughtworkers worldwide
### *300+* thoughtworkers in Germany

<br />

#### de-recruiting@thoughtworks.com
]

---








