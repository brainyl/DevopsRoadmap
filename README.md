# frontend

## Build Setup

```bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn dev

# build for production and launch server
$ yarn build
$ yarn start

# generate static project
$ yarn generate
```

For detailed explanation on how things work, check out the [documentation](https://nuxtjs.org).

## Special Directories

You can create the following extra directories, some of which have special behaviors. Only `pages` is required; you can delete them if you don't want to use their functionality.

### `assets`

The assets directory contains your uncompiled assets such as Stylus or Sass files, images, or fonts.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/assets).

### `components`

The components directory contains your Vue.js components. Components make up the different parts of your page and can be reused and imported into your pages, layouts and even other components.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/components).

### `layouts`

Layouts are a great help when you want to change the look and feel of your Nuxt app, whether you want to include a sidebar or have distinct layouts for mobile and desktop.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/layouts).

### `pages`

This directory contains your application views and routes. Nuxt will read all the `*.vue` files inside this directory and setup Vue Router automatically.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/get-started/routing).

### `plugins`

The plugins directory contains JavaScript plugins that you want to run before instantiating the root Vue.js Application. This is the place to add Vue plugins and to inject functions or constants. Every time you need to use `Vue.use()`, you should create a file in `plugins/` and add its path to plugins in `nuxt.config.js`.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/plugins).

### `static`

This directory contains your static files. Each file inside this directory is mapped to `/`.

Example: `/static/robots.txt` is mapped as `/robots.txt`.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/static).

### `store`

This directory contains your Vuex store files. Creating a file in this directory automatically activates Vuex.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/store).

## Infrastructure

You can setup the infrastructure by signing up for a [free tier](https://aws.amazon.com/free) account from AWS. Once you have the account, create a new user and grant it administrative privileges. Also, you will need to generate `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY ` for the new user.

On your local machine make sure to install `aws-cli` by following this [guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html). Finally, add the following to your `~/.aws/credentials` file:

```
[default]
region = us-east-1
aws_access_key_id = AKIAYELFE2G54NIWPOWO
aws_secret_access_key = iZPKPdAQJWZ9GeSwxqc43Mg4G5ZxtkkSZect5tAl
```

You can now proceed to execute the following scripts to setup the infrastructure on AWS.

### Create the Artifact Management Stack

This stack is used to manage the roles and s3 buckets that would be used to manage our interaction with codepipeline, codebuild and cloudformation. This helps us to enforce the principle of least privilege.

```
aws cloudformation create-stack --template-body file://infrastructure/artifact-management.yaml --stack-name ArtifactManagementStack --capabilities CAPABILITY_NAMED_IAM
```

Should you run into any issues and want to delete the stack, you can run the following from the CLI

```
aws cloudformation delete-stack --stack-name ArtifactManagementStack
```

### Create the Pipeline Stack

This stack is responsible for creating the pipeline for a given branch. This pipeline is triggered whenever changes are pushed to the branch that the pipeline was created for. Depending on the branch, `development` or `master`, we can set the `--stack-name` to either `DevopsRoadmapDevelopmentPipeline` or `DevopsRoadmapPipeline`.

Make sure to switch to the right `branch` before creating the pipeline stack. Once you are in the branch you want to create a pipeline for, you can execute the following command to create the pipeline:

```
aws cloudformation create-stack --template-body file://infrastructure/service-pipeline.yaml --stack-name DevopsRoadmapPipeline --role-arn arn:aws:iam::559094747579:role/CloudFormationRole --capabilities CAPABILITY_IAM
```

Should you run into any issues and want to delete the stack, execute the following command:

```
aws cloudformation delete-stack --stack-name DevopsRoadmapPipeline
```

### Access the Deployed Application

Once the pipeline has been executed successfully, you can check the output section of the CloudFormation Stack (`DevopsRoadmap-master-stack`) that was created to get the url of the CDN distribution. Clicking this URL should take you to the application
