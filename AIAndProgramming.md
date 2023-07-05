# A journey to the center of the AI bubble

## Software has swallowed the world and now AI is swallowing software

I have had several conversations with developers across the seniority spectrum about the impact AI is having on programming and the potential impact it will have on their jobs. As a very broad generalization, there are three distinct patterns:

* The more senior someone is, the less they are worried about losing their job.
* The more someone has used it, the less convinced they are that it's ready to take anyone's job.
* The more cynical someone is, the more concerned they are that companies will use it, regardless of its effectiveness, to save money.

Personally, I see a lot of potential in AI to help create software that is more tested, accessible, and valuable. While AI still needs supervision, it offers an opportunity for developers and companies to use it to increase the amount of time available to work on tasks that never seem to be prioritized: improving site accessibility, enhancing testing coverage, and working on those little touches that make software fun to use.

My plan for this series of blogs is to examine ways we can incorporate AI into our development process to improve developer productivity by allowing more focus to be placed on creating better software.

The technology behind the AI (ChatGPT) making the most waves currently is known as a Large Language Model (LLM). In simplistic terms, LLMs are like libraries filled with millions of books on many different topics. When you ask it a question, it looks up all the books about the source in the library and provides an answer by guessing what comes next in your sentence based on the responses provided by the books in its library.

A couple of key things to note here, if the right "books" don't exist in the library, the LLM (with some exceptions) can't answer the question.  The AI doesn't understand the books the way humans do. Its answers are always predictions, meaning they are not always correct. When the AI provides an incorrect explanation, it is called a hallucination.

There are a few significant barriers to AI being a useful tool in a developer's toolkit:

1. Providing it with enough context to understand your codebase.
2. The mindset required to review code is quite different from the mindset required to write it. Effective code review requires experience and a strong attention to detail.
3. Because the code is not being tested as it is written, bugs will creep into the code that would ordinarily be caught by a programmer slowly iterating and testing at the same time.
4. It can only use knowledge that exists in the dataset used to train it. ChatGPT, for example, only has knowledge of coding libraries and tools that existed before September 2021, and it can only use versions of them that were created before then.

There are a few advantages to using it though:

1. It is fast. Even GPT 4.0 is quick compared to writing code yourself.
2. It won't get bored doing menial tasks.
3. It has a very broad, albeit somewhat incomplete, knowledge that can quickly lay a base for new tasks.

The key to adding AI into your workflow is to find tasks that fit within this set of requirements. Here are some things I have used ChatGPT for in the past two weeks. All of these tasks required me to provide a minimum amount of context to the AI:

1. Refactor a file to use a different technology, switching from Jest to Chai for example.
2. Quickly typing a file.
   * It is unlikely to complete the job but will provide a solid base to improve upon, and as long as you have strict mode turned on, it should highlight any errors it has made.
3. Generating HTML files to be sliced up into React components using a CSS library.
4. Breaking a larger file down into components.
5. Improving your tickets and feature files by catching mistakes.
6. Providing a starting point for Docker and build pipelines.

These simpler suggestions can provide some quick wins, but the true value is in looking for ways we can improve a large chunk of our workflow while mitigating the issues mentioned above. I see a massive opportunity to integrate AI into a Behavior Driven Development (BDD) workflow. BDD feature files provide enough context for an AI to create an effective test suite for us to iterate against. Writing tests is a task no one likes doing, and because we are developing against those tests, it will allow us to catch any hallucinations or oversights made by the AI.

In part two, we will look at a BDD workflow and start discussing ways we can introduce AI.
