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
