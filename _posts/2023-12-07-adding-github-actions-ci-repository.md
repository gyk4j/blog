---
layout: post
title:  "Adding GitHub Actions CI to Repository"
---
I was trying out GitHub Actions on a 
[Java Maven project](https://github.com/gyk4j/wreck) today just to see how easy 
or hard it is to add continuous integration, since it was encouraging me to do 
so, and looked very welcoming and easy. :)

My experience is with a Java Maven project. Your milege may vary with other
build systems for other languages, but I expect the experience to be more or 
less the same.

At the **Get started with GitHub Actions** page, I chose **Java with Maven**.
Had a quick preview of the `maven.yml` and left everything unchanged. Using the
default workflow template and ensuring it builds successfully first before 
tweaking seems to be a safer and saner choice. Save and committed changes.

Fingers crossed. BOOM! Build failed -- on the very first try. Not very 
encouraging.

```
Error: HTTP Status 403 for request POST https://api.github.com/repos/<USER>/<REPO>/dependency-graph/snapshots
Error: Response body:
{
  "message": "Resource not accessible by integration",
  "documentation_url": "https://docs.github.com/rest/dependency-graph/dependency-submission#create-a-snapshot-of-dependencies-for-a-repository"
}
Error: Resource not accessible by integration
Error: HttpError: Resource not accessible by integration
    at /home/runner/work/_actions/advanced-security/maven-dependency-submission-action/571e99aab1055c2e71a1e2309b9691de18d6b7d6/webpack:/maven-dependency-tree-action/node_modules/@github/dependency-submission-toolkit/dist/index.js:5317:1
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
```

What's wrong? Googling for "Resource not accessible by integration" tells me it
had something to do with permissions, or security settings. Indeed, 
[following the documentation link in the build log](https://docs.github.com/rest/dependency-graph/dependency-submission#create-a-snapshot-of-dependencies-for-a-repository)
led me:

> Create a new snapshot of a repository's dependencies.  
> You must authenticate using an access `token` with the repo scope to use this 
> endpoint for a repository that the requesting user has access to.

Googling around did not tell me or give me an example of how to add a Personal 
Access Token or `GITHUB_TOKEN` generated for each run of the Actions to the 
`maven.yml` file. Or whether there is a setting somewhere on GitHub to change.

A [discussion on StackOverflow](https://stackoverflow.com/questions/70435286/resource-not-accessible-by-integration-on-github-post-repos-owner-repo-ac) 
tells me there were some unfortunate souls who had the same problem. The top
reply suggests 3 possible ways:

1. Use PAT (not sure how to add it into a Workflow though)
2. Override the permissions by adding `permissions: write-all` under the job
3. Set **Repository > Settings > Actions > Actions Permissions > Workflow permissions** to **Read and write permissions**.

I tried out both option 2 and 3 and they both worked.

In conclusion, my experience is a mixed bag. It isn't as smooth-sailing as my 
initial assumption as simply a point-and-click task, with little to no 
customization for a straightforward project that is already building 
successfully locally.

However, the default Action template and security setting chosen by GitHub are 
puzzling, as the workflow is set up to fail by default out-of-the-box. Perhaps 
GitHub should consider tweaking the default setting.

As a side note, when it failed, the Actions job page still gleefully offered to
**Create status badge** for me to show off that my builds are failing. Not sure 
if it is GitHub's way of sarcasm or lack of EQ.
