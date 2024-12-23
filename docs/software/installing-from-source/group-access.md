## Group access

If you want others to be able to use 
software that you build from source,
the executables and libraries generated by the build
must be placed in a directory that others can access.

The simplest way to achieve this 
is to place the executables in your group space.
Group space for a PI on Collab is located at
`/storage/group/<PIuserID>/default`,
which is read-accessible by members of `<PIuserID>_collab`.
Note however that if a PI makes an alias 
to their group space in their home directory,
with the command
```
ln -s group /storage/group/<PIuserID>/default
```
the path `/storage/home/<PIuserID>/group` 
is *not* accessible to others,
because their home directory `/storage/home/<PIuserID>`
is not accessible to others.