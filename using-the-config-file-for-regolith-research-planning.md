---
description: >-
  This guide explains how to leverage the fields in the provided config file to
  design your regolith research.
---

# Using the Config File for Regolith Research Planning

**Understanding the Structure:** The config file defines several samples, each with characteristics and properties. Here's a breakdown of the key sections:

* **Sample Name:** Identifies the specific sample (e.g., Std Mars L1).
* **Characteristics:** Describes various aspects of the sample, further divided into:
  * **\[Organism]:** Organism used in the experiment (e.g., Lolium multiflorum).
  * **Term Source:** Reference database for the organism (e.g., NCBITAXON).
  * **REF Term:** Unique identifier for the organism within the source.
  * **Source:** Origin of the regolith (e.g., coarse-grade Mojave Mars Simulant).
  * **\[history/agrochemical additions] (Optional):** Details about amendments to the regolith (e.g., fertilizer).
* **Protocol:** Reference to the growth protocol used in the experiment.
* **REF Parameter:** Field name for a specific property of the regolith.
* **Parameter Value:** Measured value of the property.
* **Unit:** Unit of measurement for the value (e.g., percent).
* **Term Source:** Reference database for the property term.
* **RefTerm Source:** Reference database identifier for the property term.
* **Accession Number:** Unique identifier for the property term within the source.
* **soil type (Optional):** Additional characteristics of the regolith, further divided into:
  * **\[soil\_taxonomic/FAO classification]:** Soil classification system (e.g., soil\_taxonomic).
  * **Parameter Value:** Specific classification details (e.g., 1/3 sand, 2/3 peat moss).

**Using the Config File for Research Planning:**

1. **Define your research question:** What do you want to learn about regolith? Is it about plant growth, resource availability, or a specific chemical property?
2. **Select relevant samples:** Based on your question, choose samples with appropriate characteristics. For example, if studying plant growth, focus on samples with organisms (look for entries with a value under "\[Organism]").
3. **Identify key properties:** Review the listed "REF Parameter" sections. These represent various chemical or physical properties of the regolith. Choose those relevant to your research question (e.g., for plant growth, consider "Calcium Oxide" or "Sulfate").
4. **Analyze and compare:** Look at the "Parameter Value" for your chosen properties across different samples. This allows you to compare the impact of varying regolith composition on your research focus.
5. **Consider optional sections:** The "\[history/agrochemical additions]" section might be useful if you're studying the effect of additives on plant growth or other processes.
6. **Explore references:** The "Term Source" and "RefTerm Source" with "Accession Number" provide references for further exploration of the specific organism or property definition.

**Additional Tips:**

* Consider using spreadsheets or databases to organize the data from the config file for easier analysis and comparison.
* The config file can be a starting point for designing new experiments. You can create new entries with different regolith compositions or organisms or alternative factors of interest.
* Remember, this is just a sample config file. The specific fields and their contents might vary depending on the research focus.



[Link to example regolith metadata](https://docs.google.com/spreadsheets/d/1zE6qxytRrCT20wyu2CTJlcjWwIq4MaJxwdGPMz8OIVU/edit?usp=sharing) configuration file from the NASA Open Science Respotry.&#x20;



{% embed url="https://docs.google.com/spreadsheets/d/1zE6qxytRrCT20wyu2CTJlcjWwIq4MaJxwdGPMz8OIVU/edit?usp=sharing" %}
