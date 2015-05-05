

# Introduction #
Project contains compiled libraries (in demos projects for Windows and Linux)

# Objects #
There are 3 types ob object available:
<dl>
<blockquote><dt>server</dt>
<dd>Server listening for incoming connections.</dd>
<dt>server connection</dt>
<dd>WebSocket connection on server side.</dd>
<dt>client connection</dt>
<dd>WebSocket connection on client side.</dd>
</dl></blockquote>

If you want to create WebSocket server, you will work with **server** and **server connection** objects. Each server is than have it's own list of server connections.

If you want to create WebSocket connection to connect to remote server, you will work with **client connection** objects.

Every object has it's own index. The index is incrementing globally = the object index is unique across all types of objects.


# Types #

```
  {:callback for WebSocket connection event, triggered when data are recevied
    @param integer Connection Connection index
    @param pChar aData Received data
  }
  TConnectionDataLib = procedure(Connection: integer; aData: pChar); stdcall;

  {:callback for WebSocket connection custom events 
    @param integer Connection Connection index
  }
  TConnectionEventLib = procedure(Connection: Integer); stdcall;



  {:callback trigger after connection is added to server 
    @param integer Server Server index 
    @param integer aConnection Connection index
  }
  TServerAfterAddConnectionLib = procedure (Server: integer; aConnection: integer); stdcall;

  {:callback trigger before connection is added to server
    @param integer Server Server index 
    @param integer aConnection Connection index
    @param bool CanAdd If true, connection can be added. If false, connection is closed
  }
  TServerBeforeAddConnectionLib = procedure (Server: integer; aConnection: integer; var CanAdd: boolean); stdcall;

  {:callback trigger after connection is removed from server
    @param integer Server Server index 
    @param integer aConnection Connection index
  }
  TServerAfterRemoveConnectionLib = procedure (Server: integer; aConnection: integer); stdcall;

  {:callback trigger before connection is removed from server 
    @param integer Server Server index 
    @param integer aConnection Connection index
  }
  TServerBeforeRemoveConnectionLib = procedure (Server: integer; aConnection: integer); stdcall;

  {:callback trigger on server''s socker error
    @param integer Server Server index 
    @param integer Error Socket error
    @param pChar ErrorDescr Socket error description
  }
  TServerSocketErrorLib = procedure (Server: integer; Error: integer; ErrorDescr: pChar); stdcall;



  {:Function to initialize library for establishing client connections
    @param TConnectionEventLib aCcc Callback triggered after connection is connected to server 
      (but such connection can be refused, event represents only regular socket connection)
    @param TConnectionDataLib aCcd Callback triggered after connection receive data
    @param TConnectionEventLib aCcx Callback triggered after connection is disconnected from server
    @param TConnectionEventLib aCch Callback triggered after successful handshake (connection accepted by server)
    @param TConnectionEventLib aCcr Callback triggered when connection is receiving data
    @param TConnectionEventLib aCcs Callback triggered when connection is sending data
    @param TConnectionDataLib aCci Callback triggered when connection information are requested (see below)
  }
  TLibraryInitClient = procedure(
    aCcc: TConnectionEventLib;
    aCcd: TConnectionDataLib; 
    aCcx: TConnectionEventLib;
    aCch: TConnectionEventLib;
    aCcr: TConnectionEventLib;
    aCcs: TConnectionEventLib;
    aCci: TConnectionDataLib 

  ); stdcall;

  {:Function to initialize library for establishing server and receiving server connections
    @param TServerAfterAddConnectionLib aSaac See callback type description
    @param TServerBeforeAddConnectionLib aSbac See callback type description
    @param TServerAfterRemoveConnectionLib aSarc See callback type description
    @param TServerBeforeRemoveConnectionLibaSbrc See callback type description
    @param TServerSocketErrorLib aSse See callback type description


    @param TConnectionDataLib aScd Callback triggered after connection receive data
    @param TConnectionEventLib aSch Callback triggered after successful handshake (connection accepted by server)
    @param TConnectionEventLib aScr Callback triggered when connection is receiving data
    @param TConnectionEventLib aScs Callback triggered when connection is sending data
    @param TConnectionDataLib aSci Callback triggered when connection information are requested (see below)
  }
  TLibraryInitServer = procedure(
    aSaac: TServerAfterAddConnectionLib;
    aSbac: TServerBeforeAddConnectionLib;
    aSarc: TServerAfterRemoveConnectionLib;
    aSbrc: TServerBeforeRemoveConnectionLib;
    aSse: TServerSocketErrorLib;

    aScd: TConnectionDataLib; //server connection ondata
    aSch: TConnectionEventLib; //server connection onhandshake
    aScr: TConnectionEventLib; //server connection onreaddata
    aScs: TConnectionEventLib; //server connection onsenddata
    aSci: TConnectionDataLib //server connection info
  ); stdcall;

  {:library close function}
  TLibraryClose = procedure;



  {:library function to broadcast data to all server connections
    @param integer aIndex Server index
    @param pChar aData Data to be sent
    @return bool True if server exists
  }
  TServerBroadcast = function (aIndex: integer; aData: pChar): boolean; stdcall;

  {:library function to stop and destroy server
    @param integer aIndex Server index
    @return bool True if server exists
  }
  TServerClose = function(aIndex: integer): boolean; stdcall;

  {:library function to create server
    @param pChar aBind IP address server should be listening on (use 0.0.0.0 to listen on default IP address)
    @param pChar aPort Port server should be listening on
    @param integer aMaxConnections Maximum number of simultaneously opened connection 
      (default value is -1 = unlimited number)
    @return integer Server index
  }
  TServerCreate = function(aBind: pChar; aPort: pChar; aMaxConnections: integer): integer; stdcall;

  {:library function start server
    @param integer aIndex Server index
    @return bool True if server exists
  }
  TStartServer = function (aIndex: integer): boolean; stdcall;



  {:library function to close server connection
    @param integer aIndex Connection index
    @return bool True if connection exists
  }
  TServerConnectionClose = function(aIndex: integer): boolean; stdcall;

  {:library function to get server connection information (see below)
    @param integer aIndex Connection index
    @return bool True if connection exists
  }
  TServerConnectionInfo = function(aIndex: integer): boolean; stdcall;

  {:library function to send data via server connection
    @param integer aIndex Connection index
    @param pChar aIndex aData data to be sent
    @return bool True if connection exists
  }
  TServerConnectionSendData = function(aIndex: integer; aData: pChar): boolean; stdcall;



  {:library function to close client connection
    @param integer aIndex Connection index
    @return bool True if connection exists
  }
  TClientConnectionClose = function(aIndex: integer): boolean; stdcall;

  {:library function to get client connection information (see below)
    @param integer aIndex Connection index
    @return bool True if connection exists
  }
  TClientConnectionInfo = function(aIndex: integer): boolean; stdcall;

  {:library function to open connection to server
    @param pChar aOrigin String identifying client (IP, URL, etc.)
    @param pChar aHost Host (server) to connect to (IP, domain name)
    @param pChar aPort Server port to connect to
    @param pChar aResource Requested resource 
      (as URL path, e.g. /dir/file.ext, use / for direct connection. Parameter has no particular meaning, 
       server must be programmed to understand what to do with requested resource)
    @param pChar aProtocol See WebSocket specification (pass empty if no protocol requested)
    @param integer aWebSocketType WebSocket type, see below
    @return integer Connection index
  }
  TClientConnectionOpen = function(aOrigin, aHost, aPort, aResource, aProtocol: pChar; 
     aWebSocketType: integer): integer; stdcall;

  {:library function to send data via server connection
    @param integer aIndex Connection index
    @param pChar aIndex aData data to be sent
    @return bool True if connection exists
  }
  TClientConnectionSendData = function(aIndex: integer; aData: pChar): boolean; stdcall;

```

