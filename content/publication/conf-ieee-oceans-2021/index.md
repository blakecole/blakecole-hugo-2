---
title: 'Surface vessel collision avoidance in MOOS-IvP using a geodetic unscented Kalman filter'

# Authors
# If you created a profile for a user (e.g. the default `admin` user), write the username (folder name) here
# and it will be replaced with their full name and linked to their profile.
authors:
  - admin
  - Michael R. Benjamin
  - Supun Randeni

# Author notes (optional)
author_notes:
  - 'Equal contribution'
  - 'Equal contribution'

date: '2021-09-20T00:00:00Z'
doi: ''

# Schedule page publish date (NOT publication's date).
publishDate: '2017-01-01T00:00:00Z'

# Publication type.
# Accepts a single type but formatted as a YAML list (for Hugo requirements).
# Enter a publication type from the CSL standard.
publication_types: ['paper-conference']

# Publication name and optional abbreviated publication name.
publication: In *IEEE OCEANS 2021, San Diego - Porto*
publication_short: In *IEEE-OCEANS 21*

abstract: This paper describes the design and implementation of a low-cost collision avoidance system, designed primarily for use on small and medium-sized autonomous surface vehicles (ASVs). The proposed methodology leverages real-time information broadcast via the Automatic Information System (AIS) messaging protocol, in order to estimate the position, speed, and heading of nearby vessels. The state of each target vessel is recursively estimated in geodetic coordinates using an Unscented Kalman Filter (UKF). Once identified, each vessel is avoided in accordance with the International Regulations for Preventing Collisions at Sea (COLREGs). This capability is enabled by MOOS-IvP, a behavior-based autonomy middleware that is able to make navigation decisions by weighing the relative importance of multiple competing objectives. For the purposes of collision avoidance, each target vessel produces a two-dimensional objective function which increases the cost of heading and speed combinations that will result in a collision or near-miss event. However, the primary mission behaviors remain active, allowing the IvP solver to choose an optimal combination of vessel speed and heading which drive the vehicle toward a desired state while simultaneously minimizing the risk of collision. It is shown through field testing that the proposed framework is an effective, robust means of collision avoidance.

# Summary. An optional shortened abstract.
summary: Field validation of an AIS-based collision avoidance algorithm enabled by the geodetic unscented Kalman Filter.

tags:
  - collision avoidance
  - unscented Kalman filter
  - target tracking
  - MOOS-IvP
  - autonomy

# Display this page in the Featured widget?
featured: true

# Custom links (uncomment lines below)
# links:
# - name: Custom Link
#   url: http://example.org

url_pdf: ''
url_code: ''
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: 'https://ieeexplore.ieee.org/document/9705900/'
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  caption: 'An autonomous surface vehicle used to validate the proposed AIS-based
collision avoidance protocol.'
  focal_point: ''
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
#projects:
#  - example

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
---
