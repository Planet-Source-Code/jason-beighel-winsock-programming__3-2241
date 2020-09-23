<div align="center">

## WinSock Programming


</div>

### Description

This article is my understanding of how to use the WinSock API. It explains how to create a socket, listen on a socket as a server, connect to a socket as a client, and how to pass information from the client to the server.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Jason Beighel](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/jason-beighel.md)
**Level**          |Beginner
**User Rating**    |4.7 (267 globes from 57 users)
**Compatibility**  |C\+\+ \(general\), Microsoft Visual C\+\+
**Category**       |[System Services/ Functions](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/system-services-functions__3-23.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/jason-beighel-winsock-programming__3-2241/archive/master.zip)





### Source Code

<html>
<head>
<title>Untitled Document</title>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">
</head>
<body bgcolor="#FFFFFF">
<font face="Verdana, Arial, Helvetica" size="2" color="#004080">
<p> First and foremost in order to use the Winsock API you have to link to the
 libraries mpr.lib and wsock32.lib. To do this in Visual Studio create a new
 project then under the "Projects" menu choose "Settings...", or just hit Alt+F7.
 In the top left of the dialog box there is a drop down list box labeled "Settings
 For:" change it to read "All Configurations". In the tab control on the right
 of the dialog box select the "Link" tab. In the middle of the tab there is an
 edit box labeled "Object/Library Modules:" add the name of the libraries you
 want to link to, be sure all the labraries in the list are separated by spaces.
 That being done you can now begin to program. </p>
<p>The first step in using the WinSock API is to initialize WSA. I'm not positive
 what WSA is, I'm assumng its short for WinSockApi, but I can't back that up.
 Whatever it is it has to be initilized. This is done by calling WSAStartup().
 This function takes two parameters a version number in a WORD value and a WSADATA
 structure, it returns an integer the return will be 0 if initialization is successful.
 Here is an example of the initialization process: </p>
