# GitHub Workflow Generator

Declare your GitHub Workflows in TypeScript or JavaScript instead of YAML!

![npm (scoped)](https://img.shields.io/npm/v/@wardellbagby/github-workflow-generator?style=for-the-badge)
[![GitHub](https://img.shields.io/github/license/wardellbagby/github-workflow-generator?style=for-the-badge)](https://github.com/wardellbagby/github-workflow-generator/blob/main/LICENSE.md)
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/wardellbagby/github-workflow-generator/Run%20all%20tests?style=for-the-badge)](https://github.com/wardellbagby/github-workflow-generator/actions?query=workflow%3A%22Run+all+tests%22)

## What is it?

Github Workflow Generator is a library that enables you to write templates for your GitHub Workflows in JavaScript or Typescript and use that code to generate valid GitHub Workflows as YAML files.  

Admittedly, that's a long and complex sentence, so let's show a quick example instead:

```typescript
const workflow: Workflow = {
  name: "Simple Workflow",
  on: {
    push: {},
  },
  jobs: {
    echo: {
      name: "Echo",
      "runs-on": "macos-10.15",
      steps: [
        {
          name: "Echoing!",
          run: 'echo "Hello World!"',
        },
      ],
    },
  },
};
```

This will generate this Yaml file:

```yaml
name: 'Simple Workflow'
'on':
  push: {}
jobs:
  echo:
    name: 'Echo'
    runs-on: 'macos-10.15'
    steps:
      - name: 'Echoing!'
        run: 'echo "Hello World!"'
```

This Workflow will run on every new push to any branch, with a single job named "Echo" that runs on a macOS 10.15 runner that prints out "Hello World".

## I understand it, but why do I want this library?

If your Workflow is as simple as the Workflow listed above, you likely wouldn't want or need this library. However, if you have multiple Workflows that need to share steps, this library becomes very useful.

Imagine:

```typescript
const basicSetup: Step[] = [
  {
    name: "Checkout project",
    uses: 'actions/checkout@v2',
  },
  {
    name: "Install project dependencies",
    run: "npm ci",
  },
];

const buildAppWorkflow: Workflow = {
  name: "Build The App",
  on: {
    push: {},
  },
  jobs: {
    echo: {
      name: "Build",
      "runs-on": "macos-10.15",
      steps: [
        ...basicSetup,
        {
          name: "Build",
          run: 'npm run build',
        },
      ],
    },
  },
};

const buildLibraryWorkflow: Workflow = {
  name: "Build The Library",
  on: {
    push: {
        branches: ['special-branch']
    },
  },
  jobs: {
    echo: {
      name: "Build",
      "runs-on": "ubuntu-20.04",
      steps: [
        ...basicSetup,
        {
          name: "Build",
          run: 'npm run build-lib',
        },
      ],
    },
  },
};
```

This is only a small taste as well. You can share whole jobs, if needed.

### What's this I hear about easier versioning for my actions? Sounds cool!

Since everything is strongly-typed using TypeScript, you can also enforce that every Workflow uses the same version of your various actions, and update all of your actions at the same time. [See the "using versions" example here.](examples/using%20versions/).


### Any other neat things I can do?

Good question! Since this library doesn't currently exhaustively cover everything you can do in a Workflow, the type system has been purposefully made to be easily extensible, where possible. [Take a look at the "adding of modifying fields example for how to do that.](examples/adding%20or%20modifying%20fields/)


## So cool! How do I set this up for my project?!

First, let's install it:

```shell
npm install --save-dev @wardellbagby/github-workflow-generator
```

Next, let's create our first Workflow:

```typescript
export const myWorkflow: Workflow = {
    // TODO fill this in.
}
```

Now, we need to create a simple script to import these Workflows and write them to a file.

```typescript
import { myWorkflow } from "myWorkflowFile";
import { writeWorkflow } from "@wardellbagby/github-workflow-generator";

writeWorkflow(myWorkflow);
```

Lastly, we simply run the script you just created in the root of your project folder.

```shell
node generate_workflows.js
```

This will create the `workflows` directory inside of your existing `.github` directory with the generated Workflows saved.

### That setup is...complex. Any other examples?

Yes! This project uses itself, and as apart of its setup, it uses [script created in the last step above](scripts/generate_workflows.ts) run as a pre-commit hook using Husky. If you'd like that as an example, [check this out.](.husky/pre-commit)