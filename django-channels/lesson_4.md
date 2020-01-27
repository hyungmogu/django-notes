# Lesson 4 - Writing Web Socket Views

## STEP1 - Add consumer
- consumer accepts websocket connections
- consumer is like a view/APIView in views.py but for web sockets

    // APP_NAME/consumer.py
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
