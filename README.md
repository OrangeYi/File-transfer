# File-Transfer
## About
This is a server that facilitates a file exchange between clients.
A server and a client with the specifications given below.
Communication between client and server uses TCP.


## Details


### Communication
The data stream sent from the client to the server must adhere to the following format:
1 ASCII character: G (get = download), P (put = upload), or F (finish = termination)
8 ASCII characters (padded at the end by '\0'-characters, if necessary)
In case of an upload, the above 9-byte control information is immediately followed by the binary data stream of the file. In case of download, the server responds with the binary data stream of the file. When a client has completed a file upload, it closes the connection. Then the server closes the download connection to the other client.


### Server
Server program that can handle an arbitrary number of concurrent connections and file exchanges, only limited by system configuration or memory.
The server is started without any parameters and creates a TCP socket at an OS-assigned port.
It will print out the assigned port number, which is used when starting clients.
Both upload and download operations specify a key that is used to match clients with each other,
i.e., a client asking for downloading with a specific key receives the file that another client uploads using that key.
Files are not stored at the server, but instead clients wait for a match and then data is forwarded directly from the uploader to the downloader.
Multiple clients might specify the same key and operation. The server can always match a pair of uploader and downloader with the same key, but does not need to serve clients with the same key and operation in any particular order.







### Client Program
The client takes up to 6 parameters and can be invoked in 3 different ways:

1. terminate server: ./client \<host> \<port> F
2. download: ./client \<host> \<port> G\<key> <file name> \<recv size>
3. upload: ./client \<host> \<port> P\<key> \<file name> \<send size> \<wait time>

When requesting an upload or download, it reads data from or stores data to, respectively,
the file specified in the 4th parameter. When uploading and the 4th parameter is given as an integer number,
the number is taken as the virtual file size in bytes. In this case,
the sender application does not transmit the contents of an actual file,
but empty/random data equivalent to the virtual file size.

The 5th parameter specifies the size of the buffer that is transmitted during each
individual write/send or read/recv system call during the file transfer - except for the final data chunk that might be smaller.

When uploading a file, the 6th parameter specifies a wait time in milliseconds
between subsequent write/send system calls. This parameters allows for
a simple form of rate control that is important to test the concurrency of the server.
