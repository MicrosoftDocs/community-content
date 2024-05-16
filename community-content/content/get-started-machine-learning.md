---
title: Get started with machine learning #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: The article is an introductory guide to machine learning, covering its definition, types, applications, roadmap, careers, and resources. #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: bumie-e #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: machine-learning #Required; request from Microsoft admin. 
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 03/19/2024 #Required; mm/dd/yyyy format.
contributor-type: community #Required; don't change.
---

# Getting started with machine learning

Thanks to artificial intelligence (AI), Machine Learning (ML) is more popular than ever. Whether it's Open AI GPT or Humane AI Pin, Claude, financial forecast, intelligent games, or object tracking, ML has seen broader adoption in solving specific and generic problems. Its applications far extend Generative AI, automating autonomous work and paving the way for new research and employment.

You probably heard about ML through a friend, social media, podcast, blog, or otherwise. You should know how to get started, whether it's a viable field to choose a career path or increase your salary. You've come to the right place. In this article, I'll discuss why you should use ML, the ML roadmap, ML careers, career advice, and communities that might be helpful.

## Why ML

ML is changing the way we work and do business. Research over the years has produced some of the most outstanding technologies you've heard of, especially in areas like medicine, biotech, chip development, and business-specific tools. It isn't a shiny development but a field that has seen cumulative work for a long time. Recent computing power and funding developments have enabled us to see the groundbreaking results we see today.

So why ML?

- It's an exciting, challenging and fast-evolving field that requires you to stay abreast of recent developments.
- It's a great choice if you love mathematical and technical concepts.
- It can be applied to many business problems, allowing you to work with diverse companies.
- Of course, it has one of the most paying jobs.
- Easy to pivot from software engineering

Now that you are inspired let's get into the nitty-gritty.

