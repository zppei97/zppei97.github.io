---
title: Towards Better Estimating Facility-level Methane Emissions with Spaceborne Imaging Spectrometers

event: AGU 2023
event_url: https://www.agu.org/Fall-Meeting

location: Moscone Center, South, Poster Hall A-C
address:
  street: 747 Howard St
  city: San Francisco
  region: CA
  postcode: '94103'
  country: United States

summary: We will discuss the Iterative Lognormal Matched Filter (ILMF) algorithm.
abstract: Rapidly reducing methane emissions from the fossil fuel industry, particularly oil and gas (O&G) operations, is an effective means of mitigating global warming. There remains significant uncertainty in the magnitude and distribution given the unexpected and intermittent venting and leaks from the energy sector infrastructure. High pixel resolution (<100 meters) imaging spectroscopies enable attribution to specific facilities. Matched filter (MF) algorithm has been widely used for such instruments to detect and quantify methane emissions since it enables methane column concentration enhancement (in ppb) mapping. Unlike physics-based methods (e.g. DOAS), MF reduces the computational cost by simplifying the atmospheric radiative transfer model. However, the MF algorithm is unfaithful to the physics of gas detection and is only applicable to weak plumes due to the nonlinear model. In this presentation, we will discuss the Iterative Lognormal Matched Filter (ILMF) algorithm we are developing, to robustly and unbiasedly quantify methane enhancement. ILMF avoids the errors of Taylor's first-order expansion by logarithmically modeling the background spectra and obtains more accurate means and covariances of the background spectra through multiple iterations. We will focus on the performance of the ILMF method and MF in three sets of simulation experiments, including end-to-end simulations. We will further discuss the expected performance of both methods in typical methane hotspot regions. Finally, we will discuss the uncertainties of ILMF and subsequent improvements that can still be made.

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
date: '2023-12-12T08:30:00Z'
date_end: '2023-12-12T12:00:00Z'
all_day: false

# Schedule page publish date (NOT talk date).
# publishDate: '2017-01-01T00:00:00Z'

authors:
- Zhipeng Pei
- Ge Han
- Cuihong Chen
- Huiqin Mao
- Xin Ma
- Wei Gong
tags:
- Methane detection

# Is this a featured talk? (true/false)
featured: false

image:
  caption: 'with Kelly Neill'
  focal_point: Right

# links:
#   - icon: twitter
#     icon_pack: fab
#     name: Follow
#     url: https://twitter.com/georgecushen
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Markdown Slides (optional).
#   Associate this talk with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: []

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

<!-- {{% callout note %}}
Click on the **Slides** button above to view the built-in slides feature.
{{% /callout %}}

Slides can be added in a few ways:

- **Create** slides using Hugo Blox Builder's [_Slides_](https://docs.hugoblox.com/reference/content-types/) feature and link using `slides` parameter in the front matter of the talk file
- **Upload** an existing slide deck to `static/` and link using `url_slides` parameter in the front matter of the talk file
- **Embed** your slides (e.g. Google Slides) or presentation video on this page using [shortcodes](https://docs.hugoblox.com/reference/markdown/).

Further event details, including [page elements](https://docs.hugoblox.com/reference/markdown/) such as image galleries, can be added to the body of this page. -->
