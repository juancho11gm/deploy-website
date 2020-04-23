# Deploy your first website

Deploy your first website with Firebase. We are going to use GitHub, Firebase, Travis CI, Nodejs and GoDaddy like tech tools.

## Steps to deploy your first website using Firebase

1. Create the repository. Add the collaborators.
2. Clone the repo. Create a folder called `src`. Inside that floder create a `index.html` file and a `assets` folder. Within `assets` folder, create two folders called `js` and `css`. There you are going to save the js files and css files.
3. Create a new project in the [firebase console](https://console.firebase.google.com/u/0/).
4. Set up Firebase Hosting: Go to the `hosting` tab. Follow the steps to set up the hosting.
4.1 Run (using cmd):
    * `npm install -g firebase-tools`.
    * `firebase login`. If the command is not being executed run `firebase login --interactive`.
    * `firebase init` (Run this command from your app’s root directory).
    * Select `hosting` with spacebar.
    * Select your existing project.
    * Deploy with `firebase deploy`.
5. Remove the content inside `index.html` in the `public`. Create a basic layout and deploy again.

## Steps to deploy automatic builds using Travis CI

### NodeJs

1. Install [NodeJs](https://nodejs.org/en/download/).
2. Run `npm init -y` inside your app folder.
3. Create a `prepare.js` file in the root folder.
4. Install `ncp` and `rimraf` dependencies (search first the utility. npm website or read [this article](https://shapeshed.com/working-with-filesystems-in-nodejs/)).
4.1 Run `npm i ncp` and `npm i rimraf`.
5. Copy this code into your `prepare.js`.

```javascript
const rimraf = require('rimraf'); //remove files
const ncp = require('ncp').ncp; //copy files
const fs = require('fs');
const public_dir = './public';

const file_list = [
    { fileSource: './src/index.html', fileDest: './public/index.html' },
];

//removes public folder
rimraf('./public/', function (rimraf_error) {
    if (rimraf_error) { throw rimraf_error; }
    // done

    //if public folder doesn't exist
    if (!fs.existsSync(public_dir)) {
        //creates public folder
        fs.mkdirSync(public_dir);
    }

    file_list.forEach((fileElement, index, array) => {
        const { fileSource, fileDest } = fileElement;
         copy_files(fileSource, fileDest);
     });

})

function copy_files(sourcePath, destPath){
    ncp(sourcePath, destPath, function (ncp_error) {
        if (ncp_error) { throw ncp_error; }
    });
}
```
6. Run `node prepare.js` to see how it works.
7. Configure your `package.json`.
7.1 Add into the scripts attribute: `"prepare-public": "node prepare.js"`.
7.2 Try it. Run `npm run prepare-public`.

## Travis CI

1. Sign up to [Travis](https://travis-ci.org/).
2. Select your repo and authorize Travis CI

![image](https://user-images.githubusercontent.com/36536646/80135198-818e5000-8565-11ea-83d5-da1362d5ad14.png)

3. Generate the firebase token running `firebase login:ci`.
4. Copy the token and add an enviroment variable in Travis

![image](https://user-images.githubusercontent.com/36536646/80138229-1abf6580-856a-11ea-8de1-11c52d6b2ed9.png)

4. Create a `.travis.yml` file in the root folder.
5. Paste the following code:

```
language: node_js
node_js:
  - "8"
script:
 - echo "Deploy!!"
install:
  - npm install -g firebase-tools
  - npm install
  - npm run prepare-public
after_success:
  - firebase deploy --project your_project_name --token $FIREBASE_TOKEN
```
* Make sure to create the enviroment variable in TravisCI and to change `your_project_name` varialble in the command.
6. ***Push*** your changes.
`
git add .
git commit -m "first deploy with Travis"
git push -u origin master
`
## Steps to configure your domain using GoDaddy