[In my previous article](ml-for-those-who-don't-know-anything-about-ml.md), I defined ML as a field in AI with a focus on developing algorithms that make machines perform tasks without explicitly telling them to. Similar to a typical software project, ML projects start with defining the business problem, and the expected results. Having clear objectives and goals before starting any project is essential, as they determine the kind of data, algorithm, and result.

### Types of ML algorithms

You can use ML to solve many but not all problems. A typical ML model takes data, finds patterns and returns results as the output. For example, you can build an ML system to take a dataset containing store purchase records and predict what items would be in demand for the coming weeks. In this use case, the input here is the dataset. You select a suitable algorithm to train on the data, which you call a model, and it gives you the products that are likely to be in demand as the output.

:::image type="content" source="media/get-started-machine-learning/ml-algo.png" alt-text="Image showing the basic workings of a machine-learning system.":::

ML algorithms can be classified into three types: classical ML, deep learning architectures, and reinforcement learning.

#### Classical ML

Otherwise known as traditional ML, classical ML refers to algorithms that primarily work with tabular datasets. They are widely and commonly used in businesses and typical applications. Classical ML consists of three subtypes:

- **Supervised learning**: In supervised learning, your goal is to help the model learn the patterns in the data that produce a specific result. For example, you teach your model to differentiate between apples, oranges, and mangoes. The target labels are the three classes (apple, orange, and mango). At the same time, the images of the fruit itself are the input data.

    Another example here is employee attribution. What employee should get a promotion, which should get a raise, and which should get nothing? The dataset can contain information about individual employees, including the manager's remarks, and the target includes three classes: get_promoted, get_raise, and do_nothing. This algorithm can be: <br><br>

   - **Classification algorithms**: These are where you are predicting based on categories, labels, or classes, such as classifying fruits in the grocery store.
   - **Regression algorithms**: This is where you predict continuing values, such as the price of a house over five years. 

- **Semi-supervised learning**: It's an ML approach that combines a small amount of labelled data with a large amount of unlabeled data during training. It sits between supervised learning (where all the training data is labelled) and unsupervised learning (where none of the training data is labelled). The basic idea behind semi-supervised learning is to use the large volume of unlabeled data to better understand the underlying structure of the data and help improve the learning accuracy with the small amount of labelled data. This is particularly useful when obtaining a large set of labelled data is too costly or time-consuming, but unlabeled data is plentiful.
- **Unsupervised ML**: In unsupervised ML, you don't give the model any sample of what the target/results look like, but you allow it to find patterns and create groups or classes based on those patterns. It's used in labelling large datasets -- thousands or millions of rows of data that would take forever if a person were to label. It finds data points with similar characteristics and groups them. It can also be used to build recommendation systems where you try to find the most suitable products for customers without having their past purchasing data. Algorithms in this category include:
    - **Clustering**: is a type of unsupervised ML algorithm that groups a set of objects so that objects in the same group (or cluster) are more similar to each other than to those in different groups. It's often used in exploratory data analysis to find natural groupings in data. Examples include customer segmentation, grouping experiment outcomes, or categorising plants or animals.
    - **Association Rule learning**: This is a rule-based ML method for discovering interesting relations between variables in large databases. It is intended to identify strong rules discovered in databases using some measures of interestingness. An example is market basket analysis, where you find sets of products that frequently co-occur in transactions.
    - **Dimensionality reduction**: This technique reduces the number of random variables under consideration by obtaining a set of principal variables. It's often used in the preprocessing stage of ML to reduce the complexity of data, improve performance, and reduce resource requirements. Techniques like Principal Component Analysis (PCA) and t-SNE are commonly used.

#### Deep learning architectures

Deep learning architectures have been quite popular over the past few years, with some applications in self-driving cars and generative AI. They are based on the basic principles of a neural network (with workings inspired by neurons in our brains). 

Although they have the same basic structure of a classical ML algorithm (input, algorithm, and output), they differ in the architecture and capabilities of the system. As the name implies, they're used to train more complex algorithms dealing with complex data types like audio, video, images, and text. Some even work with signals and can detect or make predictions.

Its architecture contains 'hidden layers' that include weights, with which it learns, either through back-propagation or feed-forward mechanism, the patterns in input data with the corresponding output. It can take on much larger and more complex tasks without worrying about feature engineering. This is one of its inner processes: it extracts valuable features. 

Deep learning has three major sub-classes:

- **Computer vision**: This field of AI enables computers and systems to derive meaningful information from digital images, videos, and other visual inputs and act or make recommendations based on that information. It involves tasks like image recognition, object detection, and pattern recognition. It is used in facial recognition, autonomous vehicles, and image restoration applications.
- **Natural language processing (NLP)**: This branch of AI helps computers understand, interpret, and manipulate human language. NLP involves applying algorithms to identify and extract the natural language rules such that the unstructured language data is converted into a form that computers can understand. Typical applications include speech recognition, translation, and sentiment analysis.
- **Generative AI**: This refers to the type of AI algorithm that can generate new content, from text to images, videos, and music. Generative models like Generative Adversarial Networks (GANs) and Variational Autoencoders (VAEs) are widely used. Applications include creating art, synthesising realistic human voices, and generating new molecular structures for drug discovery.

#### Reinforcement learning

Reinforcement learning is a type of ML in which an agent learns to make decisions by performing specific actions and assessing the results of those actions. The agent is rewarded for correct actions and penalised for incorrect ones, thus reinforcing the learning. This learning method is often used in gaming, navigation, robotics, and autonomous vehicle guidance systems.

:::image type="content" source="media/get-started-machine-learning/types-of-ML-algo.png" alt-text="Image showing the classifications of machine learning.":::

## ML roadmap

To get started, you need to:

- Be familiar with Python up till object-oriented programming.
- Have a fair understanding of how to manipulate matrices.

Because the paths in ML learning have slightly different requirements, having a good grasp of the basics is very important. There are various routes into ML: software engineering, data science, or ML directly. A solid data science background is essential because it makes your learning easier. I recommend the [roadmap put together by Steven Kolawle to help you get started](https://twitter.com/_stevenkolawole/status/1624098845150916626).

### Ways to learn ML

- Microsoft Learn
    - [Deep Learning Vs Machine Learning](/azure/machine-learning/concept-deep-learning-vs-machine-learning/?wt.mc_id=studentamb_185863)
    - [Introduction to Machine Learning](/training/modules/introduction-to-machine-learning/?wt.mc_id=studentamb_185863)
    - [Train and evaluate Regression models](/training/modules/train-evaluate-regression-models/?wt.mc_id=studentamb_185863.)
- Online courses
    - Data Camp: [Understanding Machine Learning](https://app.datacamp.com/learn/courses/understanding-machine-learning)
- Book recommendations
    - *The Hundred Page Machine Learning Book* by Andriy Burkov
    - *Python Machine Learning: Machine Learning and Deep Learning with Python, Scikit-learn, and TensorFlow 2* by Sebastian Raschka and Vahid Mirjalili
    - *Deep Learning with Python* by François Chollet

## ML careers

If you're looking to choose a career in ML, you need to decide whether you'd like a research-centric path or you'd like to be more involved in building products. You can't escape from writing codes in research, but you need stronger software engineering skills if you want to work in the industry.

Research-focused careers require academic qualifications, so do many industry positions, but for more engineering-focused roles, the qualifications are lenient. A good grasp of the basics and keeping up with relevant trends can put you in a good position.

There are also industry-focused research labs, which give you the best of both worlds as you work on real-world impact research and might be built into a product if successful. Some examples of these research labs are Google Research, DeepMind, Cohere, and Open AI.

As a general rule, you have to be flexible and knowledgeable about most of the sub-fields in AI, because you might need to combine two or more fields to solve a single problem (for example, image to text).

Here are some roles that might interest you:

- **ML engineer**: Develops ML models and systems. Works closely with data pipelines and deployment infrastructure to operationalise models.
- **Data scientist**: Analyses complex data to find patterns, make predictions, and offer insights. Often, they use ML techniques as part of their toolkit.
- **Research scientist (ML/AI)**: This role is typical in academia and industry research labs. It involves conducting advanced research in ML algorithms and their applications.
- **AI engineer**: Focuses on building AI systems that leverage ML, deep learning, and sometimes, rules-based systems.
- **NLP scientist**: This specialist specialises NLP, which involves interacting with computers and humans through natural language.
- **Computer vision engineer**: Works with image recognition, object detection, and similar tasks to enable machines to interpret and understand the visual world.
- **Deep learning engineer**: Specialises in deep learning architectures, such as convolutional neural networks (CNNs), recurrent neural networks (RNNs), and transformer models, for various applications.
- **Robotics engineer**: This person incorporates ML techniques into robotics to create robots that can learn from and adapt to their environment.
- **ML/AI platform engineer**: Focuses on creating and managing the platforms that facilitate ML and AI processes.
- **ML/AI infrastructure engineer**: Works on the underlying systems and hardware that support ML/AI workloads.
- **Research engineer**: Works on developing new algorithms, models, and application areas. Their role bridges the gap between theoretical research and practical application.

## Some career advice

- Using ML in your projects can be done without starting a career in ML. It's a lot easier these days when companies provide API endpoints that you can use in your project to leverage AI.
- You don't need to understand the math or basics of how algorithms work to leverage AI in your projects. Tools like the Azure AI service give you flexibility.
- Suppose you would instead pick it as a career focus for the next few years. Consider a top-down approach (starting by using the algorithm and then learning how things work) or a bottom-up approach (learning from the ground up). Top-down does have a disadvantage, as you might get carried away with building projects without a solid understanding of how they work.
- It would help if you had some background in mathematics and statistics, primarily linear algebra and calculus. High school knowledge of these topics is sufficient to get you started.
- Many ML roles and internships are biased against MSc and PhD students, though some software engineering-related roles don't require these academic qualifications. You might consider taking a Master's in the field to be better positioned for opportunities.
- When you start learning, take your time to understand the basics. It's tempting to skip steps and start building the actual models, but you must learn the basics.
- If you love to work in a fast-evolving and changing field, take on a good challenge, or want to work at the forefront of technology improvements, then pick a career in ML today.

## Communities that might be helpful

- Kaggle community
- ML Collective
- Cohere AI community
- Zindi community
- Black in AI
- Women in AI
