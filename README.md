**ChatServer Code**

```import java.io.*;```
```import java.net.*;```
```import java.util.*;```

```public class ChatServer {```
    ```private static StringBuilder chatMessages = new StringBuilder();```

    public static void main(String[] args) throws IOException {
        int port = 8080; // Change this to desired port number
        ServerSocket serverSocket = new ServerSocket(port);
        System.out.println("Server listening on port " + port);

        while (true) {
            Socket clientSocket = serverSocket.accept();
            System.out.println("Client connected: " + clientSocket.getInetAddress());

            // Handle each client connection in a separate thread
            new Thread(() -> {
                try {
                    handleClient(clientSocket);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }

    private static void handleClient(Socket clientSocket) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
        PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);

        String request = in.readLine();
        System.out.println("Request received: " + request);

        // Parse request to get message and user
        String message = null;
        String user = null;
        StringTokenizer tokenizer = new StringTokenizer(request, "&");
        while (tokenizer.hasMoreTokens()) {
            String token = tokenizer.nextToken();
            if (token.startsWith("s=")) {
                message = token.substring(2);
            } else if (token.startsWith("user=")) {
                user = token.substring(5);
            }
        }

        // Add message to chat
        if (message != null && user != null) {
            chatMessages.append(user).append(": ").append(message).append("\n");
        }

        // Send response
        out.println(chatMessages.toString());

        // Clean up
        out.close();
        in.close();
        clientSocket.close();
    }
}
![image](ChatServer.jpg)

* The methods that are called in my code are the ```main``` method and the ``handleClient`` method. The ``main`` method makes the server listen for incoming connections. While the ```handleClient``` method reads the request from the client, gets the content of the message, and username, and sends the updated chat history as a response. The relevant arguments is the GET request to the ```/add-message``` endpoint with the parameters ```s=hello``` , ```user=John``` and ```s=How%20are%20you%3F``` , ```user=Alice```.
![SSHterminal](SSH_Terminal.jpg)
![SSH_Private](SSH_Private.jpg)
* Something I learned in lab from week 2 and 3, that I didn't know before, is what each part of a URL meant. This is because now I know that each part of a URL serves a different purpose, and how each part serves a signifincat purpose for the connection to the specified address everyone inputs when we use the internet. I also learned about SSH, which I didn't know anything about before, but now I know that it is related to secure communication and remote access.

