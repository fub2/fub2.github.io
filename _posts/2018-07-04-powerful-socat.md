
The socat command shuffles data between two locations. One way to think of socat is as the cat command which transfers data between two locations rather than from a file to standard output. I say that socat works on two locations rather than two files because you can grab data from a network socket, named pipe, or even setup a general virtual network interface as one end point. 

Socat is a tool to manipulate sockets, one input and one output. But the idea of sockets is too restrictive. The documentation speaks about “data channels” which can be combinations of:

* a file
* a pipe
* a device (ex: a serial line)
* a socket (IPv4, IPv6, raw, TCP, UDP, SSL)
* a FD (STDIN, STDOUT)
* a program or script
For each data channel, parameters can be added (port, speed, permissions, owners, etc). For those who use Netcat, the default features remain the same.

* Example 1: To exchange data via a TCP session across two hosts:

```
  hosta$ socat TCP4-LISTEN:31337 OPEN:inputfile,creat,append
  hostb$ cat datafile | socat - TCP4:hosta:31337
```

* Example #2: To use a local serial line (to configure a network device or access a modem) without a terminal emulator

```
  $ socat READLINE,history:/tmp/serial.cmds \
  OPEN:/dev/ttyS0,ispeed=9600,ospeed=9600,crnl,raw,sane,echo=false
```

The “READLINE” data channel uses GNU readline to allow editing and reusing input lines like a classic shell.

* Example #3: To grab some HTTP content without a browser

```
  $ cat <<EOF | socat - TCP4:blog.rootshell.be:80
  GET / HTTP/1.1
  Host: blog.rootshell.be

  EOF
```

* Example #4: To use Socat to collect Syslog messages

```  # socat -u UDP4-LISTEN:5140,reuseaddr,fork OPEN:/tmp/syslog.msg,creat,append```

* Example #5: The “EXEC” channel allow us to specify an external program or script. Using the “fdin=” and “fdout=” parameters, it is easy to parse the information received from the input channel and to send back information.

```
  $ socat TCP4:12.34.56.78:31337 EXEC:parse.sh,fdin=3,fdout=4
```

The following Bash script simulates a web server and can look for suspicious content. If none is found, the visitor is redirected to another site. Note that, for security reasons, “EXEC” does not allow a relative path for the executable. It must be present in your $PATH.

```
  #!/bin/bash
  #
  # Simple example of honeypot running on HTTP
  # Usage: socat TCP4-LISTEN:80,reuseaddr,fork EXEC:honeypot.sh,fdin=3,fdout=4
  # FD 3 = incoming traffic
  # FD 4 = traffic sent back to the client
  #

  # Define the patterns for bad traffic here
  BADTRAFFIC1="../../.."
  BADTRAFFIC2="foobar"

  # Process the received HTTP headers
  while read -u 3 BUFFER
  do
    [ "$BUFFER" = "^M" ] && break
    echo $BUFFER | egrep -q -o "($BADTRAFFIC1|$BADTRAFFIC2)"
    if [ "$?" = "0" ]; then
      echo "ALERT: Suspicous HTTP: $BUFFER" >>http.log
      cat <END0 >&4
      <html>
      <body>
      <h1>This incident has been logged...</h1>
      </body>
      </html>
      END0
      exit 0
    fi
  done
  cat <<END1 >&4
  <html>
  <meta http-equiv="refresh" content="0; url=https://blog.rootshell.be">
  <body
  You will be redirected soon...
  </body>
  </html>
  END1
```

By using the file descriptors 3 and 4, we can easily read what’s sent by the client and send data into the TCP session.

# socat: The General Bidirectional Pipe Handler

Because socat allows bidirectional data flow between the two locations you specify, it doesn't really matter which order you specify them in. Locations have the general form of TYPE:options where TYPE can be CREATE, GOPEN or OPEN for normal filesystem files. There are also shortcuts for some locations like STDIO (or just -) which reads and writes to standard input and output respectively.

The SYSTEM type can be used to execute a program and connect to its standard input and output. For example, the command shown below will run the date command and transfer its output to standard output.

```
$ socat SYSTEM:date -
Thu Apr 23 12:57:00 EST 2009
```

Many network services handle control commands using plain text. For example, SMTP servers, HTTP servers. The below socat command will open a connection to a Web server and fetch a page to the console. Notice that the port is specified using the service name and a comma separates the address from the cnrl option which handles line termination transformations for us.

