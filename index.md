---
layout: page
title: about
---
### whoami

I am a tech person, doing security/privacy things, based in Toronto (formerly bay area). My pronouns are she/her. I dislike photos of myself. I have strong opinions on software development and security/privacy but I value collaboration. I like really interesting and obscure facts, especially pertaining to cultural, culinary, linguistic, and unrelatedly, electronics/computer history.

### fun stuff

![messy desk](/images/desk.jpg)

For fun I work on hobbyist software projects, electronics and 3D printing. I also enjoy cooking, arts/crafts, and playing the accordion.

Some current projects include building a service to analyze linkages between research papers, a nixie doom clock and some custom housing for the timelapse cameras for my 3D printer. In the future when everything reopens, I intend to document my dad's expertise and work on restoring old TVs/radios.

I sometimes [play (video) games](https://steamcommunity.com/id/worldwise001), and sometimes [stream my gameplay](https://www.twitch.tv/worldwise001).

I have [cat(s)](https://www.instagram.com/shh_furbabies).

I run some [Tor relays](/tor/).

### what am i up to

{% for post in site.posts limit:1 %}
#### [{{ post.title }}]({{ post.url }})

{{ post.date | date: "%b %-d, %Y" }}

{{ post.excerpt }}

{% endfor %}

[more...](/log/)

### work stuff

I wear, or have worn, a number of hats. My current title is Engineering Manager, but in the past I've jumped into software engineering, security/secops, and devops/SRE problems. I no longer write software day to day; previously my responsibilities included designing software systems and evaluating security systems and processes, and I still on occasion pull on that skillset. In general I provide roadmap leadership/guidance, grow careers, mentor and advise engineers, and interview/screen possible candidates on a variety of axes including programming, security, privacy, leadership, and communication skillsets.

I am fairly flexible on the tech stack, having experience in the following:
- writing/modifying Linux drivers
- writing operating system core software
- writing highly reliable backend services
- machine learning research
- scaling data pipelines
- web/frontend development
- some minor exploits (including participating in CTFs)
- administering Linux servers/systems
- administering PKIs

I also love conferences and meetups:
- I have experience organizing 100-150-person LAN party events (including handling sponsors and marketing)
- I have experience recruiting, managing, and supervising 200-300 volunteers at 2200+-participant security cons (BSidesSF).
- I'm a reasonably accomplished technical speaker (including Grace Hopper and USENIX Enigma)
- I frequently am in the role of MC/organizer of small events (company-internal meetups)
- No one yet has asked me to keynote or MC larger meetups ;)

My research focus in a past life has been on privacy problems relating to big data, namely things like: stylometry and profile-linking problems. I am currently interested in data tracing and protection problems; specifically interested in user credential propagation, and scalable access control techniques on a per-field level as opposed to per-table, per-operation, or per-database level. I am also interested in scalable data identification and redaction techniques, which I find akin on the same difficult as spam identification problems.

You can see my full work history [as a resume](/resume.pdf) or on [LinkedIn](https://www.linkedin.com/in/shharvey).

### contact

I may be reached at s at shh dot sh . My GPG Key is 1BE6 766C DC52 439A 5722 DCA2 BDE4 3806 8A2B D353.

You can also interact with me on [Twitter](https://www.twitter.com/worldwise001).
