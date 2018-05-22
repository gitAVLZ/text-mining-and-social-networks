Topic Modelling
================
Topic Modelling is a coarse level analysis of what's in a text collection.
  * A document is a mixture of topics
  * A text clustering problem
  * Different models available
  * Topic output is just a list of word distributions: interpretation is subjective

**Given**: Corpus, Number of Topics. **Not Given**: Topic Names, Topic Distribution for each document

Latent Dirichlet Allocation
---------------------------
Latent Dirichlet Allocation (LDA) is a type of generative model.
LDA is a very powerful tool and a text clustering tool that is fairly commonly 
used as the first step to understand what a corpus is about. 
LDA can also be used as a feature selection technique for text classification and other tasks.

Choose length of document 
Choose mixture of topic for document
Use topic's multinomial distribution to output words to fill topics's quota
for a particular document, 40% of the words come from topic A, then you use that topic A's multinomial distribution to output the 40% of the words. 

Training the Model
******************

.. code:: python

  import pickle
  import gensim
  from sklearn.feature_extraction.text import CountVectorizer

  # Load the list of documents
  with open('newsgroups', 'rb') as f:
      newsgroup_data = pickle.load(f)

  # Use CountVectorizor to find three letter tokens, remove stop_words, 
  # remove tokens that don't appear in at least 20 documents,
  # remove tokens that appear in more than 20% of the documents
  vect = CountVectorizer(min_df=20, max_df=0.2, stop_words='english', 
                         token_pattern='(?u)\\b\\w\\w\\w+\\b')
  # Fit and transform
  X = vect.fit_transform(newsgroup_data)

  # Convert sparse matrix to gensim corpus.
  corpus = gensim.matutils.Sparse2Corpus(X, documents_columns=False)

  # Mapping from word IDs to words (To be used in LdaModel's id2word parameter)
  id_map = dict((v, k) for k, v in vect.vocabulary_.items())

  # Use the gensim.models.ldamodel.LdaModel constructor to estimate 
  # LDA model parameters on the corpus, and save to the variable `ldamodel`

  # Your code here:
  ldamodel = gensim.models.ldamodel.LdaModel(corpus, num_topics=10, id2word=id_map, passes=25, random_state=34)

  # Print Top 10 Topics / Word Distribution
  output = ldamodel.print_topics(10)
  print output

Predict Topic for Document
**************************

.. code:: python

  new_doc = ["\n\nIt's my understanding that the freezing will start to occur because \
  of the\ngrowing distance of Pluto and Charon from the Sun, due to it's\nelliptical orbit. \
  It is not due to shadowing effects. \n\n\nPluto can shadow Charon, and vice-versa.\n\nGeorge \
  Krumins\n-- "]
  
  
  # for new document, use the previous model to find the probability which topic it belongs to
  def topic_distribution():
      
      # Fit and transform
      X = vect.transform(new_doc)

      # Convert sparse matrix to gensim corpus.
      corpus = gensim.matutils.Sparse2Corpus(X, documents_columns=False)

      output = list(ldamodel[corpus])[0]

      return output

Assigning Topic Names for Each Word Distribution
************************************************
Topic names are subjective to the assigner.

.. code:: python

  def topic_names():
      
      # Your Code Here
      output = ['Computers & IT', 'Automobiles', 'Computers & IT', 'Religion', 'Automobiles', 'Sports',
               'Education', 'Religion', 'Computers & IT', 'Science']
      return output