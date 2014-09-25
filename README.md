Remote Control Server
===================
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/cxulo/RemoteControlServer?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

![Banner](http://remote-control-collection.com/files/posts/server_gui_advanced.png "Remote Control Collection")

### Description
The Remote Control Server is needed to receive commands from any [Remote Control App](http://remote-control-collection.com/). The Remote Control apps allow you to remotely control your PC from your smartphone or tablet. You can download the latest stable version here: [Server Releases](https://github.com/Steppschuh/RemoteControlServer/releases)

### For developers
If you are interested in improving this software, feel free to contribute your code. If you would like to port this software to Mac OS or Linux, please [contact me](http://steppschuh.net/?contact). Usage samples can be found below.

### License information
The copyright belongs to [Stephan Schultz](http://steppschuh.net/). More detailed license information can be found [in the project root](https://github.com/Steppschuh/RemoteControlServer/blob/master/LICENSE).




Usage
===================

### Sending a data package
 ```visualbasic
  Dim command As New Command
  command.source = Network.getServerIp()
  command.destination = Server.getCurrentApp().ip
  command.priority = Command.PRIORITY_MEDIUM
  command.data = Converter.stringToByte("Some fancy command!")
  command.send()
 ```
The above snippet will send a data package to the last connected device. The priority property lets the `Network` module decide which method it should use to send the data, based on the following table:

Priority | Protocol | Tries
--- | --- | ---
`PRIORITY_LOW` | UDP | 1
`PRIORITY_MEDIUM` | TCP | 1
`PRIORITY_HIGH` | TCP | 3
`PRIORITY_INDISPENSABLE` | TCP | Until received

### Receiving a data package
The `TCP` and `UDP` modules are constantly listening for incoming connections / data packages. If data was received and the source was authenticated, it will be processed as a `Command` object in the `Remote` module, where the used API will be detected. Depending on the API level, the command data will be parsed by the responsible `API` module. If it is a valid command, the server will execute it.

 ```visualbasic
  Dim command As Command = New Command
  command.source = remoteEndPoint.Address.ToString
  command.destination = localEndPoint.Address.ToString
  command.data = receivedData
  command.process()
 ```
The above snippet is part of the `listen()` methods of the `TCP` and `UDP` module and initiates the processing of a command.

### Simulating keyboard input
#### Pressing a key
To send a key up and down event for a single key use:
 ```visualbasic
  'Simulate Esc key press
  Keyboard.sendKeyPress(Keys.Escape)
  
  'The method above will call:
  Keyboard.sendKeyDown(ByVal key As Keys)
  Keyboard.sendKeyUp(ByVal key As Keys)
 ```
#### Sending a shortcut
To simulate pressing multiple keys, use:
 ```visualbasic
  'Send shortcut for Copy
  Dim shortcut = New List(Of Key) From {Keys.ControlKey, Keys.C}
  Keyboard.sendShortcut(shortcut)
 ```
#### Sending a string
If you want to press multiple keys after each other, use:
 ```visualbasic
  'Send Hello World
  Keyboard.sendEachKey("Hello World")
 ```
#### Sending a unicode character
If you need to send characters that have no `Key` representation, use:
 ```visualbasic
  'Send © (Copyright)
  'If you have the char
  Keyboard.sendUnicodeKeyPress("©")
  
  'If you have the unicode
  Keyboard.sendUnicodeKeyPress(169)
 ```
