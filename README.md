gerrit-intellij-plugin
======================

Introduction
-----------

Unofficial [IntelliJ Platform] plugin for [Gerrit Code Review Tool]. It supports any product based on the IntelliJ platform:
* IntelliJ IDEA
* IntelliJ IDEA CE
* RubyMine
* WebStorm
* PhpStorm
* PyCharm
* PyCharm CE
* AppCode
* Android Studio
* 0xDBE
* CLion

Only Gerrit 2.6 or newer is supported (missing / incomplete REST API in older versions).

[IntelliJ Platform]: http://www.jetbrains.com/idea/
[Gerrit Code Review Tool]: http://code.google.com/p/gerrit/

You can install this plugin from the [IntelliJ Plugin Manager].
If you install this plugin directly in your IDE's plugin manager, you will get notified when a new release is available.
[IntelliJ Plugin Manager]: http://plugins.jetbrains.com/plugin/7272

Your Support
------------
If you like this plugin, you can support it:
* Spread it: Tell your friends who are using IntelliJ and Gerrit about this plugin (or even encourage them to use these fantastic products!)
* Vote for it: Write your review and vote for it at the [IntelliJ plugin repository].
* Star it: [Star it at GitHub]. GitHub account required.
* Improve it: Report bugs or feature requests. Or even fix / implement them by yourself - everything is open source!
* Donate: You can find donation-possibilities at the bottom of this file.
[IntelliJ plugin repository]: http://plugins.jetbrains.com/plugin/7272
[Star it at GitHub]: https://github.com/uwolfer/gerrit-intellij-plugin

Troubleshooting
---------------
### List of changes is empty
By default, you will only see changes to Git repositories that are configured in the current project of your IntelliJ IDE.
* Make sure that Git repositories are configured in the 'Version Control' settings.
* Make sure that the Git repository remote url (at least one of them) is on the same host as configured in Gerrit plugin settings. Or:
* Add a remote whose name equals the Gerrit project name with Gerrit as remote url.

