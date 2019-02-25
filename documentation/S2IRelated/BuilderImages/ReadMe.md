# The following are the supported s2i builder images available for OpenShift Container Platform users

1. Node.JS
2. Java
3. .NET Core
4. Python
5. Perl
6. Ruby
7. PHP

# Customizing S2I Images - S2I builder images normally include assemble and run scripts, but the default behavior of those scripts may not be suitable for all users
1. assemble

```
#!/bin/bash
echo "Before assembling"

/usr/libexec/s2i/assemble
rc=$?

if [ $rc -eq 0 ]; then
    echo "After successful assembling"
else
    echo "After failed assembling"
fi

exit $rc
```

2. run
```
#!/bin/bash
echo "Before running application"
exec /usr/libexec/s2i/run
```
Note: When wrapping the run script, you must use exec for invoking it to ensure signals are handled properly. Unfortunately, the use of exec also precludes the ability to run additional commands after invoking the default image run script.
