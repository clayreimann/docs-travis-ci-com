---
title: npm Releasing
layout: en
permalink: /user/deployment/npm/
---

Travis CI can automatically release your npm package to [npmjs.org](https://npmjs.org/) 
or another npm-like registry after a successful build. By default Travis CI will
publish to npmjs.org, however if you have a `publishConfig.registry` key in your 
`package.json` then Travis CI will push to that registry instead.

For npm version 2+ your api_key can be found in your `~/.npmrc` file. In your 
`.npmrc` you should see a line similar to `//registry.npmjs.org/:_authToken=YOUR_API_KEY`. 
A minimal configuration for publishing to npmjs.org with npm version 2+ looks like:

    deploy:
      provider: npm
      api_key: "YOUR_API_KEY"

For npm version ~1 your `~/.npmrc` file will look more like:

    _auth=YOUR_API_KEY
    email=YOUR_EMAIL_ADDRESS
    
And you can deploy with the npm provider by adding:

    deploy:
      provider: npm
      email: "YOUR_EMAIL_ADDRESS"
      api_key: "YOUR_API_KEY"

It is recommended to encrypt your api_key. Assuming you have the Travis CI command 
line client installed, you can do it like this:

    $ travis encrypt YOUR_API_KEY --add deploy.api_key

You can also have the `travis` tool set up everything for you:

    $ travis setup npm

Keep in mind that the above command has to run in your project directory, so 
it can modify the `.travis.yml` for you.

### What to release

Most likely, you would only want to deploy to npm when a new version of your
package is cut. To do this, you can tell Travis CI to only deploy on tagged
commits, like so:

    deploy:
      ...
      on:
        tags: true

If you tag a commit locally, remember to run `git push --tags` to ensure that 
your tags are uploaded to Github.

You can explicitly specify the branch to release from with the **on** option:

    deploy:
      ...
      on:
        branch: production

Alternatively, you can also configure Travis CI to release from all branches:

    deploy:
      ...
      on:
        all_branches: true

Builds triggered from Pull Requests will never trigger a release.

### Releasing build artifacts

After your tests ran and before the release, Travis CI will clean up any additional files and changes you made.

Maybe that is not what you want, as you might generate some artifacts that are supposed to be released, too. There is now an option to skip the clean up:

    deploy:
      ...
      skip_cleanup: true

### Conditional releases

You can deploy only when certain conditions are met.
See [Conditional Releases with `on:`](/user/deployment#Conditional-Releases-with-on%3A).

### Running commands before and after release

Sometimes you want to run commands before or after releasing a package. You can use the `before_deploy` and `after_deploy` stages for this. These will only be triggered if Travis CI is actually pushing a release.
