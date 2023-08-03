# Process Discovery and Conformance Checking: Road Traffic Fine Management

This repository contains a data analytics project that utilizes process mining methodology to analyse the real-life event log of an information system managing road traffic fines in Italy. The dataset was gotten from [here](https://doi.org/10.4121/uuid:270fd440-1057-4fb9-89a9-b699b47990f5) and it contains fines created between 1st January 2000 to 17 June 2013.

### Credits
<b>Publisher</b>: Eindhoven University of Technology
<b>References</b>: https://doi.org/10.1007/s00607-015-0441-1

# Project Overview
The goal of this project is to:
1. Utilize process mining techniques to discover the actual road traffic mining process carried out by the users of the software
2. Identify inefficiencies and deviations from expected process.
3. Generate insights to improve process efficiency.

# Methodology
1. Data Collection
2. Data Quality Check
3. Data Transformation
4. Process Discovery
5. Timing Analysis
6. Suggestions for process improvement

# Technologies Used
1. Python
2. Pandas
3. Graphviz library
4. Jupyter Notebook
5. Microsoft PowerBI

# Data collection
The initial event log consisted of 150370 cases between 1 January 2000 and 17 June 2013, spread over the years. The case column was used as the case ID. The event column was used as the activities and contained the following activities:
- <b>Create fine</b>: The date the fine was created.
- <b>Send fine</b>: The date the fine is sent to the offender
- <b>Insert fine notification</b>: The date offender is notified about the fine (i.e., when the fine is dropped at the post office)
- <b>Add penalty</b>: If the fine is not paid in 60 days from the creation, a penalty is added. This is the date the penalty is added
- <b>Payment</b>: When the fine is paid. Note that a fine can be paid multiple times in installment
- <b>Send for credit collection</b>: After 180 days, if the fine is not paid, it is sent to the credit collection agency
- <b>Insert date appeal to prefecture</b>: The date the offender decides to appeal the fine to the prefecture 
- <b>Send appeal to prefecture</b>: The date the offender sends the appeals the fine to the prefecture
- <b>Receive result appeal from prefecture</b>: The date the offender receives the result of the appeal from the prefecture
- <b>Notify result appeal to offender</b>: The date the offender is notified of the result of the appeal (similar to Insert fine notification above)
- <b>Appeal to judge</b>: The date the offender appeals to a judge.

The dataset contains other attributes such as the amount (the offender has to pay), expense (the amount paid in sending the fine), paymentAmount (the amount the offender paid), totalPaymentAmount(the total amount the offender has paid), vehicle class (the class of the vehicle), article (the road article that was violated), etc.

# Data quality check and transformation
The event log was reviewed to ensure the data contained were okay to use for process mining. The following was discovered:
1. Python did not automatically identify the date column. This was fixed subsequently.
2. Some columns had empty values. This was because certain activities require input in those columns while some do not. For example, expense column is filled only when the send fine activity is done.
3. The org:resource column which indicates the user who performed a particular activity was constant through out the case. Therefore, it was not possible to check for disaggregation of duties or handover of work.
4. Since the event log was gotten online, no process owner was contacted to gain further understanding of the process and other columns in the dataset. Therefore, if there are manual events in this process, it is not highlighted here.
5. In analysing, it is important to use completed cases. The event log was filtered for those cases that either ends in Payment, sent for credit collection or dismissed following a successful appeal. After filtering for the completed cases, we were left with 128,268 events.

# Output and Visualisations
## Data overview
![alt text]()

This gives an overview of the dataset. It shows the number of fines, and events and the total fine amount from these events. The stacked column chart at the top right shows the number of fines per year and the vehicle class. It is apparent that the users od vehicle class A are the most frequent traffic offenders.

The points by article number table shows that while Article 157, 7 and 158 are the most frequent articles violated, article 142 and 158 are the violations which result in most most points decucted. This is because Article 142 relates to Speeding tickets and Article 157/158 relates to parking fines.

## Process discovery based on event log data
![alt text]()
---------------------------------------------------
The Graphviz library was used to automatically generate a visual process model based on the event log data. 
1. <b>Variant analysis</b>: This variant analysis shows how frequently a particular process is followed. There was a total 193 variants with the top 5 variants acconting for 93% of the cases.
2. <b>Events chart</b>: This shows when the events occur by the time of the week. It shows that most fines are created either on a Friday or Saturday (TGIF😄), payments were made on either a Monday or Tuesday and fines are sent for credit collection mostly on Tuesday or Sunday.
3. <b>Process graph</b>: The process graph shows how the activity flows from the start of a procurement process to the end. From the process graph, all cases starts with the Creation of a fine. For Variant 2 cases, the offender pays the fine immediately it is created. Variant 2 accounts for approximately 36% of all cases. This maybe because for cases in Variants 2, these fines are paid immediately they are given. For other variants, after the case is created, it is sent to the offender and they are either paid or sent for credit collection.
4. <b>Events transition matrix</b>: The transition matrix shows how the events are been handed over from one to another and how frequently this occurs.
### Process deviation
* <b>Dismissal variables used</b>: The dismissal variable based on research to be used are either NIL (when the fine is created and should bot be changed unless there is a successful appeal), G (when there is a successful appeal and the case is dismissed by the judge), and # (when there is a successful appeal and the case is dismissed by the prefect). However, there were other dismissal variable that were found in the event log. They are detailed below:
  * <b>Dismissal variable 4</b>: This dismissal variable appears in only 2 cases and used in one article (142). Used by 2 users (User 53 and User 11) for fines created in August and December 2004 and occurs in 2 variants (Variant 3 and Variant 4)
  * <b>Dismissal variable @</b>: This dismissal variable appears in 9 cases and two variants (Variant 1 and variant 8), used by 7 users (User 537, 550, 35, 8, 29, 31, and 536). All fines were created between January 3 and June 3 2000. Article 7, 157 & 158.
  * <b>Dismissal variable D</b>: Used once for the case created on 2 June 2011 by User 848. Appears in one case. Aritcle 158.
  * <b>Dismissal variable Z</b>: Used once for the case created on June 16 2000 by user 811.
* <b>Create fine dismissal variable</b>: When a fine is created, the dismissal value is NIL. While the NIL variable is used for almost all cases in this activity, the dismissal variable 4, was used 2x, @ was used 9x, D and Z were used 1x.
* <b>Successful dismissal</b>: As highlighted above, there are some cases where the successful dismissal variable is used (G and #) but those cases do not end with Send appeal to judge or prefecture.
* <b>Receive result appeal from prefecture</b>: This activity is usually done when the appeal to the prefecture was not successful and should have a NIL dismissal variable. Below, there are 24 cases where a # variable was used instead.
* <b>Add penalty to Send for credit collection</b>: For all cases which went transitioned from <i>Add penalty</i> to <i>Send for credit collection</i>, the least duration was 273 days; significantly higher than the expected 180 days. This shows either a lack of monitoring system or the prefects do not monitor the fines if they have been paid or not for onward forwarding to the credit agencies.
* There were two cases (S66168 and N35881) where the fines were fully paid, but it was still sent to the credit agency for collection. Both fines were paid in 2002, but they were sent to the credit collection on 10 January 2004. That's about 2 years later.
### Other findings
* There are some users that carry out particular events in batches. For example, users 538, 550 and 536 performed the Send fine activity for 259, 195 and 176 cases respectively on 19 May 2003. Also, the three users performed the Insert fine notification activity 100, 69 and 65 times respectively on 25 May 2003.

## Timing analysis
![alt text]()

This analysis was done to confirm if there are deviations regarding the time when the events should be performed. Some performance indicators were developed.
1. From research, each fine created should be completed within six months; they should either be paid, sent for credit collection or dismissed. The first test that was done was relating to the duration of each case. It indicated that out of 128,268 cases, 75,259 (59% of cases) were completed after 180 days.
2. Looking at the individual cases, the send for credit collection event is where more time was spent. It takes an average of 525 days before a fine is sent for credit collection. This is way too long. One reason for this can be that the person who raised the fine is not monitoring the fines to adhere to the time limit.
3. One thing to note is that the Add penalty activity is always followed. Looks like an automatic event that happens when it is up to 60 days since the fine was created but payment has not been made.
4. There are 37,266 cases where the fine was sent after 90 days. Out of these fines which were delayed, 70% of them were sent to the credit collection agency. This might be because the offenders are already aware that these fines were sent out late and there would be no repercussion for not paying. The traffic prefects should be trained/reminded of this rule to avoid offenders getting away with fines. This also incurs cost to the management by paying for postal expenses which would not be recovered.
5. There are 53,309 fines which have not been fully paid, but have not been sent to the credit collection agency.
6. The 60 days time limit for appeals are not been adhered to by the offenders. Of the fines appealed to the judge, 36% of them were done after 60 days, while for those appealed to the prefect, 81% of them were done after 60 days. We are not sure if this time indicated when the actual appeal was made or when the prefect recorded this in the system. Either way, this shows a deviation from the expected process.

## Case details
![alt text]()

This dashboard shows information relating to a particular case by using the filter at the top right of the screen.

# Process improvement
Based on the analysis, areas for improvement were identified such as:
* Process redesign: One of the areas of improvement can be understanding who does what activity. From the event log, it appears that the prefect does all the activities. This can be misleading as there can be human error or delay in inputing this event into the system. Activities such as Appeal to judge and appeal to prefecture should be done by the offender and the time this is done should be indicated.
* Automating send to credit agency: The send to credit agency can be automated if certain conditions are met. For example, when a fine is creates, sent, no appeal and no payment made after a certain number of days. It makes the process easier and can help in early collection of fines by the collection agents. This will be relevant especially considering the fact that people move houses frequently and leaving it too long can lead to loss of fines.
* Avoid delaying sending fines: A lot of fines were not sent within the 90 days fime frame. This is because while the prefect is outside on their duty post, they might not have the time to carry out other activities. I would suggest that there should be a segregation of duties between the prefects who create the fine and the person who sends the fine to the offender to avoid this delay. 
* Improved communication: There were 3,889 times where a penalty was added after a payment was made and two cases where payment was made but still sent to the credit agencies. I think this either is as a result of delay in communication between the payment receiver and the prefect, or the prefect do not update the case with the payment on time to avoid triggering a penalty.

# Limitation
No process owner was reached out to confirm the validity of the expected process

# Repository structure
* 'Data/': Contains the data used for analysis
* 'Notebook/': Jupyter notebook detailing the data cleaning, process discovery and analysis
* 'Output/': Includes the PowerBI output and a PDF file

# Contributions
Contributions to this repository are welcome! If you encounter any issues or have suggestions for improvement, please open an issue or submit a pull request.

# Contact
For any questions or inquiries, please contact nkwachiabel@gmail.com
