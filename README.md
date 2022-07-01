
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

### multi_serial_server
```
from socket import *

#Step 1 - Create a TCP/IP socket.
#This socket is used in listening for connections
srvr_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Bind the socket - Argument is an address tuple
srvr_sock.bind(('localhost',5500))

#Step 3 - Make the socket listen for valid connections
srvr_sock.listen(5)#5 is the number of clients that could be queued before rejection
print("Server started....")

while True:
    #Step 4 - Socket accepts when a valid client connects
    tpl = srvr_sock.accept() 

    #Server sends a welcome message
    wel_msg = "Welcome client %s"%tpl[1][0]
    conn = tpl[0]
    conn.send(wel_msg.encode())

    #Receive the reply from client
    rec_msg=conn.recv(1024)
    print(rec_msg)
    conn.close()
```
####client1
```
from socket import *
import time

#Step 1 - Create a TCP/IP socket.
#This socket is used to connect to any TCP/IP socket server
clnt_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Connect the socket to the server
clnt_sock.connect(('localhost',5500))

#Step 3 - Receive the welcome message from the serverr
msg = clnt_sock.recv(1024)
print(msg)

#make it sleep for 10 seconds
time.sleep(20)

#Step 4 - Sends reply
rep = "Client1: Starting to talk...."
clnt_sock.send(rep.encode())

#Step 5 - Close the socket
clnt_sock.close()

```
#### client 2
```
from socket import *
import time

#Step 1 - Create a TCP/IP socket.
#This socket is used to connect to any TCP/IP socket server
clnt_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Connect the socket to the server
clnt_sock.connect(('localhost',5500))

#Step 3 - Receive the welcome message from the serverr
msg = clnt_sock.recv(1024)
print(msg)

#This client does not sleep

#Step 4 - Sends reply
rep = "Client2: Starting to talk...."
clnt_sock.send(rep.encode())

#Step 5 - Close the socket
clnt_sock.close()

```

### multi_parallel server
``` 
from socket import *
import threading

#Step 1 - Create a TCP/IP socket.
#This socket is used in listening for connections
srvr_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Bind the socket - Argument is an address tuple
srvr_sock.bind(('localhost',5700))

#Step 3 - Make the socket listen for valid connections
srvr_sock.listen(5)#5 is the number of clients that could be queued before rejection
print("Server started....")

#One client-server talking  - work handled by thread
def clientTalking(*tpl):
    #Server sends a welcome message
    wel_msg = "Welcome client %s"%tpl[1][0]
    conn = tpl[0]
    conn.send(wel_msg.encode())

    #Receive the reply from client
    rec_msg=conn.recv(1024)
    print(rec_msg)    #Reverse the reply and send it back
    b_msg = str(rec_msg)[::-1].encode()
    conn.send(b_msg)
    conn.close()



while True:
    #Step 4 - Socket accepts when a valid client connects
    tpl_clnt = srvr_sock.accept() #same as >>>a,b = srvr_sock.accept()
    #The result of accpet is a tuple of two elements
    #First element is another socket object for talking between the server and client
    #Second element is the address tuple (ip, port) of the client

    #Every client-server talking is handled by a thread
    thrd = threading.Thread(target=clientTalking,args=tpl_clnt)
    thrd.start()
```

#### client 1
``` 
from socket import *
import time

#Step 1 - Create a TCP/IP socket.
#This socket is used to connect to any TCP/IP socket server
clnt_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Connect the socket to the server
clnt_sock.connect(('localhost',5700))

#Step 3 - Receive the welcome message from the serverr
msg = clnt_sock.recv(1024)
print(msg)

#make it sleep for 10 seconds
time.sleep(20)

#Step 4 - Send a string to the server
msg = "Hello to server from client 1"
clnt_sock.send(msg.encode())

#Step 5 - Receive the reversed string from the server and print
msg = clnt_sock.recv(1024)
print(msg)

clnt_sock.close()

```

#### client 2
```
from socket import *
import time

#Step 1 - Create a TCP/IP socket.
#This socket is used to connect to any TCP/IP socket server
clnt_sock = socket(AF_INET,SOCK_STREAM)

#Step 2 - Connect the socket to the server
clnt_sock.connect(('localhost',5700))

#Step 3 - Receive the welcome message from the serverr
msg = clnt_sock.recv(1024)
print(msg)

#This client does not sleep

#Step 4 - Send a string to the server
msg = "Hello to server from client 2"
clnt_sock.send(msg.encode())

#Step 5 - Receive the reversed string from the server and print
msg = clnt_sock.recv(1024)
print(msg)

clnt_sock.close()
```


### UDP Server
``` 
import socket
import sys

# Create a UDP/IP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Bind the socket to the port
server_address = ('localhost', 10000)
print('starting up on %s port %s' % server_address)
sock.bind(server_address)

print('\nwaiting to receive message')
data, address = sock.recvfrom(4096)

print('received %s bytes from %s' % (len(data), address))
print(data)

if data:
    sent = sock.sendto(data, address)
    print('sent %s bytes back to %s' % (sent, address))
```
### Server_UDP
```
import socket
import sys

# Create a UDP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_address = ('localhost', 10000)
message = 'This is the message.  It will be repeated.'

try:

    # Send data
    print('sending "%s"' % message)
    sent = sock.sendto(message.encode(), server_address)

    # Receive response
    print('waiting to receive')
    data, server = sock.recvfrom(4096)
    print('received "%s"' % data)

finally:
    print('closing socket')
    sock.close()
```

