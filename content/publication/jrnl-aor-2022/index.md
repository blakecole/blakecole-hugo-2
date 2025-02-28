---
title: "Unscented Kalman filter for long-distance vessel tracking in geodetic coordinates"

authors:
- admin
- Gabriel Schamberg

author_notes:
- "[Corresponding author] Conceptualization, Methodology, Validation, Writing."
- "Analysis, Supervision, Proofreading"

date: "2022-07-01T00:00:00Z"
doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: "2017-01-01T00:00:00Z"

# Publication type.
# Accepts a single type but formatted as a YAML list (for Hugo requirements).
# Enter a publication type from the CSL standard.
publication_types: ["article-journal"]

# Publication name and optional abbreviated publication name.
publication: "Applied Ocean Research"
publication_short: "Appl. Ocean Res."

abstract: This paper describes a unique instantiation of the unscented Kalman filter (UKF) that is particularly well-suited to long-distance surface vessel tracking. The proposed filter leverages a nonlinear process model to predict the position, speed, and heading of targets directly in geodetic coordinates, obviating the need for intermediate coordinate frame transformations, and enabling the use of a simple linear observer. These features differentiate the proposed filter from prior architectures, most of which require the definition of a planar coordinate system in order to describe the linearized state kinematics in an easily differentiable form. Reliance upon local Earth-tangent frames is acceptable in confined operating regions, but incurs substantial estimation error when the target is far from the local origin. It is shown that this error grows with distance-cubed from the local origin. By tracking targets directly in geodetic coordinates, the proposed filter is able to achieve more consistent performance, and a three-fold reduction in computational cost. A series of numerical simulations and field exercises are performed to ground these claims, and verify the efficacy of the proposed filter.


# Summary. An optional shortened abstract.
summary: A UKF-based tracking filter that does not require linearization of target kinematics, eliminating unnecessary coordinate frame transformations.

tags:
- target tracking
- unscented Kalman filter
- geodetic

featured: true

# links:
# - name: ""
#   url: ""
url_pdf: ''
url_code: ''
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: 'https://www.sciencedirect.com/science/article/abs/pii/S0141118722001468'
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: 'The proposed filter enables target tracking directly in geodetic coordinates, eliminating intermediate coordinate frame transformations.'
  focal_point: "Smart"
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: example
---
