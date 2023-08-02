# Process Discovery and Conformance Checking: Road Traffic Fine Management

This repository contains a data analytics project that utilizes process mining methodology to analyse the real-life event log of an information system managing road traffic fines in Italy. The dataset was gotten from here[https://doi.org/10.4121/uuid:270fd440-1057-4fb9-89a9-b699b47990f5] and it contains fines created between 1st January 2000 to 17 June 2013.

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
The event log consists of 1696 cases between January 2015 and August 2017. Although, majority of the cases happened in 2017. Each procurement contains the following attributes
- <b>Document date</b>: 
- <b>Invoice received</b>: The date the invoice is received from the Vendor
- <b>1st approval</b>: The date the invoice was first approved
- <b>2nd approval</b>: The date the invoice was approved by the 2nd approver
- <b>Posting date</b>: When the invoice was posted to the system
- <b>Payment performed</b>: When the payment was made to the Vendor
- <b>Entity</b>: all cases relates to one entity - 4097 
- <b>Account</b>: The unique identifier of the Vendor. We will be using this rather than the Vendor Name
- <b>Comment</b>: If the process was a reversal of an existing process
- <b>Gross Amount</b>: The gross amount for each case

# Data preparation
The event log data was cleaned and transformed into a suitable format for process mining. This includes the following:
The following activities were carried out:
- Rename the columns to make them easier to reference and understand
- Melt the data to get the dataframe into the required format for process mining
- Remove rows with missing times. This is because from the dataset received, those activities do not occur in that particular purchase order.
- Sort the event log using the unique identifier (<b>Document No.</b>) and Date column. This is to arrange each Purchase document accoring to how the activities were performed (by date)
- Extracted completed cases. I assumed that the process is complete when the payment has been made (i.e., the last activity is <b>Payment performed</b>).
- PowerBI Data format. One of the weakness I have noticed when using PowerBI for process mining is that where two activities are performed on the same date, it sorts these activities alphabetically. For example, if the <b>posting date</b> and <b>payment performed</b> activities were performed on the same day, the payment performed activity would come first before posting date even though in jupyter notebook, posting date comes first. To avoid this, I  included an <b>Event_ID</b> column so that to sort the activities properly in Powerbi.

# Output and Visualisations
## Process discovery
![alt text](https://github.com/nkwachiabel/Procure_to_Pay/blob/main/Images/Process%20Discovery.jpg?raw=true)

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
