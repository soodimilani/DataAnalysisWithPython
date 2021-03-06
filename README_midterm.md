# DataAnalysisWithPython

## Midterm:
The purpose of the midterm exam is to work with StackExchange API to extract the data from the website, save it, and analyze it.

In the Midterm.py file, the question and the required functions have been explained for each step. Here, I will give a big picture of how the analysis has been done.



First, we import required modules. Then we define the functions which are necessary to search and save the data.

## Functions:
- *time_epoch*: StackExchange uses time entries in epoch format. This function converets the date entry to epoch format. You can see an example of the output of this function.
- *search_save_question_tag*: This function search through questions in a specified time interval which contain some tags. It will check if there is anymore data on next pages. In that case, it will search through next page until there is no more data. Then the data will be saved in a json format. *time_epoch* is used inside this function.
- *read_data*: This function takes the path of the file, and reads it.
- *pretty_print* prints the data in a more readable form.


## Analysis 1:
### Steps:
   1. Get the questions with tag python and pandas. Inputs: tag='python;pandas', from_date='2016 01 01',to_date='2016 10 28'.
   2. Read the data using *read_data*.
   3. Parse the body for responses to collect a list of questions and user_id for the questions. To do so, we define two function: *get_question_list* and *get_user_id_list*.
   4. Use the user_id obtained to send a request again to get user profile. Obtain the badges count to determine weightage. Since we have a set of user IDs, we can use */users/{ids}* method to get the profiles. *search_save_user_profile* will take care of this. There are three types of badges: Gold, Silver, and Bronze. We assume the weight of 10, 5, and 2 for each badge, respectively. The output of this step will be a dictionary in which user IDs are keys and the weightage of each user is the value. Since we can get the profiles for at most 100 users, we divide the user IDs in batches with size 100. *user_id_batch_100* will do this. Then we use *id_question* and *top_questions* to find the questions which users with high weightage asked.

A snapshot of the results: 
- data collected for the questions and tags:

		{

		  "is_answered": true, 

		  "tags": [
			 "python", 
			 "pandas", 
			 "numpy"
		  ], 

		  "bounty_closes_date": 1478287199, 

		  "title": "daily data, resample every 3 days, calculate over trailing 5 days efficiently", 

		  "last_activity_date": 1477688683, 

		  "answer_count": 2, 

		  "creation_date": 1477272066, 

		  "score": 3,

		  "link": "http://stackoverflow.com/questions/40209520/daily-data-resample-every-3-days-calculate-over-trailing-5-days-efficiently",

		  "bounty_amount": 500,

		  "owner": {
			 "user_id": 2336654, 
			 "profile_image": "https://i.stack.imgur.com/cZOEs.jpg?s=128&g=1", 
			 "user_type": "registered", 
			 "reputation": 29968, 
			 "link": "http://stackoverflow.com/users/2336654/pirsquared", 
			 "accept_rate": 96, 
			 "display_name": "piRSquared"
		  }, 

		  "view_count": 67,

		  "last_edit_date": 1477686080, 

		  "question_id": 40209520
		}


- question_list: ['How can I replicate excel COUNTIFS in python/pandas?',.....]
- user_id_set: [5906433, 5552408, 3760132,.... ]

- data collected for user IDs and badge counts:

		{
		
		  "is_employee": false, 
		  
		  "last_access_date": 1476725496, 
		  
		  "user_id": 237696, 
		  
		  "account_id": 85196,
		  
		  "badge_counts": {
		  
		    "bronze": 71, 
		    "silver": 38, 
		    "gold": 4
		  },
		  
		  "last_modified_date": 1477424115, 
		  
		  "profile_image": "https://i.stack.imgur.com/cUqoH.jpg?s=128&g=1", 
		  
		  "user_type": "registered", 
		  
		  "reputation_change_day": 0, 
		  
		  "creation_date": 1261583297, 
		  
		  "reputation_change_quarter": 89,
		  
		  "reputation_change_year": 901, 
		  
		  "reputation": 7849, 
		  
		  "link": "http://stackoverflow.com/users/237696/mr-sk", 
		  
		  "location": "Bandar Seri Begawan, Brunei", 
		  
		  "accept_rate": 80, 
		  
		  "display_name": "mr-sk", 
		  
		  "reputation_change_month": 89, 
		  
		  "website_url": "http://www.nycdayz.com", 
		  
		  "reputation_change_week": 0
		  
		}
		
		
- user IDs and their weightage: [(1179880, 1118), (344286, 718), .... ]

- user IDs and questions they asked:  {1785871: ['Multi-dimensional/Nested DataFrame in Pandas',   'Multi-dimensional/Nested DataFrame/Dataset/Panel in Pandas'],  4661264: ['pandas Python 3.5.1: incremental developement using the same dataset, changed a column name but on running script, old name remains in the dataframe'], .....}

