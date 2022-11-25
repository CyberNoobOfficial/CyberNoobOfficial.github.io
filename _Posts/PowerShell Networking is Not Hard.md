**PowerShell Networking is not that hard!**

PowerShell as the name suggests is a pretty powerful language giving its users a lot of flexibility when writing scripts for Windows platforms. But that flexibility comes with a price " **The Syntax "** most programmers will find the syntax very confusing at first and even unreadable. I am no different, so this is why I decided to tackle my fear of PowerShell one post at a time.

This week we will be looking at how to create simple network communication scripts using PowerShell.

PowerShell has been supported on Microsoft Windows destitutions since the release of Windows 7 SP1 and Windows Server 2008R2.
 PowerShell is part of the .NET Framework which allows it to access many internal .NET classes with ease.

[The .NET Framework consists of classes that arrange into namespaces. The classes in a particular namespace are usually arranged a particular theme such as security, XML, input and output, directory services, protocols and other things.](https://devblogs.microsoft.com/scripting/use-net-framework-classes-to-explore-windows-powershell-processes/)

The namespace that we are going to look into today is the **Net** which provides a simple programming interface for many protocols. We will be playing with some basic classes under this namespace such as DNS, WebRequest, WebResponse, Sockets, and HTTP-specific classes.

**DNS**

[**DNS — ** Isa static class that retrieves information about a specific host from the Internet Domain Name System (DNS).](https://docs.microsoft.com/en-us/dotnet/api/system.net.dns?view=net-5.0)

To call a class inside the System Namespace, you need to put its name inside square brackets as such — 

PS C:\\> **[System.Net.Dns]**

IsPublic IsSerial Name BaseType

-------- -------- ---- --------

True False Dns System.Object

Also note that you don't have to use the "System" namespace because it's already assumed by PowerShell.

PS C:\\> **[Net.Dns]**

IsPublic IsSerial Name BaseType

-------- -------- ---- --------

True False Dns System.Object

You can find a full documentation of every class under the .Net namespace [here](https://docs.microsoft.com/en-us/dotnet/api/system.net?view=net-5.0). But if you want to print the different methods under each class you can use the **GetMembers()** method Also note that I used **Format-Table** to format the output as a table.

PS C:\\> **[Net.Dns].GetMembers() | Format-Table**

Name DeclaringType ReflectedType MemberType MetadataToken Module IsSecurityCritical

---- ------------- ------------- ---------- ------------- ------ ------------------

GetHostByName System.Net.Dns System.Net.Dns Method 100665229 System.dll True

GetHostName System.Net.Dns System.Net.Dns Method 100665235 System.dll True

GetHostByAddress System.Net.Dns System.Net.Dns Method 100665232 System.dll True

...

Ok let's play with some of the functions of the Dns class. To call a function inside our class we will use the " **::**" operator. We will call the **GetHostByName** function which as the name suggests returns the IP that is linked to a host name Using the DNS protocol.

**PS C:\\> [Net.Dns]::GetHostByName("www.google.com")**

HostName Aliases AddressList

-------- ------- -----------

www.google.com {} {172.217.21.4}

We can also do the opposite using the **GetHostByAddress()** function.

PS C:\\> [Net.Dns]::GetHostByAddress("157.240.1.35")

HostName Aliases AddressList

-------- ------- -----------

edge-star-mini-shv-01-lht6.facebook.com {} {157.240.1.35}

Pretty simple isn't it ? Let try and do more complex stuff .

**WebRequest & WebResponse Classes**

**WebRequest — ** [Makes a request to a Uniform Resource Identifier (URI). This is an](https://docs.microsoft.com/en-us/dotnet/api/system.net.webrequest?view=net-5.0)[abstract](https://docs.microsoft.com/en-us/dotnet/api/system.net.webrequest?view=net-5.0)[class.](https://docs.microsoft.com/en-us/dotnet/api/system.net.webrequest?view=net-5.0)
**WebResponse**   **—** [** ** Provides a response from a Uniform Resource Identifier (URI). This is an abstract class.](https://docs.microsoft.com/en-us/dotnet/api/system.net.webresponse?view=net-5.0)
**HttpWebResponse —** [** ** Provides an HTTP-specific implementation of the WebResponse class.](https://docs.microsoft.com/en-us/dotnet/api/system.net.httpwebresponse?view=net-5.0)

We will be using WebRequest create a Get request to google.com and HttpWebResponse(Subclass of WebResponse) to print the returned HTML.

The first thing we will do is to check if the WebRequest worked. We will do that by examining the status code of the HttpWebResponse.

# First thing we do is creating the WebRequest Object.

$request = [Net.WebRequest]::Create("http://www.google.com")

# From that WebRequest we can create our HttpWebResponse Object.

$response = [Net.HttpWebResponse] $request.GetResponse()

# Now we will get the HTTP Response status code.

Write-host($response.StatusCode.value\_\_)

Now that we know that it works lets try and read the data. To do that we need to create a data stream. The stream contains the body of the response.

[Stream is the abstract base class of all streams. A stream is an abstraction of a sequence of bytes, such as a file, an input/output device, an inter-process communication pipe, or a TCP/IP socket. The Stream class and its derived classes provide a generic view of these different types of input and output, and isolate the programmer from the specific details of the operating system and the underlying devices.](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream?view=net-5.0)

Another thing we will need to do is to create a Stream reader which will read the data from the stream.

**# \<Add the above code\>**

# Create a data stream.

$datastream = [IO.Stream]$response.GetResponseStream()

# Create stream reader.

$streamreader = [IO.StreamReader]::new($datastream)

# Extract the content.

$responseContent = $streamreader.ReadToEnd()

# Printing the response to the user.

Write-host($responseContent)

The Stream object is not used only by the .Net namespace this is why it part of the IO namespace insted.

**HttpListener**

Now that we know how to query http sites lets create simple http server in PowerShell it can be used for numerus of things but we will just make a simple implementation.

To do that we will use the **HttpListener —** [ is a class which Provides a simple, programmatically controlled HTTP protocol listener. This class cannot be inherited.](https://docs.microsoft.com/en-us/dotnet/api/system.net.httplistener?view=net-5.0)

It is quite simple to create an HttpLisitnet Object. All we have to do is create the Httplisitner Object using the **new()** function and [prefixes](https://docs.microsoft.com/en-us/dotnet/api/system.net.httplistener.prefixes?view=net-5.0)which are URI prefixes handled by the HttpLisitner. We start the server by calling the **start()** function **.**

# First we create an instance of HttpListener Class.

$httplisitner = [System.Net.HttpListener]::new()

# Then we add prefixes (We can add serval).

$httplisitner.Prefixes.Add("http://127.0.0.1:8080/")
 $httplisitner.Prefixes.Add("http://127.0.0.1:7070/")

# Then we start the server.

$httplisitner.Start()

write-host ("Listening ...")

After we created the server we can listen for incoming requests. We will retrieve the content of the Http request and use it to respond to the user.

# Wait for incoming requests.

while ($httplisitner.IsListening) {

# A class that provides access to Request and Response Objects.

$context = $httplisitner.GetContext()

# User Request object.

$request = $context.Request

# Server Response object.

$response = $context.Response

We create a while loop that waits for incoming requests from the user. The [**Context**](https://docs.microsoft.com/en-us/dotnet/api/system.net.httplistenercontext?view=net-5.0)object provides access to the request and response objects used by the HttpListener class.
 We will use the request object to retrieve information about the user's http GET request and the response object to return an http response.

# Response for 127.0.0.1:8080

if ($request.HttpMethod -eq 'GET' -and $request.RawUrl -eq '/') {

# The response HTML returned to the user.

$responseString = "\<HTML\>\<BODY\> Hello world!\</BODY\>\</HTML\>";

# Convertting HTML to Bytes.

$buffer = [System.Text.Encoding]::UTF8.GetBytes($responseString);

# Getting the Buffer Length.

$context.Response.ContentLength64 = $buffer.Length

# Send Response To User.

$context.Response.OutputStream.Write($buffer, 0, $buffer.Length)

# We have to close the reponse Object.

$context.Response.OutputStream.Close()

write-host("Send Hello World")

}

The above code will respond with "Hello World!" when the user access http://[127.0.0.1:8080](http://127.0.0.1:8080/)

First we check the value of the $request.HttpMethod and the $request.RawUrl. In our case we wait for Http Get request with the URL value of none or '/'.

Then we create the Servers response with some basic HTML which we convert to bytes using **[System.Text.Encoding]::UTF8.GetBytes().**

After that we can stream the response to the user using **$context.Response.OutputStream.Write()** this function enables binary output to the outgoing HTTP content body.

In the end we close the OutputStream object using **$context.Response.OutputStream.Close(). **

And that's it we successfully sent and HTML response the user.

# Response for 127.0.0.1:8080/kill

if ($request.HttpMethod -eq 'GET' -and $request.RawUrl -eq '/kill')
 {

# Stopping the server.

$httplisitner.stop()

write-host("Lisitner Sttoped")

}

} # Close The while

One problem the you might face is that you can't stop the HttpListiner without closing the PowerShell terminal. To overcome this I've added a /kill route that when accessed by the the user will stop the HttpListiner using the stop() function. (It's not the best solution but it will work for now.)

**Sockets**

For the last part of this tutorial I would like to approach a more basic element of networking **Sockets.**

[**Sockets — ** Provides a managed implementation of the Windows Sockets (Winsock) interface for developers who need to tightly control access to the network.](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.socket?view=net-5.0)

We will be creating a simple Command and Control Server. A command and control server is often used by attackers to execute OS commands on victim host machines.

**Server**

Again I will be splitting the code so it would be easier to explain the different prats. We will start by setting up server.

# Set values.

$serverPort = 13000
 $serverIpaddress = [System.Net.IPAddress]::Loopback #Just getting the loopback Address 127.0.0.1
 $run = $true

# Create new TcpLisitener.
 $server = [System.Net.Sockets.TcpListener]::new($serverIpaddress, $serverPort)

# Start listening for client requests.
 $server.Start()

# Accept connection requests.
 $tcpClient = $server.AcceptTcpClient()

We start by initializing some variables that will be used later in the code — 

**$serverPort — ** The port which the server is listening on.
**$serverIpaddress — ** The servers IP address, in our case we use the Loopback address 127.0.0.1.
**$run — ** This value tells the server when to stop it's execution.

We will use the **$serverPort** and **$serverIpaddress** to create a new [TcpLisitener](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcplistener?view=net-5.0)( **$server** ) which listens for incoming TCP connections. To start listening we will call the **start()** function.

To listen for incoming requests we will use [**AcceptTcpClient**](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcplistener.accepttcpclient?view=net-5.0)**()** function which is a blocking which means the code will stop executing untill a client connects to the server. This function return a [**TcpClient**](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient?view=net-5.0)object which we will use for communication with the client.

# Run until user ask to stop.
 while($run)
 {

# Clear variables.
 $cmd = $null
 $buffer = [System.Byte[]]::new(2048)
 $cmdResult =$null

# Get Stream Object.
 $stream = $tcpClient.GetStream()

# Read bytes.
 $stream.Read($buffer, 0, $buffer.Length)
 $cmd = [System.Text.Encoding]::ASCII.GetString($buffer)

# Remove nulls.
 $cmd = $cmd -replace "\0", ""

# Run user's command
 try{
 # Use Invoke-Expression to convert String to OS Command.
 $cmdResult = Invoke-Expression($cmd)
 }
 catch{
 $cmdResult = "Not an OS command"
 }
 write-host($cmd)
 if($cmdResult -ne $null)
 {
 # Convert Response to bytes and send to user.
 $response = [System.Text.Encoding]::ASCII.GetBytes($cmdResult)
 $stream.Write($response, 0, $response.Length)
 }
 else
 {
 $response = [System.Text.Encoding]::ASCII.GetBytes("Command ran successfully")
 $stream.Write($response, 0, $response.Length)
 }

if($cmd -eq "stop"){$run = $false}

}

# Gracefully close everything.
 $stream.Close()
 $tcpClient.Close()
 $server.Stop()

 This while loop is a bit long so lets break it to it's basic components — 

# Clear variables.
 $cmd = $null
 $cmdResult = $null
 $buffer = [System.Byte[]]::new(2048)

The first thing we do is initializing the variables that we'll be using every time we get a message from the user.

**$cmd — ** Is the command sent by the user.
**$cmdResult — ** Is the result of running the OS command.
**$buffer — ** Is where we will store the bytes sent by the client.

# Get Stream Object.
 $stream = $tcpClient.GetStream()

# Read bytes.
 $stream.Read($buffer, 0, $buffer.Length)
 $cmd = [System.Text.Encoding]::ASCII.GetString($buffer)

# Remove nulls.
 $cmd = $cmd -replace "\0", ""

The next thing we'll do is create the [Stream](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.networkstream?view=net-5.0)object of out TcpClient Object. The stream object allows us to read the data sent by the client and write data to the client.

The data will be read into out buffer($buffer) using the Read function. As you remember the data is sent in the form of bytes, so we'll convert it to String using the **GetString** function.
 Because our buffer has a lot of Null terminating strings(\0) at the end of it, we will remove them using the replace keyword — 
**$cmd = $cmd -replace "\0", "" **.

# Run user's command
 try{
 # Use Invoke-Expression to convert String to OS Command.
 $cmdResult = Invoke-Expression($cmd)
 }
 catch{
 $cmdResult = "Not an OS command"
 }
 write-host($cmd)

After parsing the command we can now run it on the host machine. To do that we'll use the **Invoke-Expression** which will run our $cmd as an OS command.
 If the operation is not a valid cmdlet we will tell the user that by sending **"Not an OS command".**
Either way we will keep the value of the operation in **$cmdResult**.

# Convert Response to bytes and send to user.
 $response = [System.Text.Encoding]::ASCII.GetBytes($cmdResult)
 $stream.Write($response, 0, $response.Length)
 }
 else
 {
 $response = [System.Text.Encoding]::ASCII.GetBytes("Command ran successfully")
 $stream.Write($response, 0, $response.Length)
 }

If the operation returns output we would like to send it back to the client. We will do that by first converting the result to bytes using the **GetBytes** function, and then sending it back to the user using the **write** function.

If there's not output we will just send **"Command ran successfully"**.

if($cmd -eq "stop"){$run = $false}

If the user sent **stop** we will exit our program and close the connection.

# Gracefully close everything.
 $stream.Close()
 $tcpClient.Close()
 $server.Stop()

We will do that by first closing the Stream object, then the TcpClient object and finally we will ask the server to **stop** listening.

**Client**

# Client

# Set values.
 $serverPort = 13000
 $serverIpaddress = [System.Net.IPAddress]::Loopback #Just getting the loopback Address 127.0.0.1
 $run = $true

# Create tcp Client
 $tcpClient = [System.Net.Sockets.TcpClient]::new($serverIpaddress, $serverPort)

# Run until user ask to stop.
 while($run)
 {

# Create buffer.
 $buffer = [System.Byte[]]::new(2048)

# Convert to bytes.
 $message = Read-Host("Enter Line ")
 $cmd = [System.Text.Encoding]::ASCII.GetBytes($message)

# Get client stream for reading and writing.
 $stream = $tcpClient.GetStream()

# Send message to server
 $stream.Write($cmd,0, $cmd.Length)

# Read Servers Response.
 $stream.Read($buffer, 0, $buffer.Length)
 $response = [System.Text.Encoding]::ASCII.GetString($buffer)
 $response = $response -replace "\0", ""
 write-host($response)

if($message -eq "stop"){$run = $false}

}
 $stream.Close()
 $tcpClient.Close()

Creating the client is very much similar to creating the server. The main difference is that we connect to the server, so we don't need to listen on a port.

As you can see we get the user's input using the **Read-Host()** function. This input we send to server. We will print the result of that input to the client and ask for more instructions.

Everything else is pretty much the same, so there is no need to break it down.

**Conclusion**

In this basic tutorial we learned a few things.

- PowerShell is part of the .NET Framework which means it has access to many internal .NET classes and functions.
- We learned how to access these classes.
- We learned how to use the DNS class.
- We learned how to work with **WebRequest** & **WebResponse** classes.
- And we learned how to work with Sockets.

There is much more that can be done using System.Net but I hope this short tutorial is will make it easier at least at the begging.
