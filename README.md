# SpringBoot-ChatApplication
Creating a Spring Boot chat application using WebSocket implementation involves several steps. I'll provide you with a high-level overview of the process and then guide you through each step with code snippets and instructions.

**** Given code is for guiding purpose not implemented in the file*********
**Step 1: Set Up Your Spring Boot Project**

You can use Spring Initializr to set up your Spring Boot project with the necessary dependencies for WebSocket and web development. Follow these steps:

1. Go to the Spring Initializr website: [https://start.spring.io/](https://start.spring.io/)
2. Choose your project settings, such as project type (Maven or Gradle), language (Java or Kotlin), and Spring Boot version.
3. Add dependencies: Search for "WebSocket" and select "Spring Websocket" and "Thymeleaf" (a template engine for HTML).
4. Click the "Generate" button to download the project zip file.

**Step 2: Create WebSocket Configuration**

In your Spring Boot project, create a WebSocket configuration class. This class will configure the WebSocket endpoints and message broker. Create a Java class like this:

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer;

@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry config) {
        config.enableSimpleBroker("/topic");
        config.setApplicationDestinationPrefixes("/app");
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/chat").withSockJS();
    }
}
```

This configuration sets up a WebSocket message broker with a destination prefix of "/topic" and an application destination prefix of "/app". It also registers a "/chat" endpoint for WebSocket communication.

**Step 3: Create a Controller**

Create a Spring MVC controller to handle HTTP requests and WebSocket messages. Here's a basic example:

```java
import org.springframework.messaging.handler.annotation.MessageMapping;
import org.springframework.messaging.handler.annotation.SendTo;
import org.springframework.stereotype.Controller;

@Controller
public class ChatController {

    @MessageMapping("/chatMessage")
    @SendTo("/topic/public")
    public ChatMessage send(ChatMessage chatMessage) {
        return chatMessage;
    }
}
```

This controller handles messages sent to "/app/chatMessage" and broadcasts them to all subscribed clients on the "/topic/public" channel.

**Step 4: Create a Chat Message Model**

Create a simple model to represent chat messages:

```java
public class ChatMessage {

    private String content;
    private String sender;

    // getters and setters
}
```

**Step 5: Create HTML Templates**

Create HTML templates for the chat application using Thymeleaf or your preferred template engine. These templates will be used to display the chat interface. Here's a basic example of an HTML template:

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>WebSocket Chat</title>
</head>
<body>
    <div id="chatDiv">
        <ul id="chatMessages"></ul>
        <input type="text" id="messageInput" placeholder="Type your message here..." />
        <button id="sendButton">Send</button>
    </div>
    <script src="/webjars/sockjs-client/sockjs.min.js"></script>
    <script src="/webjars/stomp-websocket/stomp.min.js"></script>
    <script src="/js/chat.js"></script>
</body>
</html>
```

**Step 6: Create JavaScript for WebSocket Client**

Create a JavaScript file (e.g., chat.js) to handle WebSocket communication on the client side. Here's a basic example:

```javascript
var stompClient = null;

function connect() {
    var socket = new SockJS('/chat');
    stompClient = Stomp.over(socket);
    stompClient.connect({}, function (frame) {
        stompClient.subscribe('/topic/public', function (message) {
            showMessage(JSON.parse(message.body).content);
        });
    });
}

function sendMessage() {
    var messageInput = document.getElementById('messageInput');
    stompClient.send("/app/chatMessage", {}, JSON.stringify({
        'content': messageInput.value
    }));
    messageInput.value = '';
}

function showMessage(message) {
    var chatMessages = document.getElementById('chatMessages');
    var messageLi = document.createElement('li');
    messageLi.textContent = message;
    chatMessages.appendChild(messageLi);
}

document.getElementById('sendButton').addEventListener('click', sendMessage);
window.onload = connect;
```

**Step 7: Run Your Application**

Now, you can run your Spring Boot application, and it should provide a basic chat interface with WebSocket functionality.

This is a simplified example of a Spring Boot chat application using WebSocket. Depending on your specific requirements, you can enhance the application with user authentication, message history, and other features. Additionally, you may want to consider using a front-end framework like React or Angular for a more interactive user interface.

Remember to manage your project dependencies using Maven or Gradle, and ensure you have the required webjars for SockJS and Stomp.js in your project.
