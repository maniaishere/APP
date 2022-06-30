

### Single server
```
#To execute - From two command prompts, execute server (first) and client programs.

#Step 1 - Import socket
from socket import *

#Step 2 - Create a TCP/IP socket.
#This socket is used in listening for connections. Python uses BSD style sockets
#Here constant AF_INET represents Network layer protocol IP
#SOCK_STREAM represents Transport Layer protocol TCP
#For UDP use SOCK_DGRAM
srvr_sock = socket(AF_INET,SOCK_STREAM)

#Step 3 - Bind the socket - Argument is an address tuple
srvr_sock.bind(('localhost',5300))

#Step 4 - Define max queue size of connections
srvr_sock.listen(5)#5 is the number of clients that could be queued before rejection
print("Server started....")

#Step 5 - Listen indefinitely for one connection.
#Socket accepts when a valid client connects
tpl = srvr_sock.accept()#Blocking call-waits indefinite
#same as >>>a,b = srvr_sock.accept()
#The result of accept is a tuple of two elements
#First element is another socket object for talking between the server and client
#Second element is the address tuple (ip, port) of the client

#Server sends a welcome message
wel_msg = "Welcome client %s"%tpl[1][0]
conn = tpl[0]#New socket object for talking
conn.send(wel_msg.encode())

#Receive the reply from client
rec_msg=conn.recv(1024)#Blocking
print(rec_msg.decode())
conn.close()
srvr_sock.close()
```

### single client
```
from socket import *

#Step 1 - Create a TCP/IP socket.
#This socket is used to connect to any TCP/IP socket server
clnt_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Connect the socket to the server
clnt_sock.connect(('localhost',5300))

#Step 3 - Receive the welcome message from the serverr
msg = clnt_sock.recv(1024)
print(msg)
print(type(msg))

#Step 4 - Sends reply
print("Starting to talk.... Enter your message to server")
rep = input()
clnt_sock.send(rep.encode())

clnt_sock.close()

```
