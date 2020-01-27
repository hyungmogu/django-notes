# Lesson 3 - Getting the channels package installed and configured


## Installation

1. Install Django channels
    ```
    pip install -U channels
    ```

2. Add channels to app in setting

    // PROJECT_NAME/settings.py
    ```
    INSTALLED_APPS = [
        ...
        'channels',
    ]
    ```

3. Add channel_layers to setting
    - Channel-lamemoryyers acts as a messenger between built piece of code
    - In  layer - fine for development, but not so great for production
    - Limit data to strings, bool, number, list, and dictionary

    ```
    // PROJECT_NAME/routing.py
    from channels.routing import ProtocolTypeRouter

    application = ProtocolTypeRouter({
        # Empty for now (http->django views is added by default)
    })


4. Set ASGI_APPLICATION in setting.py to point to the routing object

// PROJECT_NAME/settings.py
ASGI_APPLICATION = "PROJECT_NAME.routing.application"