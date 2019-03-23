# S2I Related Build Notes

## An organization has an option to use:
1. From a number of pre-built S2I images or
2. Customise the existing S2I builder image or
3. Build custom S2I builder images.

S2I Build Process - Refer diagram.
1 - S2I Builder Image
2 - S2I Scripts - assemble and run scripts are mandatory, others such as save-artifacts, usage, test/run are not mandatory

**Note**We can create our own custom S2I Builder Image and S2I Scripts. 

## Override S2I Default Scripts:
1. S2I builder images provide their own default S2I scripts. 
2. We can change the default S2I build and deloyment scripts to change how our application is built and executed. 
3. To provide your own S2I scripts -- Create the scripts in $APPHOME/.s2i/bin/
 
## Incremental S2I Builds:
1. A java application with a large number of dependencies managed using maven
2. A NodeJS application with a large number of dependencies managed using npm

## S2I Build Sequence:
1. Instantiate the appropriate S2I builder image
2. Clone the Application source into the container
3. "assemble" i.e build
4. Add "run" as the CMD entry point to the image (so you have an option to overwrite)
5. Commit the image
6. Push the image to the registry.


## S2I Advantages:
```
	Operational security
		Building arbitrary Dockerfile exposes host system to root privilege escalation

		Can be exploited by malicious user because entire Docker build process is run as user with Docker privileges

		S2I restricts operations performed as root user and can run scripts as non-root user

	Ecosystem
	S2I encourages shared ecosystem of images, allowing developers to leverage best practices for applications

	Reproducibility
	Produced images can include all inputs, including specific versions of build tools and dependencies

	Ensures that image can be reproduced precisely

	Image flexibility
	S2I scripts can be written to inject application code into almost any existing Docker image, taking advantage of existing ecosystem

	Currently, S2I relies on tar to inject application source, so image needs to be able to process tarred content

	Speed
	With S2I, assemble process can perform large number of complex operations without creating new layer at each step, resulting in fast process

	S2I scripts can be written to reuse artifacts stored in previous version of application image, rather than having to download or build them each time build is run

	Patchability
	S2I allows you to rebuild application consistently if underlying image needs patch due to security issue

	Operational efficiency
	By restricting build operations and preventing arbitrary actions that Dockerfile allows, PaaS operator can avoid accidental or intentional abuses of build system
```

## Docker ONBUILD 
1. Parent image can be as simple as just having the FROM instruction.
2. ONBUILD instructions are executed when the build of a child image is triggered.
3. Child image content can just be:
```
FROM PARENT_IMAGE_WITH_ONBUILD_INSTRUCTIONS
RUN echo "Started npm application"
```

## Ref Diagram
![Alt text](s2iflow.png?raw=true "")
