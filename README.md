To **create** a simple real-time chat-app using python sockets with tkinter, I am going to **create** 2 python files namely server.py and client.py

# server.py:

>         from socket import *
>         
>         from threading import *

- This **imports** the necessary modules. The **socket** module provides a way for the program to communicate over the network, and the **threading** module is used for creating and managing threads.

>         clients = set()

- This **creates** an empty set called clients. The purpose of this set is to keep track of connected clients.

>         def client_Thread(client_Socket, client_Address):
>         
>                 while True:
>                 
>                         msg = client_Socket.recv(1024).decode("utf-8")
>                         
>                         print(f"{str(client_Address[1])}: {msg}")
        
- This **defines** a function called client_Thread that will be executed in a separate thread for each connected client. It **runs** in an infinite loop, **receiving** messages (up to 1024 bytes) from the client socket and **printing** them along with the client's port number.

>                         for client in clients:
>                                 
>                                 if client is not client_Socket:
>                                         
>                                         client.send((str(client_Address[1]) + ": " + msg).encode("utf-8"))
                
- For each client in the clients set (excluding the sender), it **sends** the received message to all other connected clients.

>                         if not msg:
>                         
>                                 clients.remove(client_Socket)
>                                 
>                                 print(f"{client_Address[0]}: {str(client_Address[1])} disconnected")
>                                 
>                                 break
            
- **If** the received message is empty, it means the client has disconnected. The client socket is **removed** from the clients set, and a message indicating the disconnection is **printed**. The loop is then **broken**, and the thread is **terminated**.

>                 client_Socket.close()
    
- After the loop breaks, the client socket is **closed**.

>         host_Socket = socket(AF_INET, SOCK_STREAM)
>                 
>         host_Socket.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)

- A **new** socket (host_Socket) is created using the IPv4 address family (AF_INET) and the TCP socket type (SOCK_STREAM). The setsockopt method is used to **set** the SO_REUSEADDR option, allowing the **reuse** of the local address.

>         host_Socket.bind(("192.168.100.143", 7500))

- The socket is **bound** to the specified IP address (192.168.100.143) and port number (7500).

>         host_Socket.listen()
>         
>         print("Waiting for connection...")

- The socket is put into **listening** mode, and a message is **printed** indicating that the server is waiting for connections

>         while True:
> 
>                 client_Socket, client_Address = host_Socket.accept()
> 
>                 clients.add(client_Socket)
> 
>                 print(f"Connection established with: {str(client_Address[1])}")
> 
>                 thread = Thread(target=client_Thread, args=(client_Socket, client_Address,))
> 
>                 thread.start()
    
- This is the main loop that **accepts** incoming connections. When a client connects, a new client socket and address are **obtained** from the accept method. The client socket is **added** to the clients set, and a message indicating the connection is **printed**. A new thread is **created** to handle the communication with the client, and it starts by **calling** the client_Thread function. This allows the server to **handle** multiple clients concurrently.

# client.py:

>         from socket import *
>         
>         from threading import *
>         
>         from tkinter import *

- This **imports** the necessary modules: **socket** for network communication, **threading** for creating threads, and **tkinter** for creating a graphical user interface.

>         client_Socket = socket(AF_INET, SOCK_STREAM)
>         
>         client_Socket.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)

- A client socket (client_Socket) is **created** using the IPv4 address family (AF_INET) and the TCP socket type (SOCK_STREAM). The setsockopt method is used to **set** the SO_REUSEADDR option, allowing the **reuse** of the local address.

>         client_Socket.connect(("192.168.100.143", 7500))

- The client socket **connects** to the specified server address (192.168.100.143) and port number (7500).

>         chat = Tk()
>         
>         chat.title("Simple Real-Time Chat-App using Python Sockets")

- An instance of the Tkinter Tk class is created, **representing** the main window of the GUI application. The title of the window is **set**.

>         msg = Text(chat, width=50)
>         
>         msg.grid(row=0, column=0, padx=10, pady=10)

- A Text widget is created for **displaying** the chat messages. It is **placed** in the first row and column of the grid layout with some padding.

>         txtMsg = Entry(chat, width=50)
>         
>         txtMsg.grid(row=1, column=0, padx=10, pady=10)

- An Entry widget is created for **entering** text messages. It is **placed** in the second row of the grid layout.

>         def sendMsg():
>         
>                 clientmsg = txtMsg.get()
>                 
>                 msg.insert(END, f"\nYou: {clientmsg}")
>                 
>                 client_Socket.send(clientmsg.encode("utf-8"))
    
- The sendMsg function is **defined**. It **retrieves** the text entered in the txtMsg widget, **inserts** it into the msg widget, and **sends** it to the server using the client_Socket.

>         btnsendmsg = Button(chat, text="Send", width=20, command=sendMsg)
>         
>         btnsendmsg.grid(row=2, column=0, padx=10, pady=10)

- A Button widget is created for **sending** messages. It is **placed** in the third row of the grid layout, and the sendMsg function is **set** as the command to be **executed** when the button is clicked.

>         def receiveMsg():
>         
>                 while True:
>                         
>                         serverMsg = client_Socket.recv(1024).decode("utf-8")
>                         
>                         msg.insert(END, f"\n{serverMsg}")
        
- The receiveMsg function is **defined**. It **runs** in an infinite loop, continuously **receiving** messages from the server and inserting them into the msg widget.

>         receiveThread = Thread(target=receiveMsg)
>         
>         receiveThread.daemon = True
>         
>         receiveThread.start()

- A new thread (receiveThread) is **created** with the receiveMsg function as the target. It is **set** as a daemon thread, meaning it will automatically exit when the main program exits. The thread is then **started**.

>         chat.mainloop()

- The Tkinter main loop is **started**, allowing the GUI to be responsive and **handle** user interactions. The program continues to **run** until the user **closes** the Tkinter window.
