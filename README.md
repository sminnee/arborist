Arborist - Subversion Tree Doctoring
====================================

I made Arborist because dealing with merging between subversion branches is a pain in the arse.  The
particular merge problem it is trying to solve is when you have multiple release branches of a
project, like this:

 * trunk - all changes from rel2 and rel1 need to get into here
 * rel2 branch - all changes from rel1 need to get into here
 * rel1 branch

It provides tools to keep track of what still needs to be merged in, and assists with the merging
process.

In addition, it is designed to deal with projects that are broken down into separate modules, linked
together with `svn:externals` references.

.mergesources.yml
-----------------

The key to using arborst is to create a `.mergesources.yml` file.  It should be of the following
format:

	rel1:
		.: /project/branches/rel1
		sapphire: /modules/sapphire/branches/rel1
		cms: /modules/cms/branches/rel1
		jsparty: /modules/jsparty/branches/rel1
		cmsworkflow: /modules/cmsworkflow/branches/rel1
	rel2:
		.: /project/branches/rel2
		sapphire: /modules/sapphire/branches/rel2
		cms: /modules/cms/branches/rel2
		jsparty: /modules/jsparty/branches/rel2
		cmsworkflow: /modules/cmsworkflow/branches/rel2

In this example, we have indicated that there are two extra release branches of this project that
need to be merged into the current version (presumably trunk): rel1 and rel2.  For each of these
releases, we have branched not only the root project folder, `.`, but 4 modules linked in as
`svn:externals`: `sapphire`, `cms`, `jsparty`, and `cmsworkflow`.

Using `arb`
-----------

Once the `.mergesource.yml` file has been set up, we use it as follows:

### Basic Usage

Calling this command will give a summary of everything that needs to be merged in:

	> arb

Alternatively, you can see a summary of everything that should be merged in from one
release/project:

	> arb rel1

Once we see that, we can start an interactive session with a particular merge source.  Choose a
mergesource and start arborist using a command of this form:

	> arb rel1/sapphire

Arborist will print recommended merge and commit commands on the script, asking whether or not you
would like to execute each of them.

 * To execute the command, press enter
 * To skip the command, enter "n"
 * To stop the script exectue, press Ctrl-C

### Resuming an aborted merge session

	> arb rel1/sapphire --startrev=1234

This will execute the same command as the previous item; however, it will start the merging from
revision #1234.  This is useful if you had to cancel your arborist session for some reason.

### Cherry picking revisions

	> arb rel1/sapphire --revs=100,101,106,107

This will limit the scope of your arborist execution to some specific revisions.  This is useful if
you want to use arborist to cherry pick, rather than do a full merge.