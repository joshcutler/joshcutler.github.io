---
layout: post
title: Innovation Outsiders
---

## TL;DR

The vast majority of  big pillar innovation work being done in corporate america is relegated to innovation outsiders -- groups external to the core product delivery teams (Labs, Innovation Teams, etc.).  They are expected to find a business partner or a sponsoring executive that can fund their work to incrementally transform existing products.  This approach has been historically fraught, both at more traditional as well as companies renowned for their technical innovation. The difficulty is driven by myriad issues including difficulty aligning roadmaps, funding, awareness and accommodating technical risk.  This post lays out a hypothesis that you must reverse the flow of innovation, have it driven by transformative products and let the technical innovation derive from that.


## The Problem

> When a CEO announces a major initiative to foster innovation, mark your calendar. Three years later, many of these ambitious ventures will have quietly expired without an obituary. Among those that have met that fate in recent months are initiatives at Target, Alaska Airlines, Coca-Cola, the New York Times, and Chubb. 
>
> &mdash; [Harvard Business Review](https://hbr.org/2017/04/the-stage-where-most-innovation-projects-fail)

At any sufficiently large organization, there will be many groups currently tasked with technical innovation.  Very few own a product via which to deliver their work and as a result are either a solution looking for a problem (e.g. your blockchain team) or a matrixed project partner for an existing legacy product team (e.g. machine learning COEs).  The matrixed partner relationship has a number of inherent issues that cause even the most effective and [best-intentioned technologists to fail](https://hbr.org/2019/07/building-the-ai-powered-organization).

First, many innovation teams suffer from a [lack of alignment with the business goals and risk profiles](https://hbr.org/2019/07/why-innovation-labs-fail-and-how-to-ensure-yours-doesnt).  It is difficult for a product team to take hard dependencies on potentially “risky” technologies from innovation outsiders.  This innovation is being delivered by a group that is outside of the core group that is held accountable for timelines and as such it could disrupt existing legacy lines of business and timelines.

Second, there is a non-trivial gap between the expectations of teams that are building POCs and those that are tasked with putting that same technology into production.  That gap in knowledge goes both ways.  The production team has gaps regarding the underlying tech and will have difficulty changing it to meet their production needs.  The innovation outsiders will not have fully vetted their POC as to the security, scalability and uptime requirements that are demanded by teams shipping software into production.

Finally, and most importantly, there are cultural signals that innovation outsiders implicitly send.  Their existence signals to existing teams that they are incapable of doing innovative work.  The inception of the team signals an overall dissatisfaction at the level of innovation being produced by the rest of the organization.  Even when the new innovative solutions are a perfect fit, there is a strong tendency to dismiss collaboration via a “not invented here” attitude driven by this resentment.

Given these issues, why do such teams get created?

## Why Innovation Outsiders?

Outside innovation groups are frequently created in response to a real problem that exists at the company: leadership feels that existing teams are either incapable from a skillset, disposition or bandwidth perspective of embracing new technologies and solving hard problems.  In response to the perception that existing teams are not up to the job, leadership creates a new team, tasked with utilizing the best of breed new technologies to solve the industry’s hardest problems&trade;.  They hire a bunch of new, frequently outside, talent and potentially have pay inflation for this group.  This group of superstars is supposed to come in and show the slow/legacy/etc. teams the path forward.

Inevitably, this team runs into the above roadblocks that are inextricably linked to their origin story.

## Historical Examples

The Lab or Innovation Center approach that many organizations employ have historically been utilized with [little success over the past 20 years](https://hbr.org/2015/06/you-need-an-innovation-strategy).  Microsoft had [Live Labs](https://en.wikipedia.org/wiki/Microsoft_Live_Labs) (2006-2010)  whose mandate was:

> Live Labs' focus was on applied research and practical applications of computer science areas including natural language processing, machine learning, information retrieval, data mining, computational linguistics, distributed computing, etc.

It was ultimately shuttered because of the difficulty of injecting this innovation back into the revenue generating projects.  Mor recent efforts such as the [Microsoft Garage(https://en.wikipedia.org/wiki/Microsoft_Garage)] are primarily outlets for employees to utilize their “20% time”, coordinate hackathons and accelerate cultural change throughout existing teams.  Yahoo floundered with its [BrickHouse effort](https://techcrunch.com/2008/12/09/yahoo-to-close-brickhouse-by-end-of-year/).  Humana [shuttered its innovation lab in 2015](https://www.forbes.com/sites/robertbtucker/2017/11/20/starting-an-innovation-lab-avoid-these-pitfalls/#26a5f4747a2b).  Even Google, who is famous for its technical innovation, has had [issues with the Google X team](https://www.vox.com/2016/8/29/12663630/google-x-alphabet-moonshot) in the past because so much time is spent solving technical problems that never ship (self driving cars, Robotics acquisitions, etc.).  This is after investing upwards of $1 billion per year at some points.  The list of recent closures is quite long:

> Nordstrom downsized its innovation lab two years ago (2015). Ad agency Ogilvy closed Ogilvy Labs last summer. Many others have abandoned theirs, too: Microsoft’s Silicon Valley Research Lab (shut down in 2014), Disney’s research lab (shut down in 2016), Turner’s Media Camp (shut down in 2014), Coca-Cola’s Founders Initiative (shut down in 2016), The New York Times’ R&D Ventures (shut down in 2013), and Adecco’s Ignite Lab (shut down in 2016).
>
> &mdash; [Venture Beat](https://venturebeat.com/2017/03/22/its-time-to-ditch-your-innovation-lab/)

Optum’s other pushes into innovation with groups such as the Optum Garage have attempted to build and ship products but the innovation has been more about process than technology, resulting in products like SnapVet[^12].

Who is doing it well?  To date, Amazon has been very reluctant to create innovation outsiders.  Their moonshots are products not technologies – and many of their products fail.  The difference is that their products drive their innovation and that the innovation takes place on the team that is building the product.  If Alexa as a product needs to solve hard problems in the voice space, then the Alexa team will hire the requisite people to solve those problems.  If they solve those problems for their team and ship, then they have potentially built a category defining product (Alexa, Go, AWS, etc.).

## Proposed Solution

### Transformative products require organizational transformation

We can begin to address the innovation outsider problem by centralizing some of our existing resources on to product teams.  This will change expectations, incentives and outputs in a few significant ways:

1.	**Automatic alignment of research to product.**  No research work is done in a complete vacuum.  This means that far less time is spent solving technological problems that have no viable path to being productized.

2.	**Realistic assessments of what is hard.**  In a vacuum, it is very difficult to calibrate when an achievement is valuable.  The gap between academic and real-world performance is often vast and without Product setting the finish line it is likely that technical “achievements” have achieved very little.  This is why so many POCs fail to ever mature into something more valuable.

3.	**Higher Say/Do ratio.**  By eliminating the search for technological product market fit, we can focus researchers’ efforts on problems that will have direct impact on products.  This means that the time spent shopping ideas and billable hours around to product teams can be eliminated completely.  Less slideware and more software.

4.	**Innovation is tied to making the delivery of healthcare Better/Cheaper/Faster.**  When tied to a product, we can directly evaluate the impact that research will have on the delivery of healthcare.  We can change the focus from solving technological problems to solving healthcare problems.

In practice this likely means transferring the delivery parts of ATC, IRD and others to a group that is directly tasked with shipping products that could leverage their skills.
 
This should *not* be a foreign idea to us given our embrace of scrum teams.  The core tenets of scrum teams are that they are cross-functional, have joint accountability, and have no sub-teams[^13].  All of these tenets stand in stark contrast to an organization structured around innovation outsiders.  Note that given our matrixed structure, we frequently fail on this front by splitting out product, UX, etc. as well.

### But what about the risk?

Jeff Bezos credits much of Amazon’s success on their willingness to fail on products that potentially have outsized returns.  He notes this in a recent shareholder letter:

> We all know that if you swing for the fences, you're going to strike out a lot, but you're also going to hit some home runs. The difference between baseball and business, however, is that baseball has a truncated outcome distribution. When you swing, no matter how well you connect with the ball, the most runs you can get is four. In business, every once in a while, when you step up to the plate, you can score 1,000 runs. This long-tailed distribution of returns is why it's important to be bold. Big winners pay for so many experiments[^14].

In order to leverage and drive technological advancement we need to put our best to work on truly transformative products.  If we simply move these resources onto existing product teams they may have a more direct impact, but will necessarily take less risk.  They will take less risk partially because of the legacy matrixing structure that we employ for most products, but also because it will not make sense to disrupt existing timelines and revenue streams.

As such, if we hope to focus our efforts on truly transforming healthcare we must target the types of projects where we can take a big risk but if we win, we can hit that 1000 run home-run.

## Transformative Products

The intention of this document is not to prescribe what are the most important products that we could be building, but rather to suggest the sort of reorganization that is most likely to result in transformative products.  The products themselves are a strategic question that requires a lot of thought, evaluation and vision that are beyond the scope of any one person.  The pillars that are presented here are just one suggestion of how we might align many of our existing efforts to potential products that could work in an integrated fashion to deliver something transformative.

Transformative products need big ambitions that will take a lot of time and effort to eventually achieve.  That said, they should also have tangible steps along the way to measure progress, deliver incremental value, transfer learnings to other projects and most importantly a chance to kill them if necessary.

This proposal suggests three major product categories that we could pursue.  They are characterized by a long-term vision (3-5 years) that if achieved would be highly valuable, opportunities to deliver incremental value along the way and they are only possible if we innovate technologically.

### Healthcare engineering tools of the future
This would be a set of internally facing efforts that utilize and improve the latest technologies to make our developers more effective.  It would encompass the work being done by HCP, Public Cloud, Model Ops, and others to make our developers more effective and bring us out of the batch era.

### Realtime Healthcare
This pillar would drive us to making it possible to consume as many healthcare services as possible in real-time.  Through automation, human augmentation, real-time predictions and services we can deliver the healthcare experience of the future.  This would utilize our efforts into streaming, voice, chatbots, and modern infrastructure.

### 1-Click Healthcare
This pillar would drive us to make buying healthcare as easy as purchasing a book via 1-click on Amazon.  It would require pushing ourselves to be make data available in real-time, to trust our predictions around costs, and to face into what competing with the likes of Amazon really means.

## Conclusion

This document lays out a proposal for how we can reorganize our investments earmarked for innovation into a structure that has higher potential payoffs and higher probability of success. 

In order to do so we will need to eliminate innovation outsiders.   We must integrate them into cohesive product teams where their innovation is directly aligned to product success.  We need to leave behind our legacy matrixing structure and combine all necessary expertise to build and ship a product under one GM.

We need to embrace the core of scrum and align our assets to try and hit 1000 run home runs.  This will require acknowledging that there is risk in doing so but there are also outsized rewards.  Embracing the possibility of failure will be a major cultural change.  In order to make this palatable, we should decouple our initial efforts from existing lines of business and target big bold new ideas.

Finally, we need to reframe the problem.  We have to stop asking whether we can achieve some technical outcome – if we can’t then we should be replaced by people that can – and start asking whether we can build transformative products.



[^3]: https://hbr.org/2019/07/why-innovation-labs-fail-and-how-to-ensure-yours-doesnt
[^12]: https://www.bizjournals.com/twincities/blog/in_private/2014/10/optums-snapvet-online-diagnosis-pets-veterinary.html
[^13]: https://hbr.org/2016/05/embracing-agile
[^14]: https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.html