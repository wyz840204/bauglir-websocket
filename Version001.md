Check [WebNt.eu WebSocket](http://www.webnt.eu) pages with full documentation.

## Implementations ##
  * Pascal client and server implementation (source code documented). Support for non-secure and secure (SSL) connection.
  * PHP server implementation (source code documented)

## Binaries ##
Implementation and compiled binaries for usage are in Delphi (websocketlib.dll) and Lazarus (websocketlib.so) demos.

There is Wiki page with [interface description](http://code.google.com/p/bauglir-websocket/wiki/Libraries_1_1_0)

## Demos ##
  * Pascal (Lazarus and Delphi) client and server demos
    * source code and projects compiled for Windows XP and Linux (Kubuntu) - based directly on Pascal sources
    * source code and projects compiled for Windows XP and Linux (Kubuntu) - based on libraries (dll, so)
  * C# client and server demos
    * source code and projects compiled for Windows XP - based on library (dll). Should be easily portable to linux.
  * HTML5 web pages for WebSocket compatible browsers for testing Pascal demo from browser.
  * PHP server implementing request/response communication with HTML5 web page for testing
  * PHP server implementing broadcasts to all connected clients with HTML5 web page for testing

## Note ##
  * When creating console application directly in Pascal, take notice of **BauglirSynchronizeThreads** variable in **BClasses** unit. This variable is responsible for synchronizing to main GUI thread, since console application does not have thread, than in case your main console thread is blocking, you should set this variable to `true`. Variable is automatically set to `true` in libraries. When using library, application should be responsible for threads synchronizing (simultaneous resource access)