<p>     WSADATA WsaDat;<br>
      if (WSAStartup(MAKEWORD(1, 1), &WsaDat) != 0)<br>
            {<br>
             printf("WSA
 Initialization failed.");<br>
            } </p>
<p>For the version number I use the macro MAKEWORD(). It splits the version number
 up and its easy to see what you are requesting. When you send that version number
 you are requesting a specific version of WinSock, in the example I am requesting
 version 1.1. You can request version 1.0, 1.1, and 2.0, version 2.0 is not available
 in Win 95 without being specifically installed it does exist in all later versions
 of Windows. The exact benifits of each version I'll leave to you to research,
 from what I have read version 1.1 has all the important features and since its
 available in all version of Windows without a patch it is acceptable for most
 applications.</p>
<p> After you have initialized WinSock the next step is to create a socket. Sockets
 are of two types stream sockets and datagram sockets. Stream sockets are easier
 to use so I'll demonstrate them. All sockets are of type SOCKET, and you create
 them with the socket() function. The socket() function takes three parameters.
 The first is the type of connection you would like to use, for this use AF_INET
 this designates you want to use an Internet style connection (or in other words
 use TCP/IP) as far as I know this is the only connection permitted through WinSock.
 The second parameter is the type of socket to use, for stream sockets use SOCK_STREAM,
 or for datagram sockets use SOCK_DGRAM. The thrid parameter is some value for
 the protocol from what I have read this value has very little meaning and is
 usually ignored so I always pass zero here. The socket() function will return
 the socket or INVALID_SOCKET if it can't create the socket. Here is an example
 of that:</p>
<p>      SOCKET Socket; <br>
       Socket = socket(AF_INET, SOCK_STREAM, 0);<br>
       if (Socket == INVALID_SOCKET) <br>
            {<br>
           printf("Socket creation
 failed.");<br>
            } </p>
<p>Now we have a usable socket, what we need to do is make use of it. As with
 any network connections you have to have a server and a client. For clarity
 I'm going to call the server the computer that is listening for and incoming
 connection and the client the computer that requests a connection with a server.
 Since the server has to be listening before a client can connect I'll show how
 to setup the server first. First we bind the socket to a TCP/IP port. This is
 done with the bind() function. The bind() function takes three parameters, a
 socket to bind to, a pointer to a data structure that has the port information
 (structure type STRUCTADDR), and the size of the structure with the port information.
 There are a few points of interest in this process so i'll just explain inside
 an example.</p>
<p> //The variables we will need SOCKADDR_IN SockAddr; <br>
 //We need a socket variable but for now lets assume its the variable Socket
 we prepared before. <br>
 //bind() does require one of those prepared sockets, so at one point you will
 need to create one. <br>
 /* For those who are paying attention you may have noticed that I said before
 that we need a struct SOCKADDR variable. Except I didn't declare one here. The
 reason is that struct SOCKADD_IN holds the same information in the same way
 as struct SOCKADDR does, the difference is that struct SOCKADDR_IN is easier
 to work with. */ </p>
<p>//We want to use port 50 <br>
 SockAddr.sin_port = 50; </p>
<p>//We want an internet type connection (TCP/IP) <br>
 SockAddr.sin_family = AF_INET; </p>
<p>//We want to listen on IP address 127.0.0.1 <br>
 //I'll give a few better ways to set this value later <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b1 = 127; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b2 = 0; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b3 = 0; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b1 = 1; </p>
<p>//Ok all the information is set, lets bind() <br>
 if (bind(Socket, (SOCKADDR *)(&SockAddr), sizeof(SockAddr)) == SOCKET_ERROR)
 <br>
       { <br>
       printf("Attempt to bind failed.");<br>
       }</p>
<p> That ought to be fairly straight forward to figure out. The connection type
 should always be AF_INET, the port is an unsigned integer between 0 and 65,565,
 and the address is four unsigned short values from 0 to 255 that is a valid
 IP address of the server. We can specify the IP address we want to listen to,
 what if we want to listen on multiple addresses? You could run throuh this process
 multiple times to bind a socket on each address, or you could set the SockAddr.sin_addr.S_un.S_addr
 to INADDR_ANY like this:</p>
<p> SockAddr.sin_addr.S_un.S_addr = INADDR_ANY;</p>
<p> Instead of setting the four octets of an IP address. The next issue that comes
 up would be how do I know my IP address? There is a way of finding the address,
 but its a little involved so I'm going to discuss that later. Now that we have
 a valid socket bound to a TCP/IP port we need to listen on that socket for incoming
 connections. We use the listen() function to accomplish that. The listen() function
 takes two parameters a bound socket and the number of connections to accept.
 Here is how that looks:</p>
<p> //Once again we're carrying through the Socket variable from the previous
 example. <br>
 //We're only going to accept 1 incoming connection. <br>
 listen(Socket, 1); </p>
<p>Not much to listen(). Just to clarify the listen() function does not accept
 the incoming connections, it just sets your socket to listening on the specified
 port, no more no less. To accept the incoming connection you use accept(). The
 accept() function will will watch the port for a breif time then return an error.
 So unless you know exactly when the connection is coming and can start accept
 at just the right time you are going to miss the connection. One way around
 this is to place accept() in a while loop until a connection is received. There
 is a problem with this technique, in a DOS or console application its fine since
 nothing else can be happening it doesn't matter, but in a windows program it
 will stop responding until it gets out of that loop. You may be able to set
 the accept() function to run on a short timer or in a loop that is called in
 a thread. At any rate here is how it would look if it were in a while loop until
 it received a connection:</p>
<p> //We are still carrying through the Socket variable from before <br>
 SOCKET TempSock = SOCKET_ERROR; <br>
 while (TempSock == SOCKET_ERROR) <br>
       {<br>
       TempSock = accept(Socket, NULL, NULL);<br>
        } <br>
 Socket = TempSock; </p>
<p>The reason for creating the TempSock variable is to preserve our real socket.
 I don't want to overwrite it with an error just because we missed a connection.
 I never looked into what is returned on a successful connection, I would assume
 it is the socket you started with, but from examples I looked at it doesn't
 appear to do that. All the documentation I read on accept() skipped over the
 return value, they just copied the results back into the original socket so
 I am doing the same. The second two parameters can be used to gain information
 on who connected by passing a pointer to a SOCKADDR structure and its size like
 this: </p>
<p>SOCKADDR Addr; <br>
 accept(Socket, &Addr, sizeof(Addr); </p>
<p>I never tested sending a SOCKADDR_IN the same as in bind() but I haven't tested
 this so I won't gaurentee the results of this.<br>
 So now we are listening on a TCP/IP port and ready to accept a connection. So
 lets look into requesting a connection. To do this we use the connect() function.
 This function takes the same parameters as the bind() function except the port
 and address are the ones you want to connect to instead of listen on obviously.
 The connect() function will return a 0 if successful. Here is an example of
 that:</p>
<p> //The variables we will need <br>
 SOCKADDR_IN SockAddr; </p>
<p>//We need a socket variable but for now lets assume its a variable Socket we
 prepared earlier. <br>
 //We want to use port 50 <br>
 SockAddr.sin_port = 50; </p>
<p>//We want an internet type connection (TCP/IP) <br>
 SockAddr.sin_family = AF_INET; <br>
 //We want to connect to the IP address 127.0.0.1 <br>
 //I'll give a few better ways to set this value later <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b1 = 127; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b2 = 0; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b3 = 0; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b1 = 1; </p>
<p>if (connect(Socket, (SOCKADDR *)(&SockAddr), sizeof(SockAddr)) != 0) <br>
       { <br>
       printf("Failed to establish connection with server.");<br>
       } </p>
<p>Now that we have a server with a connected client they need to exchange information.
 This is done exactly the same for the client as it is for the server. The functions
 to use are send() and recv(). They both take four parameters the socket to send
 on, the data to send, and the number of bytes in the data. The way they expect
 the data is in a pointer to a char. You can bundle other values into this just
 typecast it into a char * and pass the correct number of bytes. The fourth parameter
 isn't used so give a zero there. These functions will return the number of bytes
 send or received if successful. They will return 0, WSAECONNRESET, or WSAECONNABORT
 if the connection was closed at the other end. These functions will also return
 SOCKET_ERROR if some error occurs during the transmission. They recv() function,
 like the accept() function, only watches for a brief period for the data to
 come through. Once again I place the function in a while loop until data is
 received. Here is how the recv() function looks in such a loop: </p>
<p>int RetVal = SOCKET_ERROR; <br>
 char String[50]; </p>
<p>while (RetVal == SOCKET_ERROR) <br>
       {<br>
       RetVal = recv(Socket, String, 50, 0);<br>
       if ((RetVal == 0)||(RetVal == WSAECONNRESET)||(RetVal
 == WSAECONNABORT))<br>
            { <br>
            printf("Connection
 closed at other end."); <br>
            break; <br>
             }<br>
       } </p>
<p>Since errors are possible in sending the data I place it in a while loop as
 well. Here is how that looks: </p>
<p>int RetVal = SOCKET_ERROR; <br>
 char String[] = "Hello"; <br>
 while (RetVal == SOCKET_ERROR) <br>
       {<br>
       RetVal = recv(Socket, String, strlen(String)
 + 1, 0);<br>
       if ((RetVal == 0)||(RetVal == WSAECONNRESET)||(RetVal
 == WSAECONNABORT)) <br>
            {<br>
            printf("Connection
 closed at other end.");<br>
            break;<br>
            }<br>
       } </p>
<p>In these examples the data to send, or the received data is in the character
 array String. When the data is received there is a fixed amount of data that
 can be received so it is possible to overrun the buffer. That is a quick run
 through of how to use WinSock for network communications.</p>
<p> Now as I said before there are ways of determining your own network address.
 This is by calling gethostname(). This will not return your IP address, only
 the text computer name. This function takes two parameters a character array
 to place the computer name in and the number of characters you have allocated
 in that array. Here is how it looks: </p>
<p>char Name[255];<br>
 gethostname(Name, 255); </p>
<p>If you look at the example above you'll note that it uses the IP address, not
 the computer name. What you can do is to call gethostbyname() which will give
 you information about a host based on its name. It takes only one parameter,
 the string that has the computer name, and it returns a pointer to a HOSTENT
 structure. Here is an example: </p>
<p>HOSTENT *HostInfo;<br>
 HostInfo = gethostbyname("computer");<br>
 if (HostInfo == NULL) <br>
       {<br>
       printf("Attempt to retreive computer information
 failed.");<br>
       }</p>
<p> The gethostbyname() function will search through DNS records in order to find
 the IP address. The careful readers will note that this HOSTENT structure is
 still worthless since it doesn't fit into the SOCKADDR_IN anywhere. The IP address
 is in the HOSTENT structure, its just buried. Here are the members of the HOSTENT
 structure that I found useful. The h_addrtype member holds the type of address
 this uses, as with the sockets the only type is AF_INET. The h_name is a character
 array that will contain the complete host and domain name for that computer,
 for instance host.domain.com. One catch to this, it will not do reverse name
 lookups, for example if you look up the computer name "MyComputer" h_name will
 hold "MyComputer.MyDomain.com" , however if you look up the computer named "10.10.10.1"
 (which is really its IP) it will not translate that into a computer name gethostbyname()
 will just put the text "10.10.10.1" in h_name. The last member I want to discuss
 is h_addr_list, this one is somewhat confusing so of course it has the information
 we are really after. The member h_addr_list if a variable of type char**, but
 every time I have used it only one dimension of the array is used. In the data
 that is filled the first four bytes hold the four octets of the IP address.
 The rest of the array holds the same information as h_name. The octets are written
 as unsigned char values so you would have to place them into the SOCKADDR_IN
 structure like this: </p>
<p>SOCKADDR_IN SockAddr; <br>
 HOSTENT *HostInfo; <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b1 = (unsigned char)HostInfo->h_addr_list[0][0];
 <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b2 = (unsigned char)HostInfo->h_addr_list[0][1];
 <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b3 = (unsigned char)HostInfo->h_addr_list[0][2];
 <br>
 SockAddr.sin_addr.S_un.S_un_b.s_b4 = (unsigned char)HostInfo->h_addr_list[0][3];
</p>
<p>In that way you can use the computer's name to find its IP so you can connect
 to any server you have the name of. Using this same technique you can also find
 your own computers IP address. </p></font>
</body>
</html>