### Error-message when clicking a change: "VcsException: fatal: bad object"
In Gerrit 2.8, fetch information was pulled out of default functionality into a plugin.
You need to install the plugin <code>download-commands</code>. When you run the Gerrit update procedure, it asks you to install
this plugin (but it isn't selected by default). Just run the update script again if you have not installed it yet.

When installing Gerrit 2.8 (or newer) from scratch (rather than using the update script) the following command will install the
<code>download-commands</code> plugin (for a new installation or an existing Gerrit instance):

    $ java -jar gerrit.war init -d {gerrit-instance} --install-plugin=download-commands


### Error-message when loading changes: "SSLException: Received fatal alert: bad_record_mac"
There are two workarounds for this issue:
* allow TLSv1 (instead of SSLv3 only) connections in your reverse-proxy in front of Gerrit. SSLv3 is considered insecure, therefore TLS should by the default in any case.
* use a recent Java setup (> 1.6)

### Checking out from VCS with Gerrit plugin does not work
Checking out directly with the Gerrit plugin does not work for some authentication methods. If you get an authentication
error or checking out does not properly finish, you can try to:
* use SSH clone URL in checkout dialog (you can find the SSH URL in the Gerrit Web UI project settings)
* or: check out with the default Git plugin and set up the Gerrit plugin manually afterwards

You can find background information about this issue in a [Gerrit mailing list topic].
[Gerrit mailing list topic]: https://groups.google.com/forum/#!topic/repo-discuss/UnQd3HsL820

### Loading file-diff-list is slow
Diff viewing is based on Git operations (i.e. it fetches the commit from the Gerrit remote). When loading the file list
takes a lot of time, you can run a local "[git gc]" and ask your Gerrit administrator to do run a "[gerrit gc]".
[git gc]: https://www.kernel.org/pub/software/scm/git/docs/git-gc.html
[gerrit gc]: https://gerrit-review.googlesource.com/Documentation/cmd-gc.html

Architecture
------------
### IntelliJ Integration
The plugin is integrated into the IntelliJ IDE with a [tool window].
[tool window]: http://confluence.jetbrains.com/display/IDEADEV/IntelliJ+IDEA+Tool+Windows
See package <code>com.urswolfer.intellij.plugin.gerrit.ui</code>.

### REST API
Most of the communication between the plugin and a Gerrit instance is based on the [Gerrit REST API].
The REST specific part is available as [standalone implementation].
[Gerrit REST API]: https://gerrit-review.googlesource.com/Documentation/rest-api.html
[standalone implementation]: https://github.com/uwolfer/gerrit-rest-java-client
See package <code>com.urswolfer.intellij.plugin.gerrit.rest</code>.

### Git
Some actions like comparing and listing files are based on Git operations. [IntelliJ Git4Idea] is used for these operations.
[IntelliJ Git4Idea]:http://git.jetbrains.org/?p=idea/community.git;a=tree;f=plugins/git4idea
See package <code>com.urswolfer.intellij.plugin.gerrit.git</code>.


Build (and develop!) the Plugin
------------------

To build the plugin on your machine you need to have at least a downloaded copy of IntelliJ.
It's very easy to set it up as a plain IntelliJ project (without Maven integration). Just ignore the Maven paragraph if
you are unsure.

### IntelliJ Project

1. Activate Plugin DevKit in IntelliJ
2. <code>git clone https://github.com/uwolfer/gerrit-intellij-plugin</code> (probably switch to intellij13 branch)
3. <code>cd gerrit-intellij-plugin</code>
4. <code>git submodule init</code>
5. <code>git submodule update</code>
6. Open checked out project in IntelliJ (File -> Open...)
7. Open project settings, Project -> Project SDK -> New -> IntelliJ Platform SDK -> Choose your IntelliJ installation folder
8. Open project settings, Modules -> Dependencies -> Click + -> Jar or directory -> Choose idea4git/lib (e.g. /Applications/IntelliJ IDEA.app/plugins/git4idea/lib) -> set Scope to "provided"
9. Open the gerrit-intellij-plugin.iml file and revert changes at the top (first lines and the module directories around line 10) (strange, but IntelliJ breaks things here...), reload project. You might have to repeat this step if you change project settings...
10. Open Run Configuration dialog -> New -> Plugin -> Use classpath of module -> choose gerrit-intellij-plugin -> (Name: "Gerrit" or whatever you like)
11. Press "Debug" button. IntelliJ should start with a clean workspace (development sandbox). You need to checkout a project to see changes (it shows only changes for Git repositories that are set up in current workspace).

### Maven (not officially supported)

The plugin depends on multiple jars of IntelliJ IDEA but as these are not available via Maven Central, you'll have to
install the various Intellij jars located in the lib folder of your IntelliJ install into your local Maven repository.

For your convienience there is a bash script that will do exactly this for you
```
    $ cd gerrit-intellij-plugin/
    $ ./install-intellij-libs.sh <IntelliJ Version> <Path to IntelliJ>
```

To run the maven build you'll also need to install an updated version of [ideauidesigner-maven-plugin].
See that readme for installation instructions.
[ideauidesigner-maven-plugin]: https://github.com/gshakhn/ideauidesigner-maven-plugin/tree/12.x

Current issue: You need to change the scope of idea and forms_rt in pom.xml from provided to compile in order to
successfully build it with maven.

After you install all jars that this plugin needs into your local repo, just run

    mvn package

The resulting zip file will be located in the target folder.


Credits
------
* https://github.com/gshakhn/sonar-intellij-plugin/ for code examples (e.g. Maven integration, IntelliJ Plugin setup)
* IntelliJ Github plugin (some code of this plugin is based on its code)


Donations
--------
If you like this work, you can support it with [this donation link]. If you don't like Paypal
(Paypal takes 2.9% plus $0.30 per transaction fee from your donation), please contact me.
Please only use the link from github.com/uwolfer/gerrit-intellij-plugin to verify that it is correct.
[this donation link]: https://www.paypal.com/webscr?cmd=_s-xclick&hosted_button_id=8F2GZVBCVEDUQ


Copyright and license
--------------------

Copyright 2013 - 2014 Urs Wolfer

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this work except in compliance with the License.
You may obtain a copy of the License in the LICENSE file, or at:

  [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
