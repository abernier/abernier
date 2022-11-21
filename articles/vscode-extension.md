VScode extension
===

https://code.visualstudio.com/api/get-started/extension-anatomy#:~:text=Your%20First%20Extension

```sh
$ npm i generator-code
$ npx -y yo code

     _-----_     ╭──────────────────────────╮
    |       |    │   Welcome to the Visual  │
    |--(o)--|    │   Studio Code Extension  │
   `---------´   │        generator!        │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

? What type of extension do you want to create? New Extension (TypeScript)
? What's the name of your extension? test01
? What's the identifier of your extension? test01
? What's the description of your extension?
? Initialize a git repository? Yes
? Bundle the source code with webpack? No
? Which package manager to use? npm
```

<details><summary>outputs</summary>
<div>
  
```sh
Writing in /Users/abernier/code/vscodeschool/test01...
   create test01/.vscode/extensions.json
   create test01/.vscode/launch.json
   create test01/.vscode/settings.json
   create test01/.vscode/tasks.json
   create test01/package.json
   create test01/tsconfig.json
   create test01/.vscodeignore
   create test01/vsc-extension-quickstart.md
   create test01/.gitignore
   create test01/README.md
   create test01/CHANGELOG.md
   create test01/src/extension.ts
   create test01/src/test/runTest.ts
   create test01/src/test/suite/extension.test.ts
   create test01/src/test/suite/index.ts
   create test01/.eslintrc.json

Changes to package.json were detected.

Running npm install for you to install the required dependencies.

added 212 packages, and audited 213 packages in 14s

46 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Your extension test01 has been created!

To start editing with Visual Studio Code, use the following commands:

     code test01

Open vsc-extension-quickstart.md inside the new extension for further instructions
on how to modify, test and publish your extension.

For more information, also visit http://code.visualstudio.com and follow us @code.
```
  
</div>
</details>

In the opened VScode window, press <kbd>F5</kbd>

<kbd>cmd+shift+p</kbd> `Hello World` <kbd>Enter</kbd>
