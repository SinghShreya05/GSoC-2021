<h1 align="center">Google Summer of Code 2021 <img src="Assets\wcoding.gif" width="80"></h1>
<h2 align="center">MINERVA</h2>

![Logo](/Assets/GSoC-FOSSology.png)

<p align="center">
        <img src="Assets\developer.gif" width="400">
</p>


![ViewCount](https://views.whatilearened.today/views/github/SinghShreya05/GSoC-2021.svg)
![GitHub](https://img.shields.io/github/followers/SinghShreya05?style=social)
![GitHub Stars](https://img.shields.io/github/stars/SinghShreya05?style=social)

## Google Summer of Code 2021 🚩 Report: "MINERVA" <img src="Assets\wcoding.gif" width="80">

<h1 align="center">CONTRIBUTIONS  <img src="https://media.giphy.com/media/dxn6fRlTIShoeBr69N/giphy.gif" width="30"></h1>
<h2>MINERVA DATASET GENERATION</h2>
<p><i>To create/generate OSS License dataset for Atarashi </i></p>

- Codebase: [GitHub](https://github.com/fossology/Minerva-Dataset-Generation)
- Documentation: [Wiki](https://fossology.github.io/gsoc/docs/2021/minerva/)

Why there is need to generate dataset?
1. To implement any Machine learning/Deep learning algorithm we need a better and bigger dataset of SPDX Licences. Due to the lack of dataset currently, all the 10 algorithms which have been tested on Atarashi are restricted to 59% accuracy. But unfortunately, there exists no such dataset for open source licenses on the web.
2. Advanced Architectures and algorithms such as LSTMs, GRU, BERT, WordNET etc require huge volumes of dataset before achieving the ability to outperform the accuracy of even traditional algorithms such as TF-IDF , n-gram etc. 
3. Licenses differ from traditional corpora, because of which 50-60% keywords are similar in any two licences, and if the licences have the same licence heading but different versions, they're around 90% similar.

There was a loose implementation of n-gramming different permutations and combinations of license text paragraphs. Ref: [SPDX-OSS Dataset](https://github.com/hastagAB/SPDX-OSS-Dataset). This method needs further improvement to make the dataset more accurate and realistic.


