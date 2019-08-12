### Defining Commands and arguments in Dockerfile

The whole command that gets executed in a Dockerfile is a combination of 2 parts; the `command` and the `arguments`

In Dockerfile, two instructions define those 2 parts:
- `ENTRYPOINT` defines the executable invoked when the container is started
- `CMD` specifies the arguments that get passed to the `ENTRYPOINT`

Although you can use the `CMD` instruction to specify the command you want to execute when the image is run, the correct way is to do it through the `ENTRYPOINT` instruction and to only specify the `CMD` if you want to define the default arguments. The image can then be run without specifying any arguments.

```$ docker run <image>```

or with additional arguments, which override whatever’s set under CMD in the Dockerfile: 

```$ docker run <image> <arguments>```

#### Understanding the difference between shell and exec forms
- `shell` form — For example, `ENTRYPOINT node app.js`.
- `exec` form - For example, `ENTRYPOINT ["node", "app.js"]`.

The difference is whether the specified command is invoked inside a shell or not.

For example, if we run `ENTRYPOINT ["node", "app.js"]`. 
This runs the node process directly (not inside a shell), as you can see by listing the processes running inside the container:

```
$ docker exec 4675d ps x
PID  TTY  STAT   TIME COMMAND
 1 ?      Ssl    0:00 node app.js
12 ?      Rs     0:00 ps x
```

If you use the shell form (`ENTRYPOINT node app.js`), these would have been the container’s processes:

```
$ docker exec -it e4bad ps x
  PID  TTY      STAT   TIME COMMAND
    1  ?        Ss     0:00 /bin/sh -c node app.js
    7  ?        Sl     0:00 node app.js
    13 ?        Rs+    0:00 ps x
```

As you can see, in that case, the main process (PID 1) would be the shell process instead of the node process. The node process (PID 7) would be started from that shell. The shell process is unnecessary, which is why you should always use the exec form of the ENTRYPOINT instruction.

