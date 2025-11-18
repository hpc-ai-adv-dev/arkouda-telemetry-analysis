# arkouda-telemetry-analysis
Telemetry Analysis for large scale systems using Arkouda

## Getting started with Arkouda

Install Chapel (from source):
https://chapel-lang.org/docs/usingchapel/index.html

Note:
Build Chapel in multi-locale mode for running distributed programs

See also
[Platform specific notes](https://chapel-lang.org/docs/platforms/index.html)
for more details on how to get Chapel running on your system


Install Arkouda (from source):
https://bears-r-us.github.io/arkouda/setup/install_menu.html


### Launching and Connecting to an Arkouda Server

After installing and building the Arkouda server, you have to launch it in a
separate process.

In a terminal, launch it with:

```console
./arkouda_server -nl <numLocales>
```

Replace <numLocales> with the number of locales that is right for your system.

Upon starting up, the server will inform you of the hostname and port it
is listening on.

For example:

```console
server listening on tcp://node01:5555
```

You will use this hostname and port to connect to the Arkouda server in
this script.

```py
import arkouda as ak

# Connect to the Arkouda server
ak.connect('node01', 5555)
```

Modify the `ak.connect` command in the jupyter notebook you're trying to run to
match the node your server is running on.