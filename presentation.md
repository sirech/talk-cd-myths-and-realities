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

---

class: transition

## Mario Fernandez
 Lead Developer
 
 **Thought**Works

---

class: transition

# What is Continuous Delivery?

---

class: middle center

> Continuous Delivery is the ability to get changes of all types—including new features, configuration changes, bug fixes and experiments—into production, or into the hands of users, safely and quickly in a sustainable way.

.bottom-right[
### continuousdelivery.com
]

---

class: center middle

![cd](images/cd.jpg)

---

class: transition

# Why Continuous Delivery?

---

class: center middle

![accelerate](images/accelerate.jpg)

.bottom-right[
### thoughtworks.com/radar/techniques/four­key­metrics
]

---

class: transition

# How to implement it?

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

- nice concepts, but on their own they don't have a lot of meaning

---

class: center middle

# *That's not very concrete*

---

class: center middle

# *Let's try to get practical*

???

- was talking to another former student of this program that now works at TW as well
- recommended a proper practical case from the industry

---

picture of left to right

---

class: impact full-width
background-image: url(images/background2.jpg)

.impact-wrapper[
# Case Study
]

---

class: impact full-width
background-image: url(images/background3.jpg)

.impact-wrapper[
# Agile transformation in automotive
]

---

portfolio context

---

class: transition

# Starting point

---

class: center middle

# March, 2018

---

class: full-width
background-image: url(images/team.png)

---

class: center middle

# Multiple microservices with frontend and backend

---

class: center middle

![deployment-overview](images/deployment-overview.jpg)

---

class: center middle

![deployment-details](images/deployment-details.jpg)

---

class: full-width
background-image: url(images/thisisfine.png)

---

class: center middle

## Deployments every 2-4 weeks

--

## 1,5d regression testing before each deployment

--

## Many open bugs

--

## Unpredictable cadence

--

## Regular delays

---

class: transition

# Something had to change

---

class: center middle

![venn](images/venn.png)

---

class: center middle
![path-to-prod](images/path-to-prod.png)

---

class: transition

# Delivery pipeline
.counting[
# 1
]

---

class: center middle

# The code for the path to production is as important as the regular code

---

class: center middle

# Own your pipelines

---

class: center middle

# A good pipeline is code

.bottom-right[
### www.gocd.org/2017/05/02/what-does-pipelines-as-code-really-mean/
]

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

---

class: center middle

# Small, independent pipelines

---

class: center middle
![small-pipelines](images/small-pipelines.jpg)

---

class: center middle

# Let the tools help you

---

class: center middle

.image-grid[
.img[![eslint](images/eslint.jpg)]
.img[![prettier](images/prettier.png)]
.img[![stylelint](images/stylelint.png)]
]

---

class: center middle

### thoughtworks.com/insights/blog/modernizing-your-build-pipelines

---

class: transition 

# Infrastructure
.counting[
# 2
]

---

class: center middle
![cloud](images/cloud.png)

---

class: center middle

# How do you become faster by *adding* responsibilities to the team?

---

class: center middle

# **DevOps** mindset

---

class: center middle

# Autonomy


---

class: center middle

# Agility

---

class: center middle

# Leverage a larger community

---

class: center middle

# *Yeah, but how?*

---

class: center middle
![iac](images/iac.jpg)

.bottom-right[
### infrastructure-as-code.com
]

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

---

class: center middle

![docker](images/docker.png)

---

class: center middle

![infrastructure](images/infrastructure.jpg)

---

class: center middle

# Support hero
![support-hero](images/support-hero.jpeg)

---

class: center middle

# Owning your infrastructure makes it exponentially easier to deliver

---

class: transition

# The Code
.counting[
# 3
]

???

- notice how I didn't talk about the code we wrote yet

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

---

class: center middle

![tdd](images/tdd.png)

---

picture of testing pyramid transition

---

class: center middle

# TBD

.bottom-right[
### trunkbaseddevelopment.com
]

---

class: center middle

# A deployment is *not* a release

---

class: center middle

# Feature Toggles

.bottom-right[
### martinfowler.com/articles/feature-toggles.html
]

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

---

class: center middle

```kotlin
@RestController
@RequestMapping(PATH, consumes = [MediaType.APPLICATION_JSON_VALUE])
*@ConditionalOnExpression("\${pact.enabled:true}")
class PactController(val repository: Repository)
```

---

class: center middle

# Declarative style

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

---

class: transition

# Ending point

---

class: center middle
![path-to-prod](images/path-to-prod.png)

---

class: center middle

# May, 2019

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
### 1,5d regression testing
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

TW publicity?

---








