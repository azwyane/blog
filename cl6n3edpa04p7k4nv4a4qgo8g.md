## Towards creating an Intrusion Detection system

## Abstract
Intrusions in network refers to all those anomalous/undesirable activities that affects the CIA’s of the security for the intention of stealing, altering data and system that serves the desire of the attacker of bringing the service down. With our heavy dependence over internet technologies, we are living in the age of inter networked digital life but along with such advancements we’re are at a constant risk in terms of security and our privacy. The attacks include DoS, DDoS, Phishing and many more, they are varied and are always changing and more sophisticated. Still at present day DoS has been a major issue for big companies serving their business online. We at our defense side must be smart enough and deal with changing patterns and detect them as early as possible.

This article tries to introduced machine learning approach on detecting those intrusive patterns, taking data-set reference from CICIDS2017. Where we first capture the network traffic flow in the form of .pcap files, then process it into a well structured format of data using CIC-flow-meter. We try to infer the patterns that separates benign from intrusive ones. From the analysis, it is obtained that machine learning approaches could be one of the layer for security purpose rather than supplementing signature based IDS wholly.

Keywords: IDS(Intrusion Detection System), IPS(Intrusion Prevention System), FP(False Positive), FN(False Negative), Benign, Intrusions,Nftables

## Introduction
Using rule based system to analyze the network flow and detect the anomalies/intrusions based on their signatures stored on the database only isn’t an effective way to detect the network intrusions early for new type of intrusions that the database doesn’t consists of. We are living in the digital age where our entire business today is heavily dependent on. While having perks, we also have risks associated with it such as DoS, DDoS, Botnet, etc and many undiscovered attacks. So we want to realize such a system which learns patterns from network flows and discriminate them as desired. For that purpose we build a machine learning model based on CICIDS2017 and use a flow meter to listen and dump packets into structured form. Then for training purpose utilize [smote](https://imbalanced-learn.org/) to balance the imbalanced datasets then apply pca for dimension reduction and finally use random forest classifier to create a model from these data. After inferring the model with testing data we achieved a astonishing result that implies that is can classify the unseen network flows consisting of benign and intrusive network flows pretty well. This brings us to implementing such a system which now can create firewall rules based on these predictions in order to block them from harming the system. It utilizes the batch processing of each captured flow and outputs the corresponding predictions.

## Objectives
- Apply Machine learning approach to build an intrusion detection system

## Problem statement
We are heavily dependent on internet based services, with rise of digital networking we are benefited as well as are in a risk of network based attacks.The network based attacks are costly for an organization and for individuals in terms of data, privacy and service. Previously used signature based method are though simple and direct in architecture, they need to maintain huge datasets, such isn’t possible to include all attack patterns till date, also new day attack type detection isn’t possible at all until network flow verified as an attack and updated to the signature database. We require an intelligent agent that learns what is actually benign network flow, we want it to detect the flows that deviate from this profile and mark it as intrusive/suspicious as early as possible.


## Literature Review
For the purpose of intrusion detection we need to create a baseline about what is traffic is normal and what is not. Further we require such a  datasets which can represent the knowledge about the feature (domain knowledge) which is valuable for the study. Searching for those datasets bring us to KDD99Cup, NSL KDD, DEFCON-8, CICIDS2017, etc. Selecting from those didn't pose such a challenge because we want such dataset that is most recent and has data entries that captures the new types of attacks. So, for the study analysis CICIDS2017 was chosen. This data set consisted of about 80% benign and 20% different types of attack types. The original creators [4](#References) of the dataset used random forest regressor and weighted average method for feature selection whereas some authors [5](#References) used principal component analysis for dimensionality reduction.

## Methodology
The methodology follows a ml workflow where Principal Component Analysis and Random Forest Classifier are used. The following link forwards to the implementation code. The dataset was downloaded from [CICIDS2017](https://www.unb.ca/cic/datasets/ids-2017.html) website, which was processed into parquet file for fast reading of the data entries. [Intrusion detection(Binary classification) with PCA and RandomForest](https://azwyane.github.io/article/intrusion-detection(binary-classification)-with-pca-and-randomforest)
For implementing IPS, we design the system in such a way where each classified flows are then prevented by creating firewall rules using net-filter nftables in Linux based operating system by automated creation of nftables block rules in input, output or forward chain. Moreover, generating what kind of rule for the each flows is more of a concern.

## Conclusion
From the above analysis, we could create a logically modeled solution for separating benign patterns apart from the intrusive ones based on the features extracted from the captured network packets using flow meter.

We could see that only applying principal component analysis setting components to be 10 such that knowledge from data doesn’t diminish for the purpose of dimentionality reduction and applying random forest classification algorithm such that the hyperparameteres used from cross validated high scoring model, we could obtain F1 score to be nearly 0.99 and roc auc to be 0.98. It shows the model learns the hidden patterns from data and is distinct on what separates benign from intrusion.

The study doesn’t end here rather with this kind of approach, it raises a question on what if we study on more set of features, what features have a value in determining the result.

Further, this study relies on flow-meter and its data structuring and its captured bidirectional flow. The input provided to the model act as an independent flow, but sophisticated attacks are sometimes made up of connected flows, it will create an ambiguity when same connection is sometime benign and anomalous.

While creating an IPS, the major factors we consider are False Positives and False Negatives. Both high FP and FN are unwanted but can be handled as per the purpose. This raises another intuition behind using this type of model along with other types of IDS. Controlling Benign as intrusions (FN) and intrusions as benign (FP) can be pretty straight forward if one applies manual setting for preventing mislabeling of benign
as intrusion for networks with low traffic but still using them in large scale and creating manual rules to revert predictions is still not an efficient way.

In further analysis we will see what other methods we can focus on getting features that are valuable using CNN and neural networks and also study what kind of IPS needs to be built for such system. In following articles we will discuss the types of approach to achieve creating varied types of rules for different kinds of attacks and block types such as specific port or ip block or entire network block . Such implementation will be targeted for network layer and for IPv4 networks only and also we will discuss the efficiency required for such system for processing huge network traffic for classifying them as Benign or Intrusions.

The entire source code of this could be found at : [https://github.com/azwyane/NGuard](https://github.com/azwyane/NGuard)

## References
1. [https://www.unb.ca/cic/research/applications.html](https://www.unb.ca/cic/research/applications.html)

2. [http://www.unb.ca/cic/datasets/IDS2017.html](http://www.unb.ca/cic/datasets/IDS2017.html)

3. [https://wiki.nftables.org/wiki-nftables/index.php/Main_Page](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page)

4. I. Sharafaldin, A. H. Lashkari, and A. A. Ghorbani, “Toward generating a new intrusion detection dataset and intrusion traffic characterization,” in ICISSP 2018 - Proceedings of the 4th International Conference on Information Systems Security and Privacy, 2018,vol.2018-January,pp.108–116.doi:10.5220/0006639801080116.

5. R. Abdulhammed, H. Musafer, A. Alessa, M. Faezipour, and A. Abuzneid, “Features dimensionality reduction approaches for machine learning based network intrusion detection,” Electronics (Switzerland), vol. 8, no. 3, Mar. 2019, doi:10.3390/electronics8030322.