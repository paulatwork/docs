## Overview

You can help us improve Nitric by creating **bug reports**. When creating a bug report, it's important that you include as much information as possible about your issue. That way, it's easier to reproduce and fix the issue.

> You can also create **feature requests** or ask a **question** via the issue templates on GitHub.

## Where do I open the bug report?

The Nitric framework has many different repositories in the <a href="https://github.com/nitrictech" target="_blank">nitrictech</a> organization on GitHub. If you have an issue with any component, you can open a new issue in the respective repo.

If you're unsure where to open the GitHub issue, you can use our main <a href="https://github.com/nitrictech/nitric" target="_blank">nitrictech/nitric</a> repo. The Nitric team frequently monitors and triages new issues within this main repo and will move the issue to another repo if required.

## Share reproduction of bug

To best help us understand and tackle the issue effectively, provide a **standalone GitHub repository** in your report that can reproduce the issue.

For information on how to create minimal, reproducible examples you can view this informative <a href="https://stackoverflow.com/help/minimal-reproducible-example" target="_blank">Stackoverflow guide</a>.

## Best practices for writing the bug report

If you don't have time to create a standalone repository, please include as much information as you can can about the issue, use the <a href="https://github.com/nitrictech/nitric/issues/new?assignees=&labels=&template=bug_report.md&title=%27Create%20bug%20report%27" target="_blank">Bug report template</a> from our main repo to help guide you.

### Include .nitric log files

Log files can be found in your stack directory under the `.nitric` folder.
Including Log files will provide a ton of useful information logged during local run and cloud deployments.

### Include output of info command

The output of the info command provides us with meta data such as OS, Chipset information, GoVersion, etc. which can be used to reproduce the issue raised.

```bash
nitric info -o yaml`
```

### Include bug description and reproduction

When describing the bug, it's help to produce the following information:

- A clear and concise description of what the bug is
- Steps to reproduce the bug
- A clear and concise description of what you expected to happen
- Screenshots (if applicable)

### Include environment and setup information

Any information about your environment and setup will be important to help reproduce the bug. Please include the following:

- Which version of the **Nitric CLI** you are using (run `nitric version` to see your version)
- Your **Operating System**
- Any nitric dependencies and their version such as SDKs or Middleware
- Which cloud providers you are deploying to

### Example Report

**Describe the bug:**

Returning a JSON response in my list handler doesn't work, it returns nothing and times out.

**Steps to reproduce:**

I created a simple function handler to set the response body to a JSON string.

```typescript
import { api } from '@nitric/sdk';

api('public').get('/customers', (ctx) => {
  // construct response for the GET: /customers request
  const responseBody = {};
  ctx.res.json(responseBody);
});
```

Then I run `nitric run -s ./ "functions/*.ts"` to run nitric locally.

**What I expected:**

I expected to see a 200 status with the JSON response printed to my terminal in a readable format or at least an error explaining what I did wrong.

**What I received:**

I then use curl to test the handler's endpoint.

```bash
curl http://localhost:9001/apis/public/customers
```

I did not receive a response from the server.

**Environment and setup information:**

- Nitric CLI version: `Nitric Helper CLI v2.0`
- Dependencies used: `@nitric/sdk@1.0.0`
- Cloud providers: AWS and Google Cloud
