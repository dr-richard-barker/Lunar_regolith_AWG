# Our goal is to perform deep-learning analysis on the Lunar regolith data using a generalised Regolith vs Earth DESeq2 linear model. 

This repo contains counts created for the "Lunar_regolith" assay using the NF_core RNAseq pipeline (https://github.com/nf-core/rnaseq). 

[Here is a link to a rough draft of a written manuscript](https://docs.google.com/document/d/1-np5DNb4gzrFckPqts1aCl-zTZpZt98fSMhH4DehIKQ/edit?usp=sharing). 

[Here is a link to a rough draft of a slide show narrative](https://docs.google.com/presentation/d/1a__b4exPY8by33kcYLlLfEo_rVxcj7CM7w__xbTRThE/edit?usp=sharing) 


Can we use analysis of differential expression of the real regolith to find studies on Earth that are similar using the European Bioinformatics Instiuotes expression atlas data?


[More information on the EBI expression atlas API syntax can be found here](https://github.com/gxa/atlas_gsa/tree/master)


---

![aa_3d-printed-lunar_base](https://github.com/dr-richard-barker/Lunar_regolith_deeplearning/assets/8679982/8ef677e4-5b39-46b2-9467-eaabcd633e7b)

**Image of lunar hab courtesy of ESA.**

---

![Lunar regolith Groots closer look](https://github.com/dr-richard-barker/Lunar_regolith_deeplearning/assets/8679982/3f17413c-2c34-408a-803a-024d78bb2d02)
Image designed to help you think about how plant might respond to lunar regolith. 


---


**Not sure how you can help?** 

Why not upload some of these data to AI tools and ask if they can help?
Here's an example make using "you AI" on genius mode during the free trial (now expired). 

---
**Human Question:**"If i add table of data are you able to use it to make graphs and show me the code?"
Yes, I can help you create graphs using data from a table. 
Please provide the table of data, and let me know what type of graph you would like to create. 
I'll be happy to assist you with the code and show you how to generate the graph.

---
**Human action:** i selected the differential expression results from "Broad Lunar Soil Model- regolith vs Earth.csv"


---
**Human Question:** Can you plot these data as a labelled volcano dot plot using the Row-names to define each dot, use the "Moon-Earth___log2FoldChange" to plot the x axis and a -log10 transformation of the "Moon-Earth___padj" as the Y axis. If they have a positive Moon-Earth___log2FoldChange color the dots red and if the have a negative Moon-Earth___log2FoldChange make the dots blue unless they have an a "Moon-Earth___padj" of >0.1 in which case make them grey.  
"Broad Lunar Soil Model- regolith vs Earth.csv"

"Genius" mode, AI Thinking, AI Programming, AI Writing,


---

**AI Answer**

"The volcano dot plot has been successfully generated using the provided data from the 'Broad Lunar Soil Model- regolith vs Earth.csv' file. 
The x-axis of the plot represents the "Moon-Earth___log2FoldChange" values, while the y-axis represents the -log10 transformation of the "Moon-Earth___padj" values.

The color of the dots is determined by the "Moon-Earth___log2FoldChange" and "Moon-Earth___padj" values. Dots with a positive "Moon-Earth___log2FoldChange" are colored red, while those with a negative "Moon-Earth___log2FoldChange" are colored blue. However, if the "Moon-Earth___padj" value is greater than 0.1, the dot is colored grey, regardless of the "Moon-Earth___log2FoldChange" value.

Here is the generated volcano dot plot:
![image](https://github.com/dr-richard-barker/Lunar_regolith_AWG/assets/8679982/c34ef772-a31e-4539-9ca2-128d3e59e32b)


The plot provides a visual representation of the data, making it easier to identify patterns and trends. 

If you have any further questions or need additional assistance, please don't hesitate to ask.


The code below was created by the AI to make this graph. 


**Python Code**

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

**Load the data**

data = pd.read_csv('/tmp/Broad Lunar Soil Model- regolith vs Earth.csv', index_col=0)

**Create a new column for the -log10 transformation of the "Moon-Earth___padj"**

data['-log10_padj'] = -np.log10(data['Moon-Earth___padj'])

**Create a new column for the color**

data['color'] = np.where(data['Moon-Earth___log2FoldChange'] > 0, 'red', 'blue')
data['color'] = np.where(data['Moon-Earth___padj'] > 0.1, 'grey', data['color'])

**Create the plot**

plt.figure(figsize=(10, 8))
plt.scatter(data['Moon-Earth___log2FoldChange'], data['-log10_padj'], c=data['color'])
plt.xlabel('Moon-Earth___log2FoldChange')
plt.ylabel('-log10(Moon-Earth___padj)')
plt.title('Volcano Dot Plot')
plt.grid(True)
plt.show()
```





