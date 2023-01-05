---
title: '${TM_FILENAME_BASE/[-_]|(\w+$)|(\w+)/${1:/capitalize}${2:/capitalize}${2:+ }/g}'
tags: ['tag1']
date: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}
---
# ${TM_FILENAME_BASE/[-_]|(\w+$)|(\w+)/${1:/capitalize}${2:/capitalize}${2:+ }/g} {: .primaryHeading }
<small>Last updated: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}</small>
{: .primaryDate }

---

Foam includes note templates! 
This allows you to easily create notes that have similar structure without having to use copy/paste :)

Templates support the [VS Code's Snippet Syntax](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_snippet-syntax), which means you can:
- add variables to the newly created note
- add tabstop to automatically navigate to the key parts of the note, just like a form

---

Try out the above example by running the `Foam: Create New Note From Template` command and selecting the `default` template. Notice what happens when your new note is created!

To remove this template, simply delete the `.foam/templates/default.md` file.

Enjoy!