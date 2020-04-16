# Class diagram extraction from textual requirements using Natural language processing (NLP) techniques

## 作者:Mohd Ibrahim, Rodina Ahmad ,Department of Software Engineering University of Malaya 
## 出版年代:2010,IEEE

### RACE架構
![](https://i.imgur.com/hgyHuzs.jpg)
```

註解:
Stemming:詞幹提取(去字尾 i.e.,ed、ing、s...)

POS Tagger:詞性標記

Semantic Parser:語義解析

```

#### 選擇 OpenNLP 作為 parser
#### OpenNLP POS tagger (lexical) 做詞性標記

```
註解:NN=>專有名詞 VB=>動詞
NP=>名詞片語
```

#### OpenNLP Chunker (syntactic) chunks the sentence into phrases 

### RACE Stemming Algorithm
用來去除字尾綴詞
本文作者用C#自己寫了一個 

下圖是部分程式碼用來表示去除複數
![](https://i.imgur.com/BnZA4jF.jpg)

### WordNet
is used to validate the
semantic correctness
of the sentences generated
at the syntactic analysis.

### Concept Extraction Engine 
包含 OpenNLP parser, RACE stemming algorithm, and WordNet 三個模組
```
Step1: Use the requirements document as input.

Step2: Identify the stop words and save the result as
{Stopwords_Found} list.

Step3: Calculate the total number of words in the
documents without the stop words(summation), the number of
occurrences (O1) of each word, and then calculate the
frequency (Ƒ) of each word, as in
    F=O1/summation

Step4: Use RACE stemming algorithm module to
find the stemming for each word and save the result
in a list.

Step5: Use OpenNLP parser in [11] to parse the
whole document (including the stop words)

Step6: Use the parser output to extract Proper Nouns
(NN), Noun phases (NP), verbs (VB). And save it in
{Concepts-list} list.

Step7: Use Step2 and Step 6 to extract:
 {Noun phrases (NP)} - {Stopwords_Found} and
save results to {Concepts-list}

Step8: For each concept (CT) in {Concepts-list} if
{synonyms_list} contains a concept (CT2) which
have a synonym (SM,同義詞) which lexically equal to CT,
then CT and CT2 concepts are semantically related
to each other.

Step9: For each concept (CT) in {Concepts-list} if
{hypernyms_list} contains a concept (CT2) which
have a [hypernyms](https://baike.baidu.com/item/%E4%B8%8A%E4%B8%8B%E4%BD%8D%E5%85%B3%E7%B3%BB/5947743) (HM) which lexically equal to
CT, then CT2 “is a kind of“ CT. Then save result
as {Generalization-list}. 

```

### Class Extraction Engine 
使用output of 'concept extraction engine' and 應用 different heuristic rules to
extract the class diagram

#### class Identification Rules:
```
C-Rule1: If a concept is occurred only one time in
the document and its frequency is less than 2 %, then
ignore as class.

C-Rule2: If a concept is related to the design
elements then ignore as class. Examples:
“application, system, data, computer, etc…”

C-Rule3: If a concept is related to Location name,
People name, then ignore as a class. Examples:
“John, Ali, London, etc…”

C-Rule4: If a concept is found in the high level of
hypernyms tree, this indicates that the concept is
general and can be replaced by a specific concept,
then ignore as class. Examples: “user, object, etc.”

C-Rule5: If a concept is an attribute, then ignore as a
class. Examples: “name, address, number”

C-Rule6: If a concept does not satisfy any of the
previous rules, then it’s most likely a class.

C-Rule7: If a concept is noun phrase (Noun+Noun),
if the second noun is an attribute then the first Noun
is a class. The second noun is an attribute of that
class. Examples: “Customer Name” or “Book ISBN”

C-Rule8: if the ontology (if-used) contains
information about the concept such as relationships,
attributes, then that concept is a class. 
```

#### Attribute Identification Rules:
```
A-Rule1: If a concept is noun phrase (Noun+Noun)
including the underscore mark “_” between the two
nouns, then the first noun is a class and the second is
an attribute of that class. Examples
“customer_name”, “departure_date”.

A-Rule2: If a concept can has one value, then it’s an
attribute. Examples:”name, date, ID, address”. Based
on A-Rule2, we collected and stored a predefined list
including the most popular attributes to be used as a
reference in RACE system
```

#### Relationship Identification Rules:
透過verb analysis as input
```
R-Rule1: using step10 in the concept extraction
engine (section 4.4), all the elements in the
{generalization-list} will be transferred as
Generalization (IS-A) relationship.

R-Rule2: If the concept is verb (VB), then by
looking to its position in the document, if we can
find a sentence having (CT1 - VB – CT2) where
CT1 and CT2 are classes, then (VB) is an
Association relationship.

R-Rule3: If the concept is verb (VB) and satisfies
R-Rule2, and the concept is equal to one of the
following {"consists of", "contain", "hold, "include",
"divided to", "has part", "comprise", "carry",
"involve", "imply", "embrace"}, then the relationship
that discovered by that concept is Composition or
Aggregation. Example: “Library Contains Books”
then the relationship between “Library” and “Book”
is Composition relationship.

R-Rule4: If the concept is verb (VB) and satisfies
R-Rule2, and the concept is equal to one of thefollowing {"require", "depends on", "rely on",
"based on", "uses", "follows"} , then the relationship
that discovered by that concept is the Dependency
relationship. Example: “Actuator uses sensors and
schedulers to open the door”, then the relationships
between (“Actuator” and “sensor”), (“Actuator” and
“Scheduler”) are the Dependencies relationships.

R-Rule5: Given a sentence in the form
CT1 + R1 + CT2 + “AND”+ CT3 where CT1,
CT2, CT3 is a classes, and R1 is a relationship. Then
the system will indicate that the relation R1 is
between the classes (CT1, CT2) and between the
classes (CT1, CT3).

R-Rule6: Given a sentence in the form
CT1 + R1 + CT2 + “AND NOT”+ CT3 where
CT1, CT2, CT3 are classes, and R1 is a relationship.
Then the system will indicate that the relation R1 is
only between the classes (CT1, CT2) and not
between the classes (CT1, CT3). 
```



