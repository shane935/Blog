# AI and Programming

Software has swallowed the world and now AI is swallowing software.  

I have had a few conversations with developers all across the seniority spectrum about the impact AI is having on programming and the potential impact it will have on their jobs.  As a very broad generalization there are three distinct patterns

* The more senior someone is the less they are worried about losing their job
* The more someone has used it the less convinced they are its is ready to take anyone's job
* The more cynical someone is the more concerned they are companies will use it regardless of effectiveness to save money

Personally I see a lot of potential in AI to help create software that is more tested, more accessible and more useful.  My plan for this series of blogs is to look at ways we can put AI to use as a part of our development process that improves developer productivity by allowing more focus to be placed on making better software.

The technology behind the AI's making the most waves currently is know as a Large Language Model (LLM). In simplistic terms LLM are like a library filled with millions of books on many different topics, when you ask it a question it looks up all the books about the source in the library and provides an answer by guessing what comes next in your sentence based on the responses provided by the books in its library.

A couple of key things to note here, if the right "books" don't exist in the library the LLM (with some exceptions) can't answer the question and the AI doesn't understand the books in the way humans do, its answers are always predictions meaning they are not always correct. When the AI provides an incorrect explanation it is called a hallucination.

There are a few huge barriers to AI being a useful tool in a developers toolkit:

1. Providing it with enough context to understand your codebase, without even mentioning privacy issues.
2. The mindset required to review code is quite different from the mindset required to write it, it requires a lot of experience and attention to detail.
3. Because the code is not being tested as it is written bugs will creep into the code that would ordinarily be caught by a programmer slowly iterating and testing at the same time.
4. It can only use knowledge that exist in the dataset used to train it, ChatGPT for example only has knowledge of coding libraries and tools that existed before september 2021 and it can only use versions of them that were created before then.

There are a few advantages to using it though

1. It is fast, even GPT 4.0 is quick compared to writing code yourself
2. It wont get bored doing menial tasks
3. It has a very broad knowledge if somewhat incomplete that can quickly lay a base for new tasks

The key to finding adding AI into your workflow is to find tasks that fit with in this set of requirements, here are things i have used ChatGPT for in the past two weeks. All of these tasks require a minimum amount of context and with the exception of the HTML and components have required some level of iteration on top of what was produced

1. Refactor a file to use a different technology, switching from jest to chai for example.
2. Quickly typing a file
    * It is unlikely to complete the job but will provide a solid base to improve upon and as long as you have strict mode turned on it should highlight any errors it has made
3. Generating HTML files to be sliced up into React components using a css library
4. Break a larger file down into components.
5. Improving your tickets and feature files by catching
6. Providing a starting point for docker and build pipelines

Some tips for getting the most out of it:

1. Expand the response window so you can read the code as it writes it.  I have been using this [gist](https://gist.github.com/shane935/34ef6905141e2dc8de68bc200f246595)
2. Ask it to act as if it is pair programming and provide comments as it writes, this will give you some context for what it is doing and help you remain engaged and in a coding mindset ast it writes
3. Use GPT 4 if possible, it gives better responses and its slower speed allows you to follow along as it writes in a more natural way

These simpler suggestions can provide some quick wins but the true value in here is in look for ways we can improve a large chunk of our workflow while mitigating against the issues mentioned above.  I see a massive opportunity to integrate AI into a BDD workflow. BDD feature files provide enough context for an AI to create an effective tests suite for us to iterate against, writing tests are a task no one likes doing and because we are developing against those tests it will allow us to catch any hallucinations or oversights made by the AI.  


Influence of AI
Positives and Negatives
Hypothisis 