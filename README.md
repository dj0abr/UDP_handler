# UDP_handler

this little program is used to send and receive messages via UDP

## Highlights

* multiple OSs: Linux & Windows
* as many independent UDP port as you need
* reception: multithreading -> unblocking read !
* extremly easy to use and to integrate into your software

## Funktions

void UdpRxInit(int *sock, int port, void (*rxfunc)(uint8_t *, int, struct sockaddr_in*), int *keeprunning);

called to initialize an UDP receiver

sock ... a pointer to an int variable. Gets the socket reference.
port ... the UDP port number
rxfunc ... when an UDP message is received this callback function will be called (user provided function, see below)
keeprunning ... a pointer to an int variable. Init this variable with 1. When you want to exit the UDP threads, set it to 0 (i.e. at program end)

------

void sendUDP(char *destIP, int destPort, uint8_t *pdata, int len);

send an UDP message

destIP ... IP address of the destination
destPort ... port number of the destination
pdata ... pointer to a byte array holding the message
len ... length of the message

-------

this callback routine must be provided by the application.
It is called when an UDP message is received.
For each UDP receiving port a separate callback function is required

rxfunc(uint8_t *pdata, int len , struct sockaddr_in* socket)

pdata ... pointer to a byte array holding the received message
len ... length of the message
socket ... a sockaddr_in structure holding information of the sender

------

## Usage

* copy udp.cpp and udp.h into your source directory and include udp.cpp to your Makefile
* depending o your OS #define _LINUX_ or _WIN32_
* at program start call UdpRxInit to initialize an UDP receiver (only if you need an receiver)

Example:
```
int socket1;
int socket2;
int keeprunning = 1;
UdpRxInit(&socket1, 12345, rxfunc1, &keeprunning);
UdpRxInit(&socket2, 33445, rxfunc2, &keeprunning);
```
* write a callback function for all receiving UDP ports, like that:

Example:

void rxfunc1(uint8_t *pdata, int len , struct sockaddr_in* socket)
{
    printf("Received %d Bytes from IP: %s\n",len,inet_ntoa(socket->sin_addr);
    for(int i=0; i<len; i++) printf("Byte %d = %02X\n",i,pdata[i]);
}

void rxfunc2(uint8_t *pdata, int len , struct sockaddr_in* socket)
{
    printf("Received %d Bytes from IP: %s\n",len,inet_ntoa(socket->sin_addr);
    for(int i=0; i<len; i++) printf("Byte %d = %02X\n",i,pdata[i]);
}

* to send an UDP message, just call sendUDP

Example:
unsigned char data[100];
sendUDP("192.168.0.1",12345,data,100);

