from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.cluster import KMeans


documents = ['Cats are known for their agility and grace.',#cat
             'Dogs are often called mans best friend.',#dog
             'Some dogs are trained to assist people with disabilities',#dog
             'The sun rises in the east and sets in the west',#sun
             'Many cats enjoy climbing trees and chasing toys',#cat
]

#Create a TfidfVectorizer object

vectorizer = TfidfVectorizer(stop_words='english')

#Learn vocabulary and idf from training set.

X = vectorizer.fit_transform(documents)

#Perform k-means clusteering

kmeans = KMeans(n_clusters=3,random_state=0).fit(X)

#Print cluster labels for each document

print(kmeans.labels_)