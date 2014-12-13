webproj
=======

QDocument.java - numviews, content, id, weight(may-be), freqincorpus, 

QIndexerCompressed.java - store trie, List of QDocuments, Index of word to postinglist, (have a word specific class to stor in trie)

QRanker.java

Stop word removal is similar in java and in python. In Java, numbers are not removed though. It will not affect it as we can only do stopword removal and stemming on queries (and not on the corpus at all).


-----SAMPLING-----------
1. Select top x phrases from every document.
2. Distribute average numviews (~90) among only these phrases.
3. Do not sample the top x phrases. These will be part of the final set of phrases.
4. If the probability of a phrase lesser than some value (need to fix the value), we ignore the phrase. We sample y% of the remaining phrases after this.
5. If document has numviews > 0, we distribute them among all phrases (including the top ones) that we sample.



------Ranking-----------
1. Irrespective of whether a word is complete or not, we try to complete the word (assuming it is a partial word) using the trie.
2. Among the results given by the trie, we select those ones with the highest co-occurrence - select some number (to be decided).
3. We try to form phrases with the whole text (last word replaced by the suggestions from the trie). We assign a weight (lambda1) if a valid phrase is found and add to the total score. If the document (phrase) also starts with this formed phrase, we add an additional weight (lambda4).
4. We try to complete the query by selecting documents (phrases) in which all the terms of the query occur (conjunctive).
5. To all these possible documents, we add the frequency component weighted by lambda2 and the numviews component weighted by lambda3.
6. We select the top few documents based on their total scores and return them.


cat data/simulatedlogs.txt | python py/smap.py | sort -t $'\t' -k1 -V | python py/sred.py | sort -t $'\t' -k3 -r -n > data/cooccurrences.txt 
