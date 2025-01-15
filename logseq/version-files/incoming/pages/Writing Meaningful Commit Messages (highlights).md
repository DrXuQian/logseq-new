title:: Writing Meaningful Commit Messages (highlights)
author:: [[Stratospheric newsletter]]
full-title:: "Writing Meaningful Commit Messages"
category:: #articles
url:: https://reflectoring.io/meaningful-commit-messages/

- Highlights first synced by [[Readwise]] [[Jan 31st, 2023]]
	- Being a new member of a team and working on projects we haven’t seen before has its challenges. If we have a task to add some logic to some part of the code, previous good commit messages can help us find out where and how to add the code. ([View Highlight](https://instapaper.com/read/1574489839/21825636))
	- The proper commit message can save a great deal of time finding the recent changes related to a bug. ([View Highlight](https://instapaper.com/read/1574489839/21825637))
	- If we fix a bug or add a feature we will probably completely forget about it a month or two later. It’s not a good idea to think that if it’s not clear for others, they can ask us about it. ([View Highlight](https://instapaper.com/read/1574489839/21825638))
	- being consistent in our commit message produces compound results over time. ([View Highlight](https://instapaper.com/read/1574489839/21825640))
	- The perfect commit message should have certain qualities:
	  
	  It should be understandable even by seeing only the header of the message (we’ll talk about the header soon).
	  It should be just enough, and not too detailed.
	  It should be unambiguous. ([View Highlight](https://instapaper.com/read/1574489839/21825641))
	- it’s good practice to separate it into several commits. In other words: we don’t want to commit a change that changes too much. ([View Highlight](https://instapaper.com/read/1574489839/21827983))
- New highlights added [[Feb 1st, 2023]] at 9:45 AM
	- it’s good practice to separate it into several commits. In other words: we don’t want to commit a change that changes too much.
	  
	  If we commit two changes together, for example, a bug fix and a minor refactoring, it might not cause a very long commit message, but it can cause some other problems.
	  
	  Let’s say the bug fix created some other bugs. In that case, we need to roll back the production code to the previous. This will result in the loss of the refactoring as well. It’s not efficient, and it’s not atomic. ([View Highlight](https://instapaper.com/read/1574489839/21828024))
	- The commit message should describe what changes our commit makes to the behavior of the code, not what changed in the code. We can see what changed in the diff with the previous commit, so we don’t need to repeat it in the commit message. ([View Highlight](https://instapaper.com/read/1574489839/21828168))
	- It should answer the question: “What happens if the changes are applied?". If the answer can’t be short, it might be because the commit is not atomic, and it’s too much change in one commit. ([View Highlight](https://instapaper.com/read/1574489839/21828172))
	- Let’s start with Git conventions. Other conventions usually have the Git conventions in their core.
	  
	  Git suggests a commit message should have three parts including a subject, a description, and a ticket number. Let’s see the exact template mentioned on Git’s website:
	  
	  Subject line (try to keep under 50 characters)
	  
	  Multi-line description of commit,
	  feel free to be detailed. (Up to 72)
	  
	  [Ticket: X] ([View Highlight](https://instapaper.com/read/1574489839/21828192))
	- Type
	  The type of commit message says that the change was made for a particular problem. For example, if we’ve fixed a bug or added a feature, or maybe changed something related to the docs, the type would be “fix”, “feat”, or “docs”.
	  
	  This format allows multiple types other than “fix:” and “feat:” mentioned in the previous part about conventional messages. Some other Angular’s type suggestions are: “build:”, “chore:”, “ci:”, “docs:”, “style:”, “refactor:”, “perf:”, “test:”, and others. ([View Highlight](https://instapaper.com/read/1574489839/21828226))
	- Summary
	  As Angular suggests: “It should be present tense. Not capitalized. No period in the end.”, and imperative like the type. ([View Highlight](https://instapaper.com/read/1574489839/21828229))
	- Let’s look at some summary examples:
	  
	  Right: fix: add authorization for document access
	  Wrong: fix: Add authorization for document access (capitalized)
	  Wrong: fix: added authorization for document access (not present tense)
	  Wrong: fix: add authorization for document access. (period in the end) ([View Highlight](https://instapaper.com/read/1574489839/21828230))
	- Body
	  The format of the body should be just like the summary, but the content goal is different. It should explain the motivation for the change.
	  
	  In other words, it should be an imperative sentence explaining why we’re changing the code, compared to what it was before. ([View Highlight](https://instapaper.com/read/1574489839/21828232))
	- Footer
	  In the footer, we can mention the related task URL or the number of the issue that we worked on: ([View Highlight](https://instapaper.com/read/1574489839/21828234))
	- Example One
	  We added a feature to the codebase. It gets the mobile number from the user and adds it to the user table. All positive and negative tests are ready except one. It should check that a user is not allowed to enter characters as the mobile number. We add this test scenario and then commit it with this message:
	  
	  test: add negative test for entering mobile number
	  
	  add test scenario to check if entering character as mobile number is forbidden 
	  
	  TST-145 ([View Highlight](https://instapaper.com/read/1574489839/21828240))
	- Example Two
	  We realized that getting a parameter from the API output is going to clean up our code. So we did the refactoring and now the new input is mandatory. This means the client should send this specific input or the API does not respond. This refactoring made a MAJOR change that is not backward-compatible. We commit our change with this commit message:
	  
	  refactor!: add terminal field in the payment API  
	  
	  BREAKING CHANGE: add the terminal field as a mandatory field to be able to buy products by different terminal numbers
	  
	  the terminal field is mandatory and the client needs to send it or else the API does not work
	  
	  PAYM-130 ([View Highlight](https://instapaper.com/read/1574489839/21828241))
	- Example Three
	  We add another language support to our codebase. We can use a scope in our commit message like this:
	  
	  feat(lang): add french language
	  The available scopes must be defined for a codebase beforehand. Ideally, they match a component within the architecture of our code. ([View Highlight](https://instapaper.com/read/1574489839/21828250))