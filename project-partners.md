---
layout: page
title: Project Partners
description: A listing of all the course staff members.
---

# Project Partners

Hello! Welcome to our website. We worked on all the projects together as a group for CS 184!

## Students

{% assign instructors = site.staffers | where: 'role', 'Project Partner' %}
{% for staffer in instructors %}
{{ staffer }}
{% endfor %}

[//]: # ({% assign teaching_assistants = site.staffers | where: 'role', 'Teaching Assistant' %})

[//]: # ({% assign num_teaching_assistants = teaching_assistants | size %})

[//]: # ({% if num_teaching_assistants != 0 %})

[//]: # (## Teaching Assistants)

[//]: # ()
[//]: # ({% for staffer in teaching_assistants %})

[//]: # ({{ staffer }})

[//]: # ({% endfor %})

[//]: # ({% endif %})
