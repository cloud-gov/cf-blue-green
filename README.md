# Cloud Foundry blue-green deployment

Allows zero-downtime deployments of applications within Cloud Foundry, with no additional setup needed.

## Usage

1. [Install the `cf` CLI](https://github.com/cloudfoundry/cli/releases), v6.12.0 or v6.12.1.
    * See [Notes](#cf-cli) below for more info about the version restriction.
1. Run `npm install -g cf-blue-green`.
    * See [Notes](#manual-installation) below for non-Node installation.
1. Run `cf-blue-green <appname>` (instead of `cf-push`) from your application directory to deploy.

This creates a copy of your already-running application, and safely switches traffic over to it. It's recommended that you try this script on a non-production application environment first, just to ensure that everything is switched over properly.

## Notes

### CF CLI

The reasons for the tight version restriction on the CF CLI is:

* < 6.12.0 [doesn't respect the `buildpack` parameter](https://www.pivotaltracker.com/n/projects/892938/stories/96041780).
* 6.12.2+ has [an unresolved bug](https://www.pivotaltracker.com/n/projects/892938/stories/100594158) (as of this writing) that breaks `cf-blue-green`

To downgrade to 6.12.1 on Mac, if you use [Homebrew](http://brew.sh/):

```bash
brew uninstall cloudfoundry-cli
brew install https://raw.githubusercontent.com/pivotal/homebrew-tap/b39786b30125187bfa37a71eebef88222aa2c435/cloudfoundry-cli.rb
```

### Manual installation

The script is distributed via NPM, but doesn't actually require Node.js beyond that. If you don't want to install Node, simply:

1. Download [the script](bin/cf-blue-green).
1. Run `chmod a+x cf-blue-green`.
1. Move the file somewhere in your PATH.

### Using with Travis

Travis supports [continuous deployment](http://docs.travis-ci.com/user/deployment/), which will automatically deploy your application after its tests pass on a specified branch. To use `cf-blue-green` with Travis, you need to use a [script provider](http://docs.travis-ci.com/user/deployment/script/) instead of the default Cloud Foundry provider. Your Cloud Foundry settings are read from environment variables.

Set up continuous deployment with the following settings in your `.travis.yml` file:

```yml
before_install: npm install -g cf-blue-green
env:
  global:
  - CF_APP=[app name]
  - CF_API=[API endpoint]
  - CF_USERNAME=[user]
  - CF_ORGANIZATION=[organization]
  - CF_SPACE=[space]
  - secure: [CF_PASSWORD=[encrypted with Travis](http://docs.travis-ci.com/user/environment-variables/#Encrypted-Variables)]
deploy:
  provider: script
  script: cf-blue-green-travis
  on:
    branch: [git branch you want to deploy]
```

### Manifests

`cf-blue-green` creates a temporary manifest from your live application, meaning that it ignores the `manifest.yml` in your directory, if you have one. To deploy any changes to your manifest, use `cf push` directly.

## Resources

More information about blue-green deployment, all of which this script drew from.

* http://www.cloudfoundry.rocks/blue-green-deployment-with-cloudfoundry/
* http://martinfowler.com/bliki/BlueGreenDeployment.html
* http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/blue-green.html
* https://github.com/dlapiduz/step-cloud-foundry-deploy/blob/master/run.sh
