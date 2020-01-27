# Lesson 4 - Writing Web Socket Views

## STEP1 - Add consumer
- consumer accepts websocket connections
- consumer is like a view/APIView in views.py but for web sockets

    **Synchronous channel setup**
    // APP_NAME/consumers.py
    ```
    from channels.generic.websocket import WebsocketConsumer
    import json

    class ChatConsumer(WebsocketConsumer):
        def connect(self):
            self.accept()

        def disconnect(self, close_code):
            pass

        def receive(self, text_data):
            text_data_json = json.loads(text_data)
            message = text_data_json['message']

            self.send(text_data=json.dumps({
                'message': message
            }))
    ```

## STEP2 - Add route for web sockets
- `AuthMiddlewareStack` replaces request of a view function with the currently authentiated user
- NOTE: Add `/ws` to distinguish websocket connections from ordinary HTTP

    // APP_NAME/routing.py
    ```
        from django.urls import re_path

        from . import consumers

        websocket_urlpatterns = [
            re_path(r'ws/chat/(?P<room_name>\w+)/$', consumers.ChatConsumer),
        ]
    ```

    // PROJECT_NAME/routing.py
    ```
        from channels.auth import AuthMiddlewareStack
        from channels.routing import ProtocolTypeRouter, URLRouter
        import APP_NAME.routing

        application = ProtocolTypeRouter({
            # (http->django views is added by default)
            'websocket': AuthMiddlewareStack(
                URLRouter(
                    APP_NAME.routing.websocket_urlpatterns
                )
            ),
        })
    ```

## STEP3 - Add channel redis

    ```
    pip install channels_redis==2.3.3
    ```

- Note: the version is to meet dependency requirements


## STEP4 - Add channel layer
- channel layer allows multiple consumers to talk to each other
    - i.e. opening multiple browser tabs, send message in one to make it appear in others


- First install channel_redis

    ```
        pip3 install channels_redis
    ```

- Second define channel layer with channel_redis (port 6379)

    ```
        CHANNEL_LAYERS = {
            "default": {
                "BACKEND": "channels_redis.core.RedisChannelLayer",
                "CONFIG": {
                    "hosts": [("localhost", 6379)],
                },
            },
        }
    ```

## STEP5 - Refine consumers

    ```
    from asgiref.sync import async_to_sync
    from channels.generic.websocket import WebsocketConsumer
    import json

    class ChatConsumer(WebsocketConsumer):
        def connect(self):
            self.room_name = self.scope['url_route']['kwargs']['room_name']
            self.room_group_name = 'chat_%s' % self.room_name

            # Join room group
            async_to_sync(self.channel_layer.group_add)(
                self.room_group_name,
                self.channel_name
            )

            self.accept()

        def disconnect(self, close_code):
            # Leave room group
            async_to_sync(self.channel_layer.group_discard)(
                self.room_group_name,
                self.channel_name
            )

        # Receive message from WebSocket
        def receive(self, text_data):
            text_data_json = json.loads(text_data)
            message = text_data_json['message']

            # Send message to room group
            async_to_sync(self.channel_layer.group_send)(
                self.room_group_name,
                {
                    'type': 'chat_message',
                    'message': message
                }
            )

        # Receive message from room group
        def chat_message(self, event):
            message = event['message']

            # Send message to WebSocket
            self.send(text_data=json.dumps({
                'message': message
            }))

    ```