- Top 5 questions and the users who asked them: 

		User 1179880 with weightage = 1118 >>> Question:

		['Faster method of looping through pixels', u'pandas.set_option to give detail of Dataframe rather than returning frame', 'Applying different aggregate functions when using pivot_table', 'Complex pivot and resample', u'Marker colour still not showing up', u'Summary data for pandas dataframe', 'Is this approach &quot;vectorized&quot; - used against medium dataset it is relatively slow', 'More elegant conversion of Postgres data within pandas Dataframe', 'Why are these tuple values seemingly losing their precision', 'Why is apply not being applied', 'kNN Classifier - importance of DataFrame column order - is this a scikit bug, pandas bug or by design?', 'Why can I not always import pandas', 'Rearrange the columns of a pandas DataFrame so that the column with highest total is first', 'Add 3 columns to DataFrame via map', 'Merge DataFrames based on index columns', 'Inserting columns to existing DataFrame']


		User 344286 with weightage = 718 >>> Question:

		['How do I compute the counts of one column based on the values of two others in Pandas?']


		User 712997 with weightage = 473 >>> Question:

		['Subplots with groupby DataFrame in pandas?']


		User 854739 with weightage = 440 >>> Question:

		['Pandas merge not keeping &#39;on&#39; column', u'In pandas, set a new column and update existing column', u'Pandas Groupby - naming aggregate output column']


		User 557067 with weightage = 395 >>> Question:

		['pandas.DataFrame: mappings a column of a list of keys to a column of the list of values']


   
## Analysis 2:

### Steps:
   1. For each of the User ID that you have collected, ping the API to get all the tags that user has identified with. Here, we use */user/{ids}/tags* method to get the data. Then we save it. *get_id_tags* function is defined to serve this purpose. *id_tag* gives a dictionary in which the user IDs are keys and tags for each user are values.
   2. Creat a file for each topic, containing user_id, user_name and link to their profile sorted by reputation. *get_user_profile* gives a dictionary which contains the user_name, link, and reputation of each user ID. *topic_user* is also defined to show the users who used each tag sorted by their reputation.
   3. For a given topic (say python), what are the top users who have reputation in that topic. *top_users_tag* prints out the first or the first two top users who are reputated in a specified topic.
   
   
A snapshot of the results: 

- tags associated with each user ID:

		{53468:
		
		{'apt-get',
		  'archive',
		  'ascii-art',
		  'astyle', .....
		},
		
		 237696: 
		 
		 {'.htaccess',
		  u'.net',
		  u'abstract-data-type',
		 } , ...
		 
		 }
		 
- Profile of each user ID sorted by their reputation:

		[(344286,
		
		  {'link': u'http://stackoverflow.com/users/344286/wayne-werner',
		   'reputation': 19239,
		   'user_name': u'Wayne Werner'}),
		   
		 (1179880,
		 
		  {'link': u'http://stackoverflow.com/users/1179880/whytheq',
		   'reputation': 14742,
		   'user_name': u'whytheq'}),
		   ...]

- top users who were active with tag 'python':

		(344286, 
		
		{'link': u'http://stackoverflow.com/users/344286/wayne-werner', 'reputation': 19239, 'user_name': u'Wayne Werner'})
		
		(1179880, 
		
		{'link': u'http://stackoverflow.com/users/1179880/whytheq', 'reputation': 14742, 'user_name': u'whytheq'})


## Analysis 3:
### Steps:
   1. For each of the badge type, find how many users (based on the data you have collected) have badge. To find the number of badges for each user, we can use the result of Analysis 1. In dictionary *user_id_weightage* the users whose values are zero, have no badges. *users_with_no_badge* finds that number.
   2. We want to see what badges are popular among the users. *popular badge* counts the number of each type of badges.
   
A snapshot of the results: 
- users_no_badge : 4 users out of 200 have no badge.
- popular_badges : {'bronze': 5701, 'silver': 1620, 'gold': 319}


# Analysis 4:
## Steps:
   1. For each of the question that is asked, find out the tags attached to it. *search_save_question* uses */questions* method to find this data. *all_tags* gives the tags associated with each question in a dictionary format.
   2. Find how many numbers of answers have been given for each question. For each tag, calculate the number of questions asked and how many times it has been answered. *number_of_answers* gives a dictionary of the questions and the number of answers given to that question. *tag_number_of_questions* and *tag_number_of_answers* will find that for each tag how many questions have been asked, and how many answers given to them, respectively.

A snapshot of the results: 

- all tags associated with the questions collected: 

		{'osx',
		
		 'partial',
		 
		 'iterator',
		 
		 'similarity',...
		 }
		 
- number of answers given to each question:

		{
		'Any way to create a column of tuples from a column of floats in pandas?': 2,
		
		'Python Pandas is it possible to find the max value in a generated series without having to store the series into memory?': 3,
		...}

- number of questions asked with each tag:

		{'python' : 14754
		
		'osx': 11,
		
		 'iterator': 6,
		 
		 'similarity': 2,
		 
		 ...}


## Analysis 5:
### Steps:
1. Find out the user whose questions have been downvoted the most.

A snapshot of the results: 
 - User IDs who downvoted the most:
 
		 [(6612697, -10),
		 
		 (6894212, -8),
		 
		 (5363621, -7),
		 
		 (6375347, -7),
		 
		 (6012341, -7),
		 
		 ...]
		 
		 