### UDP Client
```
import socket
import sys

# Create a UDP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_address = ('localhost', 10000)
message = 'This is the message.  It will be repeated.'

try:

    # Send data
    print('sending "%s"' % message)
    sent = sock.sendto(message.encode(), server_address)

    # Receive response
    print('waiting to receive')
    data, server = sock.recvfrom(4096)
    print('received "%s"' % data)

finally:
    print('closing socket')
    sock.close()
 ```

### Logical Programming

###Factorial

```
#Factorial of N
pyDatalog.create_terms('factorial, N')

factorial[N] = N*factorial[N-1]
factorial[1] = 1

print(factorial[5]==N)
```

###big bear
```
from pyDatalog import pyDatalog
pyDatalog.create_terms("Big","Bear","Elephant","Small","Cat", "Brown", "Black", "Gray", "Elephant", "X", 'Y', "Size", "Color","Dark")

+Size("Bear", "Big")
+Size("Elephant", "Big")
+Size("Cat", "Small")
+Color("Bear", "Brown")
+Color("Cat", "Black")
+Color("Elephant", "gray")
Dark(X) <= (Color(X, "Black"))
Dark(X) <= (Color(X, "Brown"))
print("Dark and Big animals are --")
print(Dark(X) & Size(X, "Big"))
```

###Father Child
```
from pyDatalog import pyDatalog
pyDatalog.create_terms('father','X','Y','Z','M','brothers','cousins','grandson','descendent')

+(father["f"]=="c")
+(father["c"]=="a")
+(father["b"]=="a")
+(father["d"]=="b")
+(father["e"]=="b")

""" Define family rules """
brothers(X, Y) <= (father[X] == Z) & (father[Y] == Z) & ~(X==Y)
cousins(X, Y) <= (father[X] == Z) & (father[Y] == M) & (brothers(M,Z))
grandson(X, Y) <= (father[X] == Z) & (father[Z] == Y)
descendent(X, Y) <= (father[X] == Y)

print("Brothers in the family are ")
print(brothers(X, Y))
print("\n\nCousins are ")
print(cousins(X, Y))
print("\nGrandson are ")
print(grandson(X, Y))
print("Descendent : ")
print(descendent(X,Y))

print(Y==father[X])
```

##THREADING
```
import threading
import time
def print_time(threadName, counter, delay):
    while counter:
      time.sleep(delay)
      print ("%s: %s" % (threadName, time.ctime(time.time())))
      counter -= 1
class myThread (threading.Thread):#Step 1 - Subclass the threading class
    def __init__(self, name, counter,delay):#Step 2 - Call parent class constructor in child
        threading.Thread.__init__(self)
        self.name = name
        self.counter = counter
        self.delay = delay
    def run(self):#Step 3 - Override the run method
        print( "Starting " + self.name)
        print_time(self.name, self.counter, self.delay)
        print ("Exiting " + self.name)
# Step 4 - Create new threads
thread1 = myThread("Thread-1", 2,2)
thread2 = myThread("Thread-2", 3,1)
# Step 5 - Start new Threads
thread1.start()
thread2.start()
#Parent Wait till all children finish
thread1.join()
thread2.join()
print ("Exiting Main Thread")
```

## CAR PARKING USING THREAD
```
# An example python program using semaphore provided by the python threading module
import threading
import time
parkedLock      = threading.Lock()
removedLock     = threading.Lock()
#Counters for total number of parkings and removals
parked          = 0
removed         = 0
#Semaphore
availbleParkings = threading.Semaphore(5)#Only five parking slots available

def ParkCar(): #Parking thread will execute this work
        availbleParkings.acquire()#Decremented by one
        global parkedLock
        parkedLock.acquire()#Acquire lock so that no other thread simultaneously modifies the parkedLock global variable
        global parked
        parked = parked+1
        parkedLock.release()
        print("Parked: %d"%(parked))      
def RemoveCar(): #Removing thread will execute this work
        availbleParkings.release()#Incremented by one
        global removedLock
        removedLock.acquire()
        global removed
        removed = removed+1
        removedLock.release()
        print("Removed: %d"%(removed))       
# Thread that simulates the entry of cars into the parking lot
def parkingEntry():
    # Creates multiple threads inside to simulate cars that are parked
    for i in range(6):
        time.sleep(1)
        incomingCar = threading.Thread(target=ParkCar)
        incomingCar.start()

# Thread that simulates the exit of cars from the parking lot
def parkingExit():
    # Creates multiple threads inside to simulate cars taken out from the parking lot
    for j in range(5):
        time.sleep(15)
        outgoingCar = threading.Thread(target=RemoveCar)
        outgoingCar.start()

# Start the parking eco-system
parkingEntryThread      = threading.Thread(target=parkingEntry)
parkingExitThread       = threading.Thread(target=parkingExit)
parkingEntryThread.start()
parkingExitThread.start()
```
