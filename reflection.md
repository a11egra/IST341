# Initial Vision

I started with the Admissions office’s desire to automate our “lead classification” ratings. We mark each “person record” as hot, warm, or cold – manually – and the “true definition” of these terms has always been murky and was left up to the recruiters’ discretion. We looked into automating these labels, so that they would update someone to “hot” if they attended an event or “cold” if they never opened our emails, but it quickly became clear that because we did not have a solid definition of the terms, we didn’t have a reliable way to create the automation rules. Initially, I think I wanted this project to be the automation, but I quickly realized that doesn’t make any sense because the automation needs to be in Slate, not Python.

When we did Random Forests, I realized that it would be more useful to find the concrete definitions – feature importances – that we could use to define our rules. A decision tree makes the most sense, intuitively, when it comes to simulating a human's decision to enroll in graduate school. (Random forests allow for nuance). The real a-ha moment came when I realized the best insight was not to define the rules themselves, but to find the nuance that wouldn’t otherwise be obvious – and, specifically, the nuance between different divisions at CGU. 

# Progress & Final Capabilities

The random forest simulator was the easiest part, since it’s really a lot of copy-paste-replacing. On my first run of the random forest model, it could really only get up to about 65% success in predicting a student’s outcome. Everything after that was making definitions based off of reasonable assumptions: 

+ Enrolled vs. Not Enrolled is a binary question (I don’t care how or why they didn’t enroll, it’s a simple yes/no)
+ Dropping programs that don’t have enough students (We can’t analyze Botany if only 3 students enrolled in 2023 – there’s not enough data to make a meaningful prediction)
+ Choosing to ignore dual degrees (this hurt my soul because of how much we have to pay attention to this at work, but ultimately that should be a phase 2 issue)
+ Widening the specifications: instead of comparing every single degree, let’s just analyze Master’s/PhD/Professional Doctorate (I had to make some choices about MFAs being Professional Doctorates, while MBAs remained Masters’)

The next major choice was to separate the data into the 8 divisions (CISAT, DBOS, DPE, DSM, IMS, SAH, SCGH, SES) and create a separate model for each. Once this was done, the average accuracy jumped to around 80%, and the feature importances were notably different! Many of the importances turned out to be either a) things I already knew and could now back up with data, b) things that surprised me initially but made sense upon further consideration, or c) revealed the flaws in my assumptions.

Creating ‘for’ loops for efficient and successful indexing (or as I am now calling it, “translating” – from human language, to computer language, and back again) ended up being the biggest part of this project. It was fascinating to see how indexing and dataframe construction (shape+structure) is really the core of what allows you to create a program, algorithm, or model. 

# Possibilities for Extension

The most obvious next step is to include more features and a broader dataset/timeframe. The amount of data we have in Slate is VAST - there are hundreds of items we track along a student’s journey (cold calls, staff interactions, faculty interactions, length of time a student waited for a decision, fellowships and/or external funding, students’ physical distance from CGU and whether or not they had to relocate to attend, online/hybrid/in-person programs, undergraduate schools/degrees received, the list goes on). The heavy lift there is the translation/indexing – the data that comes out of Slate is not always “clean” (it will be some sort of string) and will need to be translated either before exporting it out of Slate or within a Python program.

I think there’s also a LOT of potential for really interesting and insightful visualizations. Beyond bar graphs, we can compare feature importances within programs (instead of comparing programs to each other), pie charts, maybe even heat maps… Anything to make the data more palatable.

# What I’d Do Differently

My greatest regret here is the way I did the “top 3” most common values for each feature. I really just didn’t think about that deeply until I was actually putting together the slideshow. While the top 3 values for each feature from the entire applicant pool can absolutely be insightful in some contexts, what really makes sense to pair with the feature importances is the top 3 values from the ENROLLED student pool. It wouldn’t even be that hard to do, I just didn’t think of it! I could also do a decision tree model and actually export an image of the decision tree itself, which would not only look cool, but would probably really help my case if I were to present something like this to leadership.

My second regret is the same regret I always have – I get so caught up in making the code as efficient as physically possible (with negligible visible effects; it doesn’t make the code run any faster, it just makes it easier to read… sometimes) that I end up spending more time on the process than on the results. In many contexts, this is a strength, because it’s definitely not the most common approach in higher ed administration. In this context, though, the code itself is not the end goal – it’s what the code produces. And if writing 8 lines of the same code to account for each division is quicker (for me) than creating a fancy ‘for’ loop, I think I need to get better at forcing myself to write the 8 lines and move on!
