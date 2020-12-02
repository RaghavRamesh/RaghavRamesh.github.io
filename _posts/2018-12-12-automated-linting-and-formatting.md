---
title: "Automated linting and formatting"
date: 2018-12-12
categories: [tutorial]
tags: [javascript, eslint, prettier]
---

Back when I was working at [99.co](https://www.99.co) on their website and when I was leading a team in JPMorgan to build a React component library, I noticed that there were opinionated styling related discussions. While it's healthy to have discussions and it's important to have a consistent code style across the code base, for the most part the discussions were wasteful. I also came across an anecdote on a message board that said, "if you care enough to correct a style in a pull request, you should be willing to put in the effort to automate it". So I did exactly that when I set up automated formatting in both the teams. Since ESLint also has some stylistic rules, it's important to look at both the configurations together.
## Motivation

### Linting
* To prevent JS errors from creeping into the code base and have consistent formatting across the codebase as the project size and the number of contributors increase
* Static analysis (using linting) is a good first step for testing - essential for a dynamically typed language

### Formatting
* Code reviews can focus on important comments - no more styling related feedback - both the reviewer and receiver don't feel good about giving or receiving such comments.
* Improves developer experience - Applying formatting on save or by letting Prettier watch your files, it's one less thing we need to worry about.
* Check out a comprehensive list of reasons on [Why Prettier?](https://prettier.io/docs/en/why-prettier.html)


## Drawbacks

If you're retrofitting prettier (or any other code formatter) into your rep, `git blame` (without additional flags) may not be helpful since git blame by default shows last commit's author and when prettier formats the files, that history info will be erased from the latest commit. [Check this article](http://www.thisprogrammingthing.com/2016/Git-Blame-Previous-Versions/) on how to view prior versions of a file.

## Setup

### ESLint
- Download package and add to package.json dev dependencies
	- `npm install --save-dev eslint`
- Configuration
	- Create `.eslintrc` in the root of your project directory and paste the following config:
		```
		{
		  "parserOptions": {
		    "ecmaVersion": "2018"
		  },
		  "extends": [
		    "eslint:recommended"
		  ],
		  "rules": {
		    "no-console": "off"
		  },
		 "env": {
		    "browser": true
		  }
		}
		```
	- Update it according to your repo’s requirements
- Add script to package.json
	```
	"scripts": {
	  "lint": "eslint src"
	},
	```
	- Now you can run - `npm run lint`
- Add [ESLint VSCode plugin](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) or configure IntelliJ to look at .eslintrc
	- VS Code
		```
		"eslint.options": {
		  "configFile": "/Users/path/to/project/.eslintrc"
		}
		```
	- IntelliJ has this built-in
		- Settings > Search for "eslint" > configure the ESLint package to point to the `node_modules\eslint` package and modify the config file to the project's `.eslintrc` file

### Prettier
- Download package and add to package.json dev dependencies
	- `npm install --save-dev prettier`
- Configuration
	- Create `.prettierrc` and place it in the root of your project
		```
		{
		  "arrowParens": "avoid",
		  "bracketSpacing": false,
		  "jsxBracketSameLine": false,
		  "printWidth": 80,
		  "proseWrap": "always",
		  "semi": false,
		  "singleQuote": true,
		  "tabWidth": 2,
		  "trailingComma": "all",
		  "useTabs": false
		}
		```
	- Create `.prettierignore` and place it in the root of your project
		```
		node_modules
		coverage
		dist
		build
		.build

		# etc...
		```
- Add script to package.json
	```
	"scripts": {
	  "lint": "eslint src",
	  "format": "prettier --write \"**/*.+(js|jsx|json|yml|yaml|css|less|scss|ts|tsx|md|mdx|graphql|vue)\""
	},
	```
	- Now we can run - `npm run format`. You probably don’t need all of the above file types, remove as needed
- Add Prettier VSCode plugin or run prettier with a watch flag.
	- VS Code
		- Install Prettier extension and add the following to your user settings. The extension will automatically pick up config in the .prettierrc file for formatting.
		```
		"editor.formatOnSave": true,
		"editor.formatOnType": true,
		```
	- Emacs
		- [Format on save](https://github.com/prettier/prettier-emacs)
	- [Or let prettier watch files](https://prettier.io/docs/en/watching-files.html)

### ESLint + Prettier
- Because prettier can automatically fix a lot of stylistic issues in our codebase, it’s not necessary to have eslint check for those and it can actually be kind of annoying if it does. We can use eslint-config-prettier to disable all rules that are made irrelevant thanks to prettier. Remove styling rules from ESLint and let Prettier take care of it by installing this plugin
- Setup
	- `npm install --save-dev eslint-config-prettier`
- Configuration - Update your `.eslintrc` to include
	```
	"extends": [
	   "eslint:recommended", "eslint-config-prettier"
	],
	```
- Since we can’t force everyone to use the prettier integration for their editor, we can add a validation script to verify that prettier has been run on all project files using the `—list-different` flag of prettier
	```
	"scripts": {
	  "lint": "eslint src",
	  "format": "prettier --write \"**/*.+(js|jsx|json|yml|yaml|css|less|scss|ts|tsx|md|graphql|mdx)\"",
	  "validate": "npm run lint && prettier --list-different \"**/*.+(js|jsx|json|yml|yaml|css|less|scss|ts|tsx|md|graphql|mdx)\"",
	},
	```
	- Now we can run `npm run validate`

### Husky + Lint-staged
- To prevent unformatted or buggy code from entering the code base, we can the validate script as a git pre-commit hook. Husky is a git hooks wrapper which simplifies this for us
- Download Husky package
	- `npm install --save-dev husky`
- Configure husky, add npm script
	```
	"husky": {
	  "hooks": {
	    "pre-commit": "lint-staged"
	  }
	},
	```
- As our project grows, running linting across the entire project is unnecessary. We should really only be running across the files that changed. This is where lint-staged comes in handy.
- Install lint-staged
	- `npm install --save-dev lint-staged`
- Configure lint-staged
	```
	{
	  ".js": [
	    "eslint"
	  ],
	  "**/*.*(js|jsx|json|css|md)": [
	    "prettier --write",
	    "git add"
	  ]
	}
	```

This is how the package.json scripts looks like in the end
```
...
"scripts": {
  ...
  "lint": "eslint src",
  "format": "npm run prettier -- --write",
  "prettier": "prettier \"**/*.*(js|jsx|json|css|md)\"",
  "validate": "npm run lint && npm run prettier -- --list-different"
},
"husky": {
	"hooks": {
	  "pre-commit": "lint-staged"
  }
},
...
```

### Finally, Run the validate script in the CI-CD pipeline
Even if validation is bypassed in the pre-commit hook (can be done using `git commit --no-verify`) or if the pre-commit hook fails to run in a particular team member's machine, the validation could be run in the project's CI-CD pipeline to ensure

