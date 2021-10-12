# Image-Creation-with-Dockerfile-

Type the following content into a file named index.js. 

    var os = require("os");
    var hostname = os.hostname();
    console.log("hello from " + hostname);

The file we just created is the javascript code for our server. As you can probably guess, Node.js will simply print out a “hello” message. 

We will Docker-ize this application by creating a Dockerfile. We will use alpine as the base OS image, add a Node.js runtime and then copy our source code in to the container. We will also specify the default command to be run upon container creation.

Create a file named Dockerfile and copy the following content into it. Again, help creating this file with Linux editors is here.

    FROM alpine
    RUN apk update && apk add nodejs
    COPY . /app
    WORKDIR /app
    CMD ["node","index.js"]
 
Let’s build our first image out of this Dockerfile and name it hello:v0.1:

    docker image build -t hello:v0.1 .

We then start a container to check that our applications runs correctly:

    docker container run hello:v0.1

You should then have an output similar to the following one (the ID will be different though).

    hello from 92d79b6de29f

First, check out the image you created earlier by using the history command (remember to use the docker image ls command from earlier exercises to find your image IDs):

    docker image history <image ID>

What you see is the list of intermediate container images that were built along the way to creating your final Node.js app image. Some of these intermediate images will become layers in your final container image. In the history command output, the original Alpine layers are at the bottom of the list and then each customization we added in our Dockerfile is its own step in the output. This is a powerful concept because it means that if we need to make a change to our application, it may only affect a single layer! To see this, we will modify our app a bit and create a new image.

Type the following in to your console window:

    echo "console.log(\"this is v0.2\");" >> index.js

This will add a new line to the bottom of your index.js file from earlier so your application will output one additional line of text. Now we will build a new image using our updated code. We will also tag our new image to mark it as a new version so that anybody consuming our images later can identify the correct version to use:

    docker image build -t hello:v0.2 .

You should see output similar to this:

    Sending build context to Docker daemon  86.15MB
    Step 1/5 : FROM alpine
     ---> 7328f6f8b418
    Step 2/5 : RUN apk update && apk add nodejs
     ---> Using cache
     ---> 2707762fca63
    Step 3/5 : COPY . /app
     ---> 07b2e2127db4
    Removing intermediate container 84eb9c31320d
    Step 4/5 : WORKDIR /app
     ---> 6630eb76312c
    Removing intermediate container ee6c9e7a5337
    Step 5/5 : CMD node index.js
     ---> Running in e079fb6000a3
     ---> e536b9dadd2f
    Removing intermediate container e079fb6000a3
    Successfully built e536b9dadd2f
    Successfully tagged hello:v0.2

Notice something interesting in the build steps this time. In the output it goes through the same five steps, but notice that in some steps it says Using cache.
