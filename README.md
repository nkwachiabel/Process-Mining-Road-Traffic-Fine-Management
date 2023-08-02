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
![alt text]()
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

## Process discovery
![alt text]()
---------------------------------------------------
The Data Overview tab above, shows thw 
The Graphviz library was used to automatically generate a visual process model based on the event log data. 
1. <b>Variant analysis</b>: This variant analysis shows how frequently a particular process is followed. From the above, we can see that there is a total of 26 variants. The first 4 variants account for about 83% of all process, Meanwhile 7 variants occur only once. There are some cases which were completed without any approval. These cases have been captured in Variants 1, 3, 9 and 16 comprise 694 cases.
2. <b>Process graph</b>: The process graph shows how the activity flows from the start of a procurement process to the end. From the process graph, not all the cases need both approval. There are more of 1st approvals than 2nd approvals. In addition, there are four different start cases i.e., <i>Document Date</i>, <i>Invoice Received</i>, <i>1st approval</i>, and <i>Posting Date</i>. 694 payments representing 47% of the cases were made without approval. Out of these payments, 17 were reversals. Out of these payments, 402 (representing all payments made to <i>Vendor 401972</i> were made without approvals.
3. <b>Transition matrix</b>: The transition matrix shows how the events are been handed over from one to another and how frequently this occurs. From the above, it can be seen that <i>Payment performed</i> activity is not followed by any other activity i.e., that is the end of the process. However, all other events can be done before <i>Payment Performed</i> in no particular order. The most frequently occuring activity before <i>Payment performed</i> is the <i>Posting Date</i>. <i>Document Date</i> is mostly followed by <i>Invoice Received</i> and <i>Invoice Received</i> is mostly followed by <i>1st approval</i>. <i>2nd approval</i> is majorly done after <i>1st approval</i>. One thing to note from the transition matrix is that all other event can happen after <i>Posting Date</i>. It begs the question when should a procurement be posted? After approval?
4. <b>Other findings</b>: From the above, it can be seen that there are some events which happen on Saturday and Sunday. 6 payments were made on Saturday and 1 on Sunday. The most frequently occuring activity in the weekend was <i>Document Date</i> which occured 156 times.

## Performance analysis
![alt text](https://github.com/nkwachiabel/Procure_to_Pay/blob/main/Images/Performance%20analysis.jpg?raw=true)

Process performance metrics such as cycle time, and lead time were calculated for each activity or process step. Lead time provides the information of a duration of an event until another activity is performed.
1. The average duration in days graph shows which event takes more time on an average. It shows that more time is spent on the <i>1st approval</i> activity and <i>Document Date</i> activity and the least amount on time is spent on the <i>2nd approval</i> activity.
2. The transition matrix shows the average time it spends moving from one activity to another. The transition matrix shows that it takes an average of 32 days from <i>Document Date</i> activity to <i>1st approval</i>. It takes an average of 7 days from the <i>Invoice Received</i> activity to the <i>Payment performed</i>.
3. The case duration graph shows that a lot of the cases are completed within 30 days. However, there are some outliers where cases lasts more that 200 days.
4. <b>Payments</b>: There were 140 cases amounting to $501k which were paid late. There are 400 payments amounting to $383k which are recurring (same Vendor, same amount). Majority of these recurring payments (103) were paid to <i>Vendor 401972</i>.

![alt text](https://github.com/nkwachiabel/Procure_to_Pay/blob/main/Images/KPIs.jpg?raw=true)

## Vendor dashboard
![alt text](https://github.com/nkwachiabel/Procure_to_Pay/blob/main/Images/Vendor%20dashboard.jpg?raw=true)

This dashboard shows information relating to a particular dashboard by using the filter at the top right of the screen.  

## Case details
![alt text](https://github.com/nkwachiabel/Procure_to_Pay/blob/main/Images/Case%20dashboard.jpg?raw=true)

This dashboard shows information relating to a particular case by using the filter at the top right of the screen.

# Process improvement
Based on the analysis, areas for improvement were identified such as:
* Process redesign: Due to the lack of approval for majority of these payments, the procurement process should be redesigned to ensure that all payments are pre-approved. This is important to ensure segregation of duties and reduce the possibility of fraud. At least, there should be a limit on the amount that can be paid without approval and a limit when both 1st and 2nd approval is needed. It was noted that when a procurement needs to be reversed, a new process is initiated. The process should be redesigned such that the actual procurement raised should be reversible. This will enable proper monitoring of these requests. Regarding recurring payments, further investigation should be carried out especially on those recurring payments without approval. On the other hand, a separate process for recurring process can be designed such that it needs to be approved initially before the first payment.
* Reducing approval delays: It takes an average of 4 days for the 2nd approver to carry out their task after the 1st approver is done. This causes unnecessary delays to these requests. It is recommended to set come up with maybe a notification to always remind the 2nd approver that a request is pending.
* Avoid weekend payments: There were 6 instances where a payment was peformed on a weekend. This can be a fraud indicator. It is important to put in place system blockers to avoid instances such as this unless absolutely necessary.
* Performance monitoring: A lot of time is spend between some activities such as Document Date. Lastly, a performance monitoring process should be put in place to track KPIs relating to the procurement process.

# Limitation
The dataset provided no information about the users in the process. 

# Repository structure
* 'Data/': Contains the data used for analysis
* 'Notebook/': Jupyter notebook detailing the data cleaning, process discovery and analysis
* 'Output/': Includes the PowerBI output and a PDF file

# Contributions
Contributions to this repository are welcome! If you encounter any issues or have suggestions for improvement, please open an issue or submit a pull request.

# Contact
For any questions or inquiries, please contact nkwachiabel@gmail.com
