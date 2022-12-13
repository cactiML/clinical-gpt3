# clinical GPT-3
This notebook is a small (work-in-progress) exploration of [OpenAI GPT-3](https://openai.com/api/) abilities to perform various 
Natural Language Processing tasks in the **clinical domain, on patient medical records**. 

These include:
- [Medical Entity Extraction](https://github.com/cactiML/clinical-gpt#entity-extraction)
- [Clinical Text Summarization](https://github.com/cactiML/clinical-gpt#summarization-of-clinical-text)
- [Clinical Question Answering](https://github.com/cactiML/clinical-gpt#clinical-question-answering)

If you have any comments or additions, please feel free to reach out to me on Twitter [@samarthrawal](https://twitter.com/samarthrawal), via email (in my [GitHub profile](http://github.com/samrawal)), or feel free to submit a PR with any interesting findings!

Some notes:
- This is meant to just be a high-level exploration of possible capabilities -- several of these results are cherry-picked in the sense that they required a couple runs/reformulating of the prompt.
- This README file is basically the Jupyter Notebook in this repository with some small (mostly formatting-related) changes. 
- If you play around with the notebook, make sure submitting queries to the OpenAI API meets the data use agreement for whatever data you are using.

<p align="center">
  <img src="icon.png" alt="DALL-E generated icon" width="100px"/>
</p>



## Entity Extraction

### Medication Entity Extraction


```python
text = """
Mr. Henderson has PMH of COPD diagnosed in 2009 (minimally symptomatic, uses albuterol PRN); 
MI in 2015 with stent placement in 2015 (on ASA and clopidogrel); unspecified anxiety disorder 
(diagnosed by PCP in 2018 (on Prozac 10mg qd).
"""
```

#### Medication Entity Extraction


```python
res = query(
  "Extract all medications from the text and the dosage and frequency for each if specified", 
  text
)
```

    Medications:
    - Albuterol (as needed) 
    - ASA (unknown dosage, frequency unknown) 
    - Clopidogrel (unknown dosage, frequency unknown) 
    - Prozac (10mg per day)


#### Medication Generic/Brand Name Resolution


```python
drugnames = query(
  "Extract all medications from the text and print one medication per line", 
  text
)
```

    Albuterol
    Aspirin
    Clopidogrel
    Prozac



```python
res = query(
  "For each of the drugs in the list, identify if it is the generic name or Brand name of the drug.", 
  drugnames
)
```

    Albuterol - Generic Name
    Aspirin - Generic Name
    Clopidogrel - Generic Name
    Prozac - Brand Name



```python
brands = [x[0].strip() for x in list(
    filter(lambda x: "Brand" in x[1], [line.split("-") for line in res.split("\n")])
)]
print(f"{brands = }")
res = query(
  "Give me the generic name for", 
  ",".join(brands)
)
```

    brands = ['Prozac']
    Fluoxetine


### Clinical Entity Extraction


```python
res = query(
  "List the medical conditions mentioned in this selection",
  text
)
```

    1. COPD (Chronic Obstructive Pulmonary Disease)
    2. MI (Myocardial Infarction)
    3. Anxiety Disorder



```python
res = query(
  "List the medical conditions mentioned in this selection and when each occured, if mentioned",
  text
)
```

    Medical Conditions:
    
    1. COPD (Chronic Obstructive Pulmonary Disease) - diagnosed in 2009
    2. MI (Myocardial Infarction) - in 2015 
    3. Anxiety Disorder - diagnosed by PCP in 2018


## Summarization of Clinical Text


```python
text = """
REVIEW OF SYSTEMS:
General: Discomfort and mild distress.
Cardiovascular: Endorses chest pain, feeling of pressure in the chest. Denies palpitations.
Respiratory: Denies cough, sputum production, or hemoptysis
Gastrointestinal: Denies nausea, vomiting, abdominal pain, or change in bowel habits.
Genitourinary: Denies urinary frequency, urgency, or dysuria.
Musculoskeletal: Denies joint pain, swelling, or weakness 
Skin: Denies noticing any rash, lesions, or changes in skin color.
Neurological: Denies headaches, dizziness, or changes in mental status.
"""
```


```python
res = query(
  "List the positive signs detailed in the following medical Review of Systems",
  text
)
```

    Positive Signs: Discomfort, mild distress, chest pain, feeling of pressure in the chest.


## Clinical Question-Answering


```python
text = """
HISTORY OF PRESENT ILLNESS:
Roger Henderson is a 65 yo male with PMH of COPD, MI s/p stent placement 2015, anxiety, 
presenting to CMC ED complaining of shortness of breath and chest pain for past 30 minutes. 
Patient reports he was at home on his couch watching TV when he began experiencing pain 
which he describes as a sharp, stabbing sensation in the center of his chest. Patient 
concurrently began experincing shortness of breath and feeling of pressure in his chest. 
He called 911 and EMS was sent to his house to pick him up and bring him to ED. 

On arrival to ED, patient was diaphoretic and in pain, and was gasping for air while 
answering questions. Patient describes the pain as 8/10 in severity and has not changed in 
character or intensity over the past 30 minutes. Patient denies any radiation of pain beyond 
chest. Patient denies any N/V. Vitals were as follows -- BP 130/82; HR 110 bpm;  
RR 24 breaths/min; O2 Sat 98% on room air.

At ED, aspirin and nitroglycerin was administered with minimal relief in symptoms. EKG was 
performed which did not indicate any ST/T wave changes. Troponin level non-elevated. CBC
and CMP results were all within reference range. 
"""
```


```python
res = query(
  "What medications were given to patient on arrival?",
  text
)
```

    Chest x-ray was negative for any acute process and patient was given oxygen to help with his breathing. 
    
    Medications given to patient on arrival included:
    - Aspirin
    - Nitroglycerin



```python
res = query(
  "Why did patient call 911?",
  text
)
```

    The patient called 911 because he was experiencing chest pain and shortness of breath which was severe enough to warrant medical attention.



```python
res = query(
  "Who brought the patient in to the ED?",
  text
)
```

    EMS brought the patient in to the ED.

