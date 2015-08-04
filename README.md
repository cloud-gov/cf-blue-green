# Cloud Foundry blue-green deployment

Allows zero-downtime deployments of applications within Cloud Foundry.

## Usage

Requires [the `cf` CLI](https://docs.cloudfoundry.org/devguide/installcf/install-go-cli.html) v6.12.2+.

1. Run `npm install -g cf-blue-green`.
    * Requires Node.js, just for distribution via NPM. If you don't want to install it, simply:
        1. Download [the script](bin/cf-blue-green).
        1. Run `chmod a+x cf-blue-green`.
        1. Move the file somewhere in your PATH.
1. Run `cf-blue-green <appname>` (instead of `cf-push`) from your application directory to deploy.

This creates a copy of your already-running application, and safely switches traffic over to it. No additional setup needed!

It's recommended that you try this script on a non-production application environment first, just to ensure that everything is switched over properly.

## Resources

More information about blue-green deployment, all of which this script drew from.

* http://www.cloudfoundry.rocks/blue-green-deployment-with-cloudfoundry/
* http://martinfowler.com/bliki/BlueGreenDeployment.html
* http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/blue-green.html
* https://github.com/dlapiduz/step-cloud-foundry-deploy/blob/master/run.sh
