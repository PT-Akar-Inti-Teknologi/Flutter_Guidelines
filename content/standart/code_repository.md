# Branching

## Main Branch

This is where our main progress resides. Committing directly to the main branch is discouraged. Instead, update your progress on your own branch and then create a pull request into the main branch.

## Feature Branches

For each backlog item you're working on, create a feature branch in the format <feat>/<feat_name>.

## Feature Branch Base

A base feature branch helps streamline the process of integrating your code with your coworkers' when multiple developers are working on the same feature. While committing directly to the main branch is not recommended, you can commit your code directly to the base feature branch. This makes it easier for both of you to access updates from each other.

> Example of a feature branch base: feat/auth/base

## Feature Branch Backlog

This is where you should do your work and commit regularly. This branch should be created based on its corresponding base branch. For example, if you're tasked with creating a login page within the auth module, create a feature branch named feat/auth/login from the feat/auth/base branch. Then, continue your work on the login branch. When you've finished your work, create a pull request from this branch into the main branch to integrate your completed task with others.

# Commit message

## Why

- A simple navigation through git history (e.g: ignoring style changes)
- Automatic generation of the changelog
- Commit based CI/CD pipeline step (e.g: don’t trigger build and deploy if the change is only about documentation or styling)

## Convention

```
Use the following format,

type(scope): subject

body
```

### type is mandatory and must be one of the following:

- feat: A new feature
- fix: A bug fix
- build: Build related changes (eg: npm related/ adding external dependencies)
- chore: A code change that external user won’t see (eg: change to .gitignore file or .prettierrc file)
- docs: Documentation related changes
- refactor: A code that neither fix bug nor adds a feature. (eg: You can use this when there is semantic changes like renaming a variable/ function name)
- perf: A code that improves performance
- style: A change that is related to code style or lint (e.g: fix indentation, missing semicolons or whitespaces)
- test: Adding new test or making changes to existing test

### scope is optional and must follow these rules:

- A phrase describing parts of the code affected by the change. For example “(seeder)” or “(middleware)”
- Small caps with dash (-) as separator. For example “(web-server)” or “(storage-service)”
- It can be empty when the change is a global or difficult to assign to a single component, in which case the parentheses are omitted

### subject is mandatory and must follow these rules:

- In English
- Use a single space after colon
- A single sentence only
- Imperative, present tense (eg: use “add” instead of “added”, “adding” or “adds”)
- Don’t use dot (.) at the end
- Don’t capitalize first letter

### body is optional and must follow these rules:

- In English
- Use a blank line to separate with the subject
- Used only when further explanation is required or change contains several parts
- Maximum 72 characters
- Use bullet points if needed
- Multi line is ok
- Capitalize is ok

### Examples

> fix(middleware): ensure Range headers adhere more closely to RFC 2616

> feat(store): add multi shift support to store operational hours

- Modify Update Store Operational Hours API endpoint
- Update query list store to support multi shift

> feat(storage): add AWS S3 support

> refactor: move all auth functionalities to a separate module

> chore: release 2.0.1

> build: bump axios to 0.21.1

> style: replace CRLF to LF
