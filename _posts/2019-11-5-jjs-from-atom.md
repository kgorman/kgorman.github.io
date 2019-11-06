---
title: Atom editor, Javascript and Nashorn
author: kgorman
comments: true
layout: post
categories:
- nashorn
- javascript
- atom
---

When writing Javascript using Oracle [Nashorn](https://www.oracle.com/technical-resources/articles/java/jf14-nashorn.html) using the CLI program `jjs` is commonplace. If you are using Atom as your editor, you will need a few mods to be able to invoke jjs and run your javascript from inside the editor. Here is how.

<!--more-->

Just a bit of quick background - when creating [Javascript User Functions](https://docs.eventador.io/sqlstreambuilder/ssb_user_functions/) for [Eventador SQLStreamBuilder](https://www.eventador.io) - the best practice is to use jjs to develop the functions before importing them into Eventador. If you are using Atom as your editor, you can run these functions from inside the editor.

- Install the [script package](https://atom.io/packages/script) (thx [rgbkrk](https://github.com/rgbkrk))
- Edit the grammar file for javascript to use jjs.
Open the file: `$HOME/.atom/packages/script/lib/grammars/javascript.js` and replace the `exports.Javascript` section like this:


```javascript
exports.JavaScript = {
  'Selection Based': {
    command: '/usr/bin/jjs',
    args: (context) => {
      const code = context.getCode();
      const tmpFile = GrammarUtils.createTempFileWithCode(code, '.js');
      return [tmpFile];
    },
  },
  'File Based': {
      command: '/usr/bin/jjs',
      args: ({ filepath }) => [filepath]},
};
```

- Save the file and restart Atom.
- Open a javascript file, or type some javascript into the editor and select it. If there is a selection it will run the selection, if not, it will run the entire file. Hit `Cmd-I` and the results will be displayed in the editor console window.

![nashorn](/images/nashorn_atom.png)
