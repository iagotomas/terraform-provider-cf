## Governance

The backlog is kept as github issues annotated with labels and milestones, and progress is tracked in the [backlog project](https://github.com/mevansam/terraform-provider-cloudfoundry/projects/1).

Please send PR against the `dev` branches.

You may reach core contributors in the [cloudfoundry slack, within the terraform channel](https://cloudfoundry.slack.com/messages/C7JRBR8CV/) (get an account from http://slack.cloudfoundry.org/). Please prefer slack channel for support requests, and github issues for qualified bugs and enhancements requests.

## Compliance

Linters are run as part of the travis build. Detected flaws will prevent the merge of the PR.

Contributors can run the same checks locally by following the procedure:

1. install dependencies

  ```bash
  # install gometalinter tool
  go get gopkg.in/alecthomas/gometalinter.v2
  # install gometalinter internal dependencies
  gometalinter.v2 --install
  ```

2. Run the linters

  ```bash
  make check
  ```

Fine tuning of the linters configuration can be done in the [.gometalinter.json](.gometalinter.json) file according to the tool [specification](https://github.com/alecthomas/gometalinter#configuration-file)

## Contributing new features

* Please search for an existing issue describing the enhancement request, or open a new issue if none matches
* For new resources/datasource, propose a resource modeling in the form of draft [documentation update](https://github.com/mevansam/terraform-provider-cf/wiki). This will help focus discussions on naming, resource granularity, syntaxic sugar and hopefully reduce PR rework.
* Once specs are agreed, propose an implementation. [Extending Terraform](https://www.terraform.io/docs/extend/index.html)  documentation is a good place to check up-to-date hashicorp recommendations and best practices, and to suggest refreshes/improvements to the `terraform-provider-cf` project.
* Submit a PR against the `dev` branch
   * Make sure to include/refine acceptance test to cover the feature. This will make sure future contribution will not introduce regressions that will break your feature in the future. Try to favor fast tests (e.g. favor a static buildpack-based app which stages and starts fast rather than a java-based buildpack). [cf-acceptance-tests](https://github.com/cloudfoundry/cf-acceptance-tests/) might be a good place for inspiration if you're searching for sample assets for tests.
   * Make sure to update the [CHANGELOG](/CHANGELOG.md) to describe your feature in the release notes.
* Once the PR is green (i.e running compilation):
   * a member of the core team will review it for security purposes and push a branch to the upstream `mevansam/terraform-provider-cf`  repo so it trigger acceptance tests. 
      * PRs from forks don't automatically run trigger acceptance tests to avoid untrusted changes having access to acceptance environment secrets. 
      * Regular contributors get soon granted push access to the repository so that they can directly
* When the PR gets at least one product review and two technical reviews, the PR is merged in the `dev` branch



## Running local acceptance tests

Local tests relies on pcf-dev, which is a local version of cloudfoundry components running is a VirtualBox machine.

1. Install dependencies

  - cf cli: follow [official procedure](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

  - pcfdev plugin:
    - download plugin: [on pivotal website](https://network.pivotal.io/products/pcfdev). You must register to access downloads.
    - install plugin: ```cf install-plugin pcfdev-v0.30.0+PCF1.11.0-linux```
    - run plugin: ```cf dev start```

  The last command will actually download a virtual machine template (this will take some time) then start the machine. Starting process is also quite long.


2. Run tests

  - export environment variables
    ```
    export CF_API_URL=https://api.local.pcfdev.io
    export CF_USER=admin
    export CF_PASSWORD=admin
    export CF_UAA_CLIENT_ID=admin
    export CF_UAA_CLIENT_SECRET=admin-client-secret
    export CF_CA_CERT=""
    export CF_SKIP_SSL_VALIDATION=true
    export GITHUB_USER=valid-github-user
    export GITHUB_TOKEN=valid-github-personal-access-token
    ```

  - (optional) activate logs
    ```
    export CF_DEBUG=true
    export CF_TRACE=debug.log
    ```

  - run tests
    ```
    cd cloudfoundry
    TF_ACC=1 go test -v -timeout 120m .
    ```


## Creating a release

* Open up an issue "cutting release 0.9.9" to gather contributors concensus on when to cut the release
* Update [CHANGELOG](/CHANGELOG.md) in the `dev` branch and push.
   * Review commits since last release, to spot contributions that could have been missed in the  [CHANGELOG](/CHANGELOG.md). Add mention for the missing contribs 
   * Rename `0.9.9 (unreleased)` section to the release version `0.9.9 (today)`, and insert a top empty `0.10.0 (unreleased)`
* On your clone, checkout the `dev` branch, and execute `scripts/create-release.sh 0.9.9`
   * travis build kicks off for this tag, and tries to publish the artifacts github, check it on [travis the branch list](https://travis-ci.org/mevansam/terraform-provider-cloudfoundry/branches)
* edit the release notes in the [github release page](https://github.com/mevansam/terraform-provider-cloudfoundry/releases) reusing or linking the [CHANGELOG](/CHANGELOG.md)
