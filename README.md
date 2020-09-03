# SearchEngine
Libraries used - spacy<br>
en_core_web_lg model used from spacy to find similarity between 2 words.<br>
The code has been well commented to explain the role of each function written, this is just an overview of how the system works.<br>
Print statements have been commented in order to save I/O time and improve visibility on console.

## Instructions to run the program:
1. pip3 install -U spacy
2. python3 -W ignore main.py (The -W ignore is to ignore the warnings, warnings arise due to use of en_core_web_lg empty vectors for similarity)
3. Keep entering values according to the information asked on console, if not provided, just press enter, 
   the system already takes default values


## User input:
1. Path to the text corpus
2. Path to the inverted index 
3. K in top K documents
4. Queries 

## Part 1:
### Files used:
main.py, index.py, tf_idf.py

### Data extracted from corpus:
1. Total number of documents
2. Inverted index - {term:{total number of docs in which it appears,list(docid,frequency in the doc)}
3. Document index - {doc_id:list(token,frequency in the doc)}
4. Word_set - set of_words in the vocabulary (use explained later)

### Working:
The text corpus is scanned for tokens. The above mentioned data is extracted from the corpus.<br><br>
<b>Note:</b> This step takes the maximum time and is considered as the server bootup time time by us.<br><br>
Considering, building a system which is always up
and needs to quickly answer the user queries, this time should not affect user interaction.

### Preprocessing:
Simple Preprocessing is done.
1. <a href="https://www.google.com">Google</a> gives Google
2. special characters are removed
3. text is converted to lower form

### Output:
1. Invert index is printed in a file.
2. Top k docs id's are printed on the console and their content in the file without_improv.txt

<b>Note:</b> Documents are 1 indexed or doc_id starts from 0

After providing the inverted index path, the system might take some time because it calculates similarity between all terms.          


## Part 2:
### Improvement 1:
-----------------------------------------------<b>Improving the accuracy of the system</b>--------------------------------------------------<br>
Similar words along with cache hit list<br>
The improvement proposed is based on the information needs of the user. We aim to improve the relevant information to the user<br>
instead of the information relevant to the query.<br><br>
This improvement is best demonstrated with an example:<br>
Suppose our text vocabulary comprises of 10 words numbered from 1 to 10 including U.S and United States.<br>
For each token, we find similarity of that word with the other words and if the similarity > 0.4, we consider them to be similar and add the 2nd word
to the similar_words list of the 1st word.<br><br>
In this case, U.S and United States will have similarity value > 0.8<br><br>
<b>Note:</b> Similarity score is calculated using en_core_web_lg model<br><br>
We initialize a hit_list which keeps a track of the frequency of all the words that users around the globe enter.<br>
Whenever a query is recieved, the similar words of each word of the query is checked and if the number of times a similar word has been searched for
is more than the word, we replace the word by the similar word simultaneously increasing the hit value of the word by 1.<br><br>
<b>For example:</b> If the user searches for U.S and United States is a similar word to it having a higher hit value, we replace U.S by United States<br>
and also increase the hit value of U.S by 1.
<br><br>
<b>Note:</b> There is no increase in the hit value of United States because hit list depicts the exact queries entered by the users.
<br><br>
<b>Assumption:</b> Majority of the users are able to portray the correct information need.

<b>Testing:</b> The testing of this improvement can only be done after executing about 20 queries before running the actual query, so that hit list has some values accumulated.<br><br>
The first 15 queries have been given as the sample queries to see the actual improvement.<br>

The improvement can have adverse effect when:
1) Majority of the people have searched for wrong keywords.
2) The user is searching for something specific and not related to the majority of users.

<b>Results for the improvements have been shown in the table.</b>


### Improvement 2:
-----------------------------------------------<b>Improving the speed of the system</b>--------------------------------------------------<br>
The leader follower model has been implemented in order to improve the speed of the IR system. Though it can compromise on accuracy, it can improve user interaction by sppeding up the queries significantly.<br>
root(N) leaders are chosen using random number generator and each follower is assigned a leader.<br><br>
The closest leaders are found and the leader and its followers are added to the list based on their scores. The leader list is also iterated based on its scores with the document.

<b>Results can lead to poorer accuracy but significant increase of speed in retreival.</b>
