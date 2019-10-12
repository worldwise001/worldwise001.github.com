---
layout: page
title: USENIX Enigma 2020 Submission
permalink: /cfp/enigma2020
---

## Abstract

Third-Party Integrations: we all have to use them, whether we like it or not. Various paradigm shifts in today's computing environment have contributed to this reality: microservice architecture is becoming increasingly common with the democratization of cloud computing power, and more and more organizations are realizing that it's often simply easy to pay for a particular service instead of building it from scratch. However, threat risk analysis about integration interactions--especially when it relates to the sharing of information--can be extremely time-consuming if not grueling for a lot of folks in this space; there often are not just financial contract reviews, but also legal contract reviews, vendor security assessments, security/privacy review processes, etc. and that's not including the potential complexity of the actual engineering integration! All of this can create friction and reduce the helpfulness of your security organization.

In this talk we will briefly cover a typical system in place in an organization; we will then identify common patterns of mistakes that can happen in this pipeline, and propose solutions based on what we've tried in this space. The aim of this talk is to show that the boring, grueling work in security is just as important as exciting 0-days, and we hope to also show that there is still new exciting metrics and incident response systems you can derive from these processes. We will also argue that it is through these improvements, that you will have not just a more holistic, but also more consistent threat risk map of your overall organization.

## Additional Presentation Details
Main takeaway point: Third-party integrations can be really difficult for organizations to deal with, and later reason through. We propose ways to streamline and structure this process so that organizations can get a more holistic view of their data ingress/egress points.

### Brief outline
- Brief definition of third-party integration (i.e. vendor relationship) and other related terminology (3 min total)
  - some outward examples of integrations gone wrong, to illustrate why we need to pay attention to this
  - Outline of typical process, and some examples of sad feedback from other teams
- Highlighting our struggles or other technical difficulties in the space (5 mins)
  - Modeling ingress/egress threat, understanding whitelists/blacklists
  - Every Vendor and the Kitchen Sink
  - Firehose of security/privacy reviews
  - No vendor security questionnaire to rule them all
  - Limited access control mechanisms
- Technical solutions (10 mins)
  - Giving users the means to take on security pre-assessment responsibility
    - Vendor transparency
    - Providing searching tools
    - Surfacing prior art/documentation
  - Tying domains to vendors/products, and linking these to apps/services via integrations
    - Engineering augmentation to your proxy/access control management system
  - Improving the assessment process
    - Streamlining requirements, by narrowing down the base set of security questions
    - Tiering vendors based on security seriousness/data sensitivity
    - Examining off-the-shelf services/APIs (vendors to assess vendors)
    - May never be perfect, but might be Good Enough
- Conclusion/Thinking about the future (2 mins)
  - New data sources to link to your data threat risk map
  - Some statistics on how/where we've improved our assessment times and process analysis times
  - Can we push vendors to provide more standalone/in-house software?


### Other notes
I really love giving entertaining/funny talks, and I really like using imagery and analogies extensively in my presentations. I hope to get the audience laughing/following along as much as possible on this seemingly dry/boring topic.

## Speaker Bio
Sarah is a software engineer on a privacy engineering team at Square. Her background includes 4+ years of industry experience in security/privacy infrastructure design and engineering, and 4 years of academic privacy research. She has a variety of event organizing and speaking experience; highlights include speaking at and co-organizing BSidesSF 2019, organizing and presenting a 300+ person CTF workshop at Grace Hopper, and giving a series of funny lightning talks on infrastructure security and privacy challenges.

She also has given talks as a hologram, and in general never takes herself seriously.

She can be followed for cats and tech humor on Twitter: @worldwise001.

## Topics
- Education
- Security process (e.g., threat modeling)
- Usable security and privacy

# Reviews

## Review A

- Overall recommendation: 5. Strong accept
- Proposed content quality: 5. Excellent
- Speaker rating: 5. Excellent

## Comments
Sarah is an excellent speaker and presenter. I find this topic to be relevant, interesting, and compelling.

The outline is well thought through and it's clear that Sarah is experienced with real world cases and examples.

## Review B

- Overall recommendation: 3. Weak accept
- Proposed content quality: 3. Average
- Speaker rating: 4. Good

## Comments
The risk posed by third-parties in system security is large and complex. There's strong potential for this talk to help shine a light on this lightly covered terrain, although the abstract doesn't completely fulfill that promise. Third-party risk being difficult to mitigate isn't noteworthy, and some of the solutions proposed are too vaguely stated to evaluate. When I read a point like "new data sources to link to your data threat risk map" or "engineering augmentation to your proxy/access control management system", it's hard to get a feel for what the speaker's actually going to say. I'm hopeful that when the points in the abstract are more clearly stated, the talk will be a good fit for the conference.

I've seen Sarah present twice before in person, and I know she can give engaging talks that are personal, humorous, and informative.

## Review C

- Overall recommendation: 5. Strong accept
- Proposed content quality: 4. Good
- Speaker rating: 3. Average or Unknown

## Comments
I love this topic. New apps built on top of cloud services have shifted the buy/build decision overwhelmingly in favor of buy for everything outside of what they believe is their core product. However, these vendors have extremely valuable data and the principles and tools for asserting any sort of security properties are still very specific to each app, broadly speaking.

I don't have any suggestions for improvement, the basic outline looks appropriate.
