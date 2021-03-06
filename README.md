<h1 align="center">Google Summer of Code 2021 <img src="Assets\wcoding.gif" width="80"></h1>
<h2 align="center">MINERVA</h2>

![Logo](/Assets/GSoC-FOSSology.png)

<p align="center">
        <img src="Assets\developer.gif" width="800" height="400">
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

Why there is a need to generate a dataset?
1. To implement any Machine learning/Deep learning algorithm we need a better and bigger dataset of SPDX Licences. Due to the lack of dataset currently, all the 10 algorithms which have been tested on Atarashi are restricted to 59% accuracy. But unfortunately, there exists no such dataset for open source licenses on the web.
2. Advanced Architectures and algorithms such as LSTMs, GRU, BERT, WordNET, etc. require huge volumes of the dataset before achieving the ability to outperform the accuracy of even traditional algorithms such as TF-IDF, n-gram, etc. 
3. Licenses differ from traditional corpora, because of which 50-60% keywords are similar in any two licenses, and if the licenses have the same license heading but different versions, they're around 90% similar.

There was a loose implementation of n-gramming different permutations and combinations of license text paragraphs. Ref: [SPDX-OSS Dataset](https://github.com/hastagAB/SPDX-OSS-Dataset). This method needed further improvement to make the dataset more accurate and realistic.


The main idea was to initially split the licenses into different permutations and combinations of license text paragraphs maintaining a sliding window approach and use these existing files as a baseline model for further manipulating and generating texts. Added FOSSology Nomos agent STRINGS.in regex to the files.

<p align="center">
        <img src="Assets\work.gif" width="400" height="300">
</p>

### 1. DOWNLOADING RECENT RELEASED LICENSE FROM SPDX RELEASED JSON TO TXT FILES 
SPDX License List is a list of commonly found licenses and exceptions used in free and open or collaborative software, data, hardware, or documentation, and releases are done on a quarterly basis (more or less) at the end of January, April, July, and October. SPDX Licenses were manually downloaded to txt format from [license-list-data/text/](https://github.com/spdx/license-list-data/tree/master/text).
Licenses JSON format can be downloaded from [license-list-data/json/](https://github.com/spdx/license-list-data/tree/master/json)

I worked on a script to download the licenses from SPDX, SPDX-exceptions, FOSSology Database directly to txt format by passing JSON filepath of new release. Script : [download](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Download-licenses-Script)

For SPDX licenseListVersion: 3.13, licenses downloaded are : [files](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Original-SPDX-Dataset)
</br>
Original FOSSology db licenses <i>(SPDX licenses are subset of licenses present here)</i> : [files](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Original-DB-Foss-Dataset)

#### SPDX recent release : [SPDX](https://spdx.org/licenses/licenses.json)
```
 python ./Download-licenses-Script/spdx.py
```
#### SPDX-exceptions recent release : [SPDX-exceptions](https://spdx.org/licenses/exceptions.json)
```
 python ./Download-licenses-Script/exceptions.py
```
#### Licenses in Fossology Database : [licenseRef](https://raw.githubusercontent.com/fossology/fossology/master/install/db/licenseRef.json)
```
 python ./Download-licenses-Script/database-foss.py
```

### 2. GENERATED FILES THROUGH INITIAL SPLIT
The basic idea was n-gramming license text paragraphs such that we are able to maintain a sliding window, i.e for a licene with 4 paragraphs, all the different files that I wanted to generate were - <i>para1, para2, para3, para4, para1+para2, para2+para3, para3+para4, para1+para2+para3, para2+para3+para4, para1+para2+para3+para4.</i>
<i>Not para1+para3, para1+para3+para4, etc. because the structure of licenses needs to be maintained.</i>
```
 python ./Script-Initial-Split/initial_split.py
```
Script : [initial_split](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Script-Initial-Split)
</br>
Files : [SPDX](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Split-SPDX-licenses)
</br>
Files : [FOSSologyDatabase](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/Split-DB-Foss-Licenses)

### 3. GENERATED FILES BY ADDING REGEX TO FILES SPLITS
For license check and new dataset generation which satisfies each and every condition of regex for a license file, I used string generators through free and open-source libraries such as [xeger](https://pypi.org/project/xeger/), [intxeger](https://pypi.org/project/intxeger/) and whosoever comes lexicographically closer to the existing datasets in our database used with a threshold value being considered so that randomness in string generation of licenses can be kept at a minimum. It has to be done lexicographically since relevance to existing datasets is maintained.

<p align="center">
        <img src="Assets\regexsplit.png" width="400">
</p>

I have extracted regex from STRINGS.in file, scripts, extracted regex-csvs can be found in [STRINGSin-Regex-Extraction](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/STRINGSin-Regex-Extraction).

### 4. HANDLING REGEX EXPANSION
To the regex extracted from STRINGS.in file major task was to handle expansions i.e .{1,32}, .{1,64}. There were 3 cases considered, to generate ambiguous characters, replacing with an empty string, or generating a sequence of words from the license itself such that it holds proper meaning to it. Ambiguous characters were straightaway rejected after discussion with mentors. Validated the generated files from the second and third approaches using NOMOS and observed that the third approach results are drastically better than the second approach. 
So for the generating sequence of words, I worked on two algorithms and integrated them with the existing codebase. 

A. NGRAM
</br>
<i>(basically a set of co-occurring words within a given window)</i>
</br>
B. MARKOV
</br>
<i>(As an extension of Naive Bayes for sequential data, the Hidden Markov Model provides a joint distribution over the letters/tags with an assumption of the dependencies of variables x and y between adjacent tags.)</i>

Added "Multiprocessing" to the Script to speed up the process of data generation.

Codebase : [Ngram](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/ngram)
</br>
To generate licenses with ngram expansion:
```
 python ./ngram/licenses.py
```
Codebase : [Markov](https://github.com/fossology/Minerva-Dataset-Generation/tree/main/markov)
</br>
To generate licenses with ngram expansion:
```
 python ./markov/markov_licenses.py
```

After getting validated by NOMOS, Ngram regex expansion performed better than Markov expansion.

### 5. VALIDATION OF FILES GENERATED
We use Nomos to identify the licenses, either with license headers with which its regex matches or labels such as Unclassified licenses, No License found, Public-domain, Restricted, and so on. This is a baseline validation for the resulting text files. Terminal command to run this will be  : 
```
 sudo nomos -J -d <folder_with_files>
```
And to use multiple cores to validate files (here I am using 3 cores) :
```
 sudo nomos -J -d <folder_with_files> -n 3
```
After the validation files were segregated into different folders, with license headers as folder names. 

This is a brief overview of the project.

<p align="center">
        <img src="Assets\project_overview.png" width="800" height="500">
</p>

The entire codebase has now been moved to FOSSology : [Minerva-Dataset-Generation](https://github.com/fossology/Minerva-Dataset-Generation)

### 6. ADDED NOISE TO DATASET - AUGLY IMPLEMENTATION
I have added noise to the generated dataset using [Augly](https://github.com/facebookresearch/AugLy) for increasing both the size and the diversity of labeled training data which also helps to build robust ML models. Augly offers transformations in both function and class formats, as well as intensity functions to help us understand how intense a transformation is (based on the given parameters). AugLy can also create important metadata that will assist in understanding how your data was altered.

<p align="center">
        <img src="Assets\augly.PNG" width="800" height="400">
</p>

Tested on a license over : [Colab](https://colab.research.google.com/drive/1Tz1EXJffeoBj5lguDYse5UHP0EhXIemE?usp=sharing) 
</br>
After a discussion with the mentors, it was concluded that the licenses will be manipulated using <i>SIMULATE TYPOS and REPLACE SIMILAR CHARACTERS</i>

<h1 align="center">👨🏻‍🏫 DELIVERABLES</h1>

1. Scripts to Download licenses (txt) from JSON file.
2. License Generated using Sliding Window Approach <i>(that is initial split)</i>
3. Implemented Multiprocessing to License Generation. 
4. Scripts handling Regex Expansion using both the algorithms <i>(i.e Markov and Ngram)</i> 
5. Shifting the Codebase of Atarashi <i>(Discussed and implementation in progress)</i>
5. Augly Implementation on Licenses <i>(Tested and will be continuing after training on generated dataset is done.)</i>

<h1 align="center">🚀 FUTURE PLANS</h1>

1. Normalisation of text files generated.
2. Writing custom hooks for repetitive code.
3. Use the generated data for training ML models.
4. Experiment with advanced NLP Algorithms for license generation and validation techniques.

<h1 align="center">📚 Things I learned from Google Summer of Code</h1>

1. To write optimized codes.
2. Explored various NLP algorithms tested and implemented them.
3. Sharpened my skill of GIT
4. Learned the importance of time management as well as perfect deliverables.
5. Worked on my scripting skills.
6. Understood the importance of constructive discussions with mentors and peers.
7. Improved my documentation skill
8. Various open-source licenses and their importance in codes, projects, and software.

<img align="right" alt="GIF" src="Assets\ilovecoding.gif" width="400" />

# Let's get connected!
- [Drop me a mail](mailto:shreya.out@gmail.com)
- [Connect with me on LinkedIn](https://www.linkedin.com/in/shreyasingh05/)
- [Give a follow on GitHub](https://github.com/SinghShreya05)
- [Instagram](https://www.instagram.com/nottonlyyours/)
