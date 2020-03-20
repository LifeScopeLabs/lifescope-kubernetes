# LifeScope on Kubernetes

This repository contains all of the scripts for setting up the various components of the LifeScope platform on Kubernetes.
It's written using Kustomization, which is built in kubectl as of version 1.14.

The base K8s config is in <repo_name>/base/<repo_name>.yaml. Any staging- or production-specific alterations are
present in <repo_name>/overlays/<staging_or_production>/<staging_or_production>.yaml.

## Adding dev or production config files

The Docker images that are built and available on Docker Hub do not contain any instance-specific sensitive data
such as API keys or passwords. In order to get these files into the Kubernetes deployment, you'll have to create
a configuration file with the proper name and place it in a specific location so that Kustomize can generate the appropriate Secret.
Note that each repo has its own separate config and therefore needs a different dev or production config from any
other repos' config of the same name - do not copy lifescope-api's dev.json and assume it's valid for lifescope-app.

The dev configurations must be placed in <repo_name>/base; staging environments will use this as well. 
All of the repos except lifescope-embed expect a file named 'dev.json', while lifescope-embed expects 'config.dev.js'.

Production config files must be placed in <repo_name>/overlays/production.
lifescope-embed's config is called 'config.production.js',  lifescope-xr's config is called 'prod.json', and all other
repos' configs are called 'production.json'.

Per the instructions in most repos' '02-local-build.md' isntructions, whatever the dev and production config files
contain will override what's in the default config, so you only need to specify fields that are different from the
base config. 

## Configure Kubernetes with Kustomization files

Kustomize should be run by pointing kubectl to the directory of the environment you wish to run; it will automatically
search for the kustomization.yaml file in there. In general this looks like the following:

```kubectl apply -k <repo_name>/overlays/<environment directory>```

To give an example, if you were spinning up an nginx configuration in a staging environment you would run the following:

```kubectl apply -k lifescope-nginx/overlays/staging```

Running a production instance of lifescope-api would look like this:

```kubectl apply -k lifescope-api/overlays/production```

To run something locally on minikube, you would point kubectl to the base directory like this:

```kubectl apply -k lifescope-app/base```