```
$ socat - TCP:localhost:www,crnl
GET /

...
```

If the network service is more interactive, you might like to use readline to track your command history, improve command editing, and allow you to search and recall your previous commands. Instead of connecting standard IO as the first location in the above command, using READLINE,history=$HOME/.http_history will cause socat to use readline to get your commands.

Many of the socat location TYPEs take more than one option. For example, GOPEN (generic open) lets you specify append if you would like to append too rather than overwrite the file. The below keeps a log file of the time each time you execute it. This is similar to the Web server example, a comma separated list of additional options for the location.

```$ date | socat - GOPEN:/tmp/capture,append
```

While this example is quite superfluous in that you could just use the shell >> redirection to append to the file, you could also include a network link into the mix with minimal effort using socat as shown below. The first command connects port 3334 on localhost to the file /tmp/capture. The seek-end moves the file to zero bytes from the end and the append makes sure that bytes are appended to the file rather than overwriting it. The client command, shown as the second command below, is very similar to the simpler example shown above except we now send standard IO to a socket address.

```
$ socat TCP4-LISTEN:3334,reuseaddr,fork gopen:/tmp/capture,seek-end=0,append
$ date | socat STDIO tcp:localhost:3334
```

One great use case for socat is making device files from one machine available on another one. I'll use the example from the socat manual page shown below to demonstrate. The first location creates a PTY device on the local machine allowing raw communication with the other location. The other location is an ssh connection to a server machine, where the standard IO is connected to the serial device on the remote machine.

```
(socat PTY,link=$HOME/dev/vmodem0,raw,echo=0,waitslave \
 EXEC:"ssh   modem-server.example.com socat - /dev/ttyS0,nonblock,raw,echo=0")
```
 
While creating virtual modems is not as attractive as it might once have been, other devices can be moved around too. The below command makes /dev/urandom from a server available through a named pipe on the local machine.

```
socat \
  PIPE:/tmp/test/foo  \
  SYSTEM:"ssh myserver socat - /dev/urandom" 
```

Creating a Virtual Private Network over SSH in a Single Line
Virtual networks are created using the TUN device of the Linux kernel. Note that if you send data to a TUN device there is no encryption happening so if those packets move over the real network you have a Virtual Public Network. While there are overviews of using socat with TUN and socat with SSL I think it is much simpler to just use SSH to protect the network link from eavesdropping. You probably already have SSH setup so its much simpler to use because no SSL certificates need to be generated and distributed. The trick with using ssh is how to bolt things together. You could setup port forwarding with ssh and use socat to connect those ports to a virtual TUN device. But that leaves forwarded ports between the two hosts which serve no legitimate purpose other than servicing the socat TUN devices.

It is clear that one end point will be a direct TUN location, and the other is leaning towards being an ssh into the remote host. The trick is making the ssh into the remote host use socat to connect its standard IO to a TUN device. So we use socat twice in the one command: once to connect a TUN to an ssh session on the local machine, and once to connect standard IO to a TUN device on the remote end.

The below command will setup the 192.168.32.2 address on localhost to communicate with 192.168.32.1 on the server host over a VPN. If you use the 192.168.32.1 address you should be able to connect to network services on the server as though it was on the LAN.

The first location just sets up a local TUN device with an address and brings the network interface up. The second location will ssh into the server machine and run socat there to connect the standard IO of the ssh session to a TUN device on the server. The -d -d options can be selectively removed to remove the debugging chatter from the local and remote socat processes but are very informative when experimenting.

```
# socat -d -d  \
    TUN:192.168.32.2/24,up \
    SYSTEM:"ssh root@server socat -d -d  - 'TUN:192.168.32.1/24,up'" 
```

You might need to be root to create TUN devices. If socat can not make them as the current user you will see a message like the below.

```
2009/04/23 14:41:09 socat[17930] E ioctl(3, TUNSETIFF, {""}: Operation not permitted
```

socat is a great tool to have in your collective command line toolbox. There are options to use socat with tcpwrappers, and a huge array of the parameters that can be set on sockets and other through other low level system calls can be tweaked through parameters to socat.

The ability to setup a makeshift VPN using ssh for data protection using a one line command could be just what you are after when you want to get at a few services without needing to research which ports you need to forward.
