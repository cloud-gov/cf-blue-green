***Note: if your application's manifest is "complete", use [Autopilot](https://github.com/concourse/autopilot) instead. More info [below](#manifests), in [the Autopilot README](https://github.com/concourse/autopilot#warning), and in [this Issue](https://github.com/concourse/autopilot/issues/11).***

# Cloud Foundry blue-green deployment

Allows zero-downtime deployments of applications within Cloud Foundry, with no additional setup needed.

## Usage

1. [Install the `cf` CLI](https://github.com/cloudfoundry/cli/releases) **v6.12.4+**.
1. Run `npm install -g cf-blue-green`.
    * See [Notes](#manual-installation) below for non-Node installation.
1. Run `cf-blue-green <appname>` (instead of `cf-push`) from your application directory to deploy.

This creates a copy of your already-running application, and safely switches traffic over to it. It's recommended that you try this script on a non-production application environment first, just to ensure that everything is switched over properly.

## Notes

### Manual installation

The script is distributed via NPM, but doesn't actually require Node.js beyond that. If you don't want to install Node, simply:

1. Download [the script](bin/cf-blue-green).
1. Run `chmod a+x cf-blue-green`.
1. Move the file somewhere in your PATH.

### Using with Travis

Travis supports [continuous deployment](http://docs.travis-ci.com/user/deployment/), which will automatically deploy your application after its tests pass on a specified branch. To use `cf-blue-green` with Travis, you need to use a [script provider](http://docs.travis-ci.com/user/deployment/script/) instead of the default Cloud Foundry provider. Your Cloud Foundry settings are read from environment variables.

Set up continuous deployment with the following settings in your `.travis.yml` file:

```yml
sudo: true
env:
  global:
  - CF_APP=[app name]
  - CF_API=[API endpoint]
  - CF_USERNAME=[user]
  - CF_ORGANIZATION=[organization]
  - CF_SPACE=[space]
  - secure: [CF_PASSWORD=[encrypted with Travis](http://docs.travis-ci.com/user/environment-variables/#Encrypted-Variables)]
before_deploy: npm install -g cf-blue-green
deploy:
  provider: script
  script: cf-blue-green-travis
  on:
    branch: [git branch you want to deploy]
```

### Manifests

`cf-blue-green` creates a temporary manifest from your live application, meaning that it ignores the `manifest.yml` in your directory, if you have one. To deploy any changes to your manifest, use `cf push` directly.

## Multiple domains

The script fails on apps with multiple domains, because the domains in the manifest are in the form of a list:

```yml
domain:
  - 18f.gov
  - digitalgov.gov
```

To work around this, use the env var `B_DOMAIN` for the domain you'd like the B instance to use.


## Resources

More information about blue-green deployment, all of which this script drew from.

* http://www.cloudfoundry.rocks/blue-green-deployment-with-cloudfoundry/
* http://martinfowler.com/bliki/BlueGreenDeployment.html
* http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/blue-green.html
* https://github.com/dlapiduz/step-cloud-foundry-deploy/blob/master/run.sh

## See also

* [Autopilot](https://github.com/contraband/autopilot): Autopilot is a CloudFoundry Go plugin that provides a subcommand, `zero-downtime-push` for hands-off, zero-downtime application deploys.
* [BlueGreenDeploy[(https://github.com/bluemixgaragelondon/cf-blue-green-deploy): cf-blue-green-deploy is a plugin, written in Go, for the CF command line tool that automates a few steps involved in zero-downtime deploys.
