# Home case study: Public Knowledge Extraction

In many cases, **information retrieval** is a laborious and repetitive task. Hence,
developping a robust automatization of such a process is a good investment both
in terms of time and performance.

This case study focuses on scraping [PubMed](https://pubmed.ncbi.nlm.nih.gov/),
the leading search engine for bibliographic data in biology and medicine, to find
association of genes with other genes, and with cancer types.

# Resources

To scrape PubMed, we use the [PyMed](https://pypi.org/project/pymed/) library, which
provides direct access to the results of a PubMed query in Python.

Rather than using a Machine Learning algorithm to recognize gene names, we use a list
of more than 50000 human genes (including aliases). This will prevent any problem of
named entity recognition.

The list of genes can be found on
[genenames.org](https://www.genenames.org/download/statistics-and-files/).

# Solution

## Short-term

This short-term solution is implemented in the **notebook** together with a quick demo.

The processes of finding gene-gene association and cancer-gene association are pretty similarly
executed. The gene query returns a tunable number of abstracts, but not all of them refer to
cancer. We select the ones that do, and we look for occurences of words that are also in
our genes list or in our cancer types list. For cancer types, lemmatization is also
performed in order to reduce words to their canonical form. In the code, it's also possible
to use other words than "cancer" (such as "oncology" or "melanoma") to spot relevant abstracts.

Related genes and cancer types are then returned in a **sorted dictionary** with their number of
occurences: the most frequent ones come first. See the comments in the notebook for more
detail.

A few improvements could be added to this short-term solution:
- It could be interesting to also look at paper titles (not just abstracts), which might sometimes contain names of genes or cancer types that are not in the abstract.
- Instead of just looking for organ names, it could be better to check whether the word "cancer" comes right after: a cancer-related paper mentioning lungs will not always be lung-cancer-related. This applies to organs, but not cancer types such as leukemia or melanoma.
- In order to reduce the false positive rate, maybe removing genes and cancer types with too few occurences could help. This might also partially solve the problem mentioned right above.

## Medium-term

Since this tool is not an end in itself, but is meant to be used in other projects, the
medium-term solution would be to encapsulate it in an **API**.

In terms of improving its performance, it would be interesting to assign a **score** to each gene
and cancer type (especially genes): instead of just counting their occurences, we could
assign them a high score when they appear close to the query gene in a given abstract, and
a lower one when they appear far from it. The score metric has to be tuned in order to
balance it for genes that appear in many abstracts, but far from the query, and for genes that
appear in few abstracts, but close to the query.

It will also be necessary to check if PubMed has some kind of **anti-spam firewall** that might
prevent us from using PyMed too much.

## Long-term

In the long-term, it could be interesting to test how **Machine and Deep Learning** techniques
perform for our task. Recurrent Neural Networks and Attention Networks are especially popular
for NLP tasks, and could for example check whether a gene mentioned in an abstract is associated
to the query in a deeper way than just a co-occurence.

**Word embedding** techniques could also be interesting to use to embed genes and find actual relations
and clusters that relate them.
