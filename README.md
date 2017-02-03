# **pgfmutate.py**

## What is it?

pgfmutate.py is a very simple python module that creates a base interface for
*mutating* (modifying) HEKA PatchMaster (v9-compatible) PGF files.

## How do I use it?

Just import it like any other python module:

```import pgfmutate as pm
```

## Yes, but how do I *use* it?

The majority (if not *all*) of your communications with this library should be
through the `PGFTree` and `PGFTNavigator` classes.

File operations (i.e. loading and saving files) should be done through the
`PGFTree` class.

```pgf = pm.PGFTree('myfavorite.pgf') # Load 'myfavorite.pgf'
pgf.save('myfavoritecopy.pgf') # Save a copy as 'myfavoritecopy.pgf'
pgf.load('myleastfavorite.pgf') # Load a different PGF file
# ... MUTATE ...
pgf.save('myleastfavorite_mutated.pgf') # Save the changes
```

Tree navigation and manipulation should be done through the `PGFTNavigator`
class.

```nav = pgf.get_nav() # Get a navigator pointing to the root node
nav.descend(0) # Descend to the first subnode branching off the root
nav.ascend() # Go back up
nav.descend(0).ascend().descend(0).descend(0) # This is totally legal
nav.copy() # Copy the current node to the clipboard
nav.ascend().ascend().descend(1).paste() # Paste contents of the clipboard
nav.delete() #  Delete the current node
```

Editing node/record contents can be done by editing the attributes of the
instance of the appropriate Record subclass returned by `PGFTNavigator.get()`:

```nav = pgf.get_nav()
while nav.get().num_children > 0: # go to the leftmost end node
    nav.descend(0)
nav.get().seVoltage = 10000000000 # This seems appropriate for a patch-clamp
```
The names of attributes are all exactly the same as the ones found in
[the official PGF documentation](ftp://server.hekahome.de/pub/FileFormat/Patchmasterv9/FileFormat_v9.zip).
All Record subclasses also have the attribute `num_children`, which (as the name
implies) corresponds to the number of child nodes that that node has.

That's basically it, I think!

## **!!WARNING!!** :cold_sweat:
This library offers basically *no error checking whatsoever*. That means, if you
do things like set two values to be in conflict, this library will not be able
to warn or stop you! In addition, in most cases if something bad happens with
saving or loading -- e.g., the file doesn't exist -- the library won't handle
any exceptions, which will be left to bubble up to the user (you).
