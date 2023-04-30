## Troubleshooting

### Resolve error `Package subpath './lib/tokenize' is not defined by "exports"`

Optionally run:

1. Install yarn
```bash
npm install --global yarn
```

2. Execute yarn
```bash
yarn
```

3. Update npm:
```bash
npm update
```

## Alchemy

## Alchemy App API

Create app in the Sepoli network and copy API URl

## Alchemy Composer

- https://composer.alchemy.com

## Publish on Github Pages

1. Install gh-pages

```bash
npm install gh-pages --save-dev
```

2. Package.json

```yaml
{
  "homepage": "https://jaravan.github.io/nft-minter",
  "name": "nft-minter",
  ...
```

```yaml
  "scripts": {
    ...
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  },
```

3. Execute command and push new branch to gh repo:

```bash
npm run deploy 
```

### Environment variables

```bash
npm install dotenv --save
```

### Use GH secrets in GH Pages

"React is purely a front-end framework. Everything accessible to React (even if you embed it through a build step) will later be visible in the front-end code and someone relatively basic to find. To really keep them secret you MUST have something server side!"

The following answer will actually expose the key in the gh-page branch on GitHub, also the keys will be accessible through the network tab in the developer console.

Original Answer
I'm also using create-react-app, and I found that this can be done by customizing your CI script with GitHub secret settings. (After the setting, you can use environment variables like this in your project.)

```js
const apiKey = process.env.REACT_APP_APIKey
const apiSecret = process.env.REACT_APP_APISecret
```

<strong>To add a secret to your repository, go to your repository's Setting > Secrets, click on Add a new secret. In the screenshot below, I added 2 env variables: REACT_APP_APIKey and REACT_APP_APISecret.</strong>

<ins>Notice:</ins> All the environment variable you want to access with create-react-app need to be prefixed with `REACT_APP`.

enter image description here

After you have your secret ready, you can take a look at this post, it's about how to add your own Action upon push.

To setup your action script, go to your repository > Actions, an click on Setup workflow your self, and paste in the script provided in the post or take a look at mine script below.

enter image description here

I use the following script to access the 2 environment variables I set on GitHub secret. (You can access the secret you set in the script by ${{ secrets.REACT_APP_APIKey }}.)

```yaml
name: CI

on:
  push:
    branches:
      - master

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    
    - name: Checkout
      uses: actions/checkout@v1

    - name: Build
      run: |
        npm install
        npm run-script build
      env:
        REACT_APP_APIKey: ${{ secrets.REACT_APP_APIKey }}
        REACT_APP_APISecret: ${{ secrets.REACT_APP_APISecret }}

    - name: Deploy
      uses: JamesIves/github-pages-deploy-action@releases/v3
      with:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        BRANCH: gh-pages
        FOLDER: build
```

After you setup the script, the action will be triggered by any push to master branch. After you push any commits, you can take a look at the deployment status at actions status.

## Interact with Smart Contract steps

Copy smart contract ABI to `contract-abi.json`

#### Create environment secret

1. On GitHub.com, navigate to the main page of the repository.
2. Under your repository name, click  Settings. If you cannot see the "Settings" tab, select the  dropdown menu, then click Settings.
3. In the left sidebar, click Environments.
4. Click on the environment that you want to add a secret to.
5. Under Environment secrets, click Add secret.
6. Type a name for your secret in the Name input box.
7. Enter the value for your secret.
8. Click Add secret.

#### Reference

- https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment
- https://stackoverflow.com/questions/66398572/cannot-access-environment-secrets-variables-passing-it-from-gh-pages-to-reactapp
- https://stackoverflow.com/questions/53648652/how-to-use-environment-variables-in-github-page
- https://dev.to/pierresaid/deploy-node-projects-to-github-pages-with-github-actions-4jco

### Links:

- https://www.c-sharpcorner.com/article/how-to-deploy-react-application-on-github-pages/
- https://www.youtube.com/watch?v=OVGoJV5tcM8&t=569s


## Alternatives to GH Pages

- [netlify](https://www.netlify.com)
- [Vercel](https://vercel.com)

## GH Action workflow notes (NOT REQUIRED)

```yaml
jobs:
  trying-to-use-environment-secrets:
    runs-on: ubuntu-latest
    environment: test-env #<---We need to specify what environment to use for environment secrets vs plain secrets
    steps:
      - name: Checkout
        uses: actions/checkout@v1

      - name: Store variables
        run: |
          echo ${{ secrets.DEV_SERVER_URL_FROM_ENVIRONMENT_SECRETS }}
          echo "DEV_SERVER_URL=${{ secrets.DEV_SERVER_URL_FROM_REPOSITORY_SECRETS }}"
```

```yaml
name: Deploy to GitHub Pages
    on:
      push:
        branches:
          - master
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - name: Checkout
          uses: actions/checkout@v1

        - name: Build
          run: |
            npm install
            npm run-script build
          env:
            REACT_APP_INSTAGRAM_ACCESS_TOKEN: ${{ secrets.REACT_APP_INSTAGRAM_ACCESS_TOKEN }}
            REACT_APP_SMTP_SECURE_TOKEN: ${{ secrets.REACT_APP_SMTP_SECURE_TOKEN }}

        - name: Deploy
          uses: JamesIves/github-pages-deploy-action@releases/v3
          with:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN_KEY }}
            BRANCH: gh-pages
            FOLDER: dist
```