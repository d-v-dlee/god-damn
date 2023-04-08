---
title: "Words are Cheap: Generative AI for the essays you were never going to write"
date: 2023-04-07
---

## Intro
Consume, consume, consume. One day I'll write something, I tell myself. Just wait, as soon as I connect all my ideas, all of my haters will see the true power of my note taking system!

Well, I won't really be writing much here but in this blog, I'll be showing you how to leverage OpenAI, Pinecone, Langchain, Sentence Transformers and your Roam database notes to automatically generate an essay. There are already lots of really helpful tutorials using similar tools but the cool thing here is that:
1. The content of the essay is based on your notes and highlights, not the LLMs (therefore avoiding potential hallucinations)
2. We combine Langchain with GPT's weird compression language to fit lots of tokens into prompts
3. You can get even more creative with the chain combinations used here

![Alt Text](/assets/images/2023-04-07-note-takeeer.jpg)

## High-Level
The link to the github repo is [here]([https://www.example.com/](https://github.com/d-v-dlee/langchain-pinecone-roam)) but at a high-level, it works like this.

### Set up Pinecone database
1. Export your notes from Roam
2. Preprocess your notes using LangChain and vectorize them using Sentence Transformers
3. Upload the vectors and associated metadata (author, title, text) into Pinecone

Once you have a vector database, you can pass queries to it and receive back results based on similarity to the query. For example, if you searched "life of thomas jefferson", depending on whats in your database, it might return information related to presidents, the founding fathers, democracy, etc. The fact that you get information that is semantically meaningful, versus keyword based, means you can get much better searches. The connections via semantic search does what graph-based knowledge bases were supposed to do, without all the annoying keyword tagging.

But we're going to go beyond research. Instead, we're going to build a flow to mimic "real" essay writing and perform multiple steps of research, summarization, and synthesis. The cool step here is the compression trick at the end to fit all of our intermediate outputs into our final summarization prompt.

### Essay Generation
1. Receive user query for research topic.
2. Convert query to vector and find top 5 similar results.
3. Summarize the 5 texts in parallel using LangChain and GPT3.5-turbo.
4. Generate a new topic for research based on the summaries, also in parallel.
5. Compare current and proposed topics using cosine similarity.
6. Select 3rd most similar topic as next query for added surprise.
7. Repeat steps 2-6 for 5 rounds.
8. Compress 25 summaries (5 for each topic) into smaller token representation.
9. Pass 5 topics and compressed summaries into final summarization prompt.
10. Receive essay with citations.

## In action, for real, for real

Getting your essay takes 3-5 minutes (depending on how nice GPT-4 API is feeling) and is as simple as:
```
from pinecone_handler import PineconeHandler
pch = PineconeHandler()
research_results = pch.research_and_summarize('What are the dark triad traits?')
```

Here, I enter a research question based on the book **Why Men Behave Badly.** From there, its next four queries are:
* the psychology behind attraction and mate selection in relation to personality traits and social behavior.
* psychology of human mating behavior
* the psychological and sociological effects of sexual competition between men and women.
* evolutionary psychology and gender dynamics in sexual selection.

One thing to note is that the queries are different each time the process is run. This is because we use GPT3.5-turbo to suggest potential tangential topics for each summary, including some surprises. From these suggestions, we choose a new topic each round, adding serendipity to the research, summarization, and synthesis process.

At the end of this stage, we have 25 different summaries (a summary for each of top-5 most similar results each of the five topics) and now the bottleneck is token length. GPT-4 supports a max of ~8k tokens. Twenty-five summaries are either above 8k tokens or right under it, leaving too few tokens for a response. 

Here, [Gfodor's prompt](https://twitter.com/gfodor/status/1643415357615640577) comes to the rescue. Using it shrinks the 25 summaries into ~3700 tokens, a >50% reduction! We can pass this compressed text into our final prompt.

![Alt Text](/assets/images/2023-04-07-shoggoth.jpg)

### Essay

The Dark Triad and Mate Selection: An Evolutionary Perspective

The dark triad traits - narcissism, Machiavellianism, and psychopathy - have been extensively studied for their impact on various aspects of human behavior, including romantic relationships. In particular, these traits are associated with manipulative tactics, domineering attitudes, and a lack of empathy, which can result in effective yet destructive short-term mating strategies. This essay will explore how dark triad traits intersect with the psychology of mate guarding behavior, gender-based violence prevention, and the evolution of gender roles and mate preferences.

The psychology of mate guarding provides a window into how dark triad traits can influence romantic partnerships. Individuals high in dark triad traits often engage in manipulative and controlling behaviors, such as increased stalking and underreporting their abusive actions. This can lead to a strain in relationships, with men lowering their standards in a bid to attract partners while women compete for the attention of the top 20% of desirable men. This dynamic can result in a power imbalance, where women in relationships with dark triad individuals experience increased vigilance and jealousy, potentially leading to isolation from support networks and increased exposure to violence.

Understanding gender-based violence and its prevention is an essential aspect of combatting the destructive influence of dark triad traits. Research has consistently shown that evolutionary processes have shaped the ways in which males and females interact, with men often valuing physical attractiveness and women prioritizing resources and stability. However, these innate preferences can become distorted by the interplay of gender-based stereotypes and cultural norms. For example, women's acceptance of unwanted advances may be tied to perceptions of their attractiveness, whereas men's confidence may be misconstrued as harassment. Furthermore, young liberal women may experience increased mental health issues and rely on victim language, hindering their ability to develop resilience and maintain boundaries in relationships.

Throughout human evolution, the reproductive strategies of men and women have diverged, resulting in distinct gender roles and mate preferences. Men's desire for power and resources often drives competition and dominance, while women's role as the primary caregivers for offspring has led to a preference for partners who can provide protection and resources. Alongside these differences lie commonalities, such as the importance of intelligence and behavioral flexibility in potential mates. The interplay of evolutionary pressures and cultural norms has shaped the development of these mate preferences, with studies showing that men find a variety of women attractive, yet women may view the average man as below their threshold for attractiveness.

In conclusion, dark triad traits intersect with various aspects of human romantic relationships, from mate guarding to gender-based violence prevention and the evolution of gender roles and mate preferences. Understanding the underlying psychological processes and evolutionary pressures that drive these behaviors can help inform interventions to promote healthier and safer romantic partnerships.

### Citations for essay
* When Men Behave Badly by David Buss
* A Hunter-Gatherer's Guide to the 21st Century by Heather Heying and Bret Weinstein
* No One Expects Young Men To Do Anything and They Are Responding By Doing Nothing by Rob Henderson

## Takeaways

The cool thing about this method is that you can quickly produce several different essays, exploring the connection between different but related topics, using the same start topic. Because the true benefit of writing is probably the process versus the final output, these outputs should really serve as a basis for further exploration and discovery for writing. But I'm not going to do that. What I will do though is play with the LangChain chains and compression prompts. 