# WebSocket types #
Library supports 2 types of WebSocket types (specifications).
  * [Hixie 75](http://tools.ietf.org/html/draft-hixie-thewebsocketprotocol-75)
  * [Hixie 76](http://tools.ietf.org/html/draft-hixie-thewebsocketprotocol-76)
Server can accept both. When establishing client connection, type is passed as parameter.

  * Hixie 75 is represented as **wsHixie75** string or **1** as integer
  * Hixie 76 is represented as **wsHixie76** string or **2** as integer

# Connection information #

## Getting information ##
To get information about connection, you should pass callback to initialization function and then, when you want to actually get those information, call library exported function for getting those information. Data will be passed back to you via initialization callback.

## Information data ##
Data are returned via callback as string. This string contain lines (separated by CRLF) with actual data

### Examples ###
```
SERVER CONNECTION INFORMATION STRING:
handshake=true
host=10.0.0.1
index=1
location=ws://10.0.0.1:10001/
origin=clientlibdemo
port=10001
protocol=
resource=/
websockettype=wshixie76
server=0

CLIENT CONNECTION INFORMATION STRING:
handshake=true
host=10.0.0.1
index=0
location=ws://10.0.0.1:10001/
origin=clientlibdemo
port=10001
protocol=
resource=/
websockettype=wshixie76
```

Each line contains field name, equal sign and field value.

Fields are:
<dl>
<blockquote><dt>handshake</dt><dd>whether handshake was successfull</dd>
<dt>host</dt><dd>host where client is connected to, or server is listening on</dd>
<dt>index</dt><dd>connection index</dd>
<dt>location</dt><dd>full location (as URL: host + resource)</dd>
<dt>origin</dt><dd>origin passed from client</dd>
<dt>port</dt><dd>server port</dd>
<dt>protocol</dt><dd>protocol passed from client</dd>
<dt>resource</dt><dd>resource passed from client</dd>
<dt>websockettype</dt><dd>WebSocket type</dd>
<dt>server</dt><dd>Server index (only in server connection)</dd>
</dl></blockquote>

# Exported functions #
  * LibraryClose: TLibraryClose
  * LibraryInitClient: TLibraryInitClient
  * LibraryInitServer: TLibraryInitServer
  * ServerBroadcast: TServerBroadcast
  * ServerClose: TServerClose
  * ServerCreate: TServerCreate
  * ServerStart: TStartServer
  * ServerConnectionClose: TServerConnectionClose
  * ServerConnectionInformations: TServerConnectionInfo
  * ServerConnectionSendData: TServerConnectionSendData
  * ClientConnectionClose: TClientConnectionClose
  * ClientConnectionInformations: TClientConnectionInfo
  * ClientConnectionOpen: TClientConnectionOpen
  * ClientConnectionSendData: TClientConnectionSendData

Function type represents function interface (parameters)

# Notice #
Server and connections are working in threads. Library itself has no GUI so there is no synchronization on library side. Client should create its own GUI synchronization. Demo projects do not contain such synchronization!

# Request #
It would be nice if anyone, who can rewrite library interface to another language (C, C++, etc.) would send it to me so I can publish it here.