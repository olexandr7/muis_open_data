#https://opendata.muis.ee/
#technical scripts

#https://rdflib.readthedocs.io/en/stable/gettingstarted.html#a-more-extensive-example
from rdflib import Graph
# Create a Graph
g = Graph()

# Parse in an RDF file hosted on the Internet
g.parse("http://opendata.muis.ee/object/1887998") 
#g.parse("http://opendata.muis.ee/object/1120182") 

# Loop through each triple in the graph (subj, pred, obj)
#for subj, pred, obj in g:
    #print(subj)
    #print(subj, pred, obj)

# Print the number of "triples" in the Graph
#print(f"Graph g has {len(g)} statements.")
# Prints: Graph g has 86 statements.

# Print out the entire Graph in the RDF Turtle format
#print(g.serialize(format='turtle'))

for s, p, o in g:
    print(s, p, o)
    #if "image/" in o: print(s)
    #if "P46_is_composed_of" in p: print(o)
#################################################################################################
#getting list of valid URLs into list
from rdflib import Graph
g = Graph()
url = "http://opendata.muis.ee/person-group/"
generallist = []

lst = list(range(100000, 100010))           
for i in lst:
    id = str(i)
    try:
        #g.parse(url + id)
        print(url + id)
        generallist.append(url+id)
    except:
        pass
        #print("error: " + url + id)
#################################################################################################	
#getting list of valid URLs into list
from rdflib import Graph
g = Graph()
url = "http://opendata.muis.ee/person-group/"
generallist = []

lst = list(range(100000, 100010))           
for i in lst:
    id = str(i)
    try:
        #g.parse(url + id)
        print(url + id)
        generallist.append(url+id)
    except:
        pass
        #print("error: " + url + id)
#################################################################################################	
#filtering out values from valid URLs, adding them into two lists
museumlist_url = []
museumlist_names = []


for i in generallist:
    g = Graph()
    g.parse(i)
    for s, p, o in g:
        #print(s, p, o)
        #if "image/" in o: print(s)
        if "rdf-schema#label" in p:  museumlist_url.append(i), museumlist_names.append(o)
        #print(i + " " + o)		
#################################################################################################			
#making dataframe out of list, filtering out only museums
# import pandas as pd
import pandas as pd

df = pd.DataFrame(list(zip(museumlist_url, museumlist_names)))
df = df.rename(columns={0: 'URL',1: 'Name'})
df = df[df['Name'].str.contains("Muuseum")]
df
df.to_excel("museums_list.xlsx")
#################################################################################################
####
# Textile collection analysis
####

#http://www.muis.ee/rdf/collection/837
#https://www.muis.ee/museaalview/1887998
#textile collection (Muuseumikogu tekstiil) from Tallinna Linnamuuseum

#idea
#getting list of all objects from collection into list
#getting descriptions from each of objects into dataframe
#################################################################################################
from rdflib import Graph

g = Graph()
collectionitemslist = []

# Parse in an RDF file 
g.parse("http://www.muis.ee/rdf/collection/837")

#loop through triples
for s, p, o in g:
    if "P46_is_composed_of" in p: collectionitemslist.append(o)
        
#collectionitemslist now contains urls of all items in this particular collection
#################################################################################################
#filtering out values from valid URLs, adding them into two lists
collectionitemslist_url = []
collectionitemslist_label = []
it = 0

for i in collectionitemslist:
    g = Graph()
    try:
        it += 1
        g.parse(i)
        for s, p, o in g:
            if "rdf-schema#label" in p:  print(it), collectionitemslist_url.append(i), collectionitemslist_label.append(o)
    except:
        pass
#################################################################################################
#making dataframe out of lists
# import pandas as pd
import pandas as pd

df = pd.DataFrame(list(zip(collectionitemslist_url, collectionitemslist_label)))
df = df.rename(columns={0: 'URL',1: 'Label'})
df = df[df['Label'].str.contains("tervik") == False]  #filtering out values with "tervik"

df.to_excel("C:/Temp/textile_col_labels.xlsx")
#################################################################################################
#https://github.com/amueller/word_cloud/blob/main/examples/simple.py

from wordcloud import WordCloud
import matplotlib.pyplot as plt
import pandas as pd

#additional filtering for temp scope reduce
#df = df[df['Label'].str.contains("Kleit")]  #!!!!
#creating a single string with all values from Label column
df_joined = ' '.join(df['Label'].to_list())

#df_joined = 'Kleit, õmmeldud valgest margisetist, Kleit pikkade kimono varrukatega Kleit Kleit Kleit Kleit KleitKleit Kleit'
#would actually also need to do some: cleansing, lemmisation, tokenisation
#################################################################################################
#generating world cloud

import matplotlib.pyplot as plt
# lower max_font_size
wordcloud = WordCloud(max_font_size=80).generate(df_joined)
plt.figure()
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.show()