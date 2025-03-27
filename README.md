```markdown
# MEchgram Library Documentation

MEchgram is a powerful and flexible Python library for building Telegram bots. It abstracts the Telegram Bot API and provides a simple interface for sending messages, media, handling inline queries and callback queries, administering chats, and much more. MEchgram also includes a helpful `Types` module to simplify working with data structures (like Message, Chat, and User).

---

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [A Simple Bot Example](#a-simple-bot-example)
  - [Using the Types Module](#using-the-types-module)
- [API Reference](#api-reference)
  - [The Bot Class](#the-bot-class)
    - [Initialization](#initialization)
    - [Handler Registration](#handler-registration)
    - [Message Sending Methods](#message-sending-methods)
    - [Media Methods](#media-methods)
    - [Administrative Functions](#administrative-functions)
    - [Webhook Management](#webhook-management)
    - [Editing and Copying Messages](#editing-and-copying-messages)
    - [Game and Payment Functions](#game-and-payment-functions)
    - [Chat Actions and Miscellaneous Methods](#chat-actions-and-miscellaneous-methods)
    - [Protection Functions](#protection-functions)
    - [Asynchronous Operation](#asynchronous-operation)
  - [The Types Module](#the-types-module)
- [Advanced Features](#advanced-features)
  - [Next-Step Handlers](#next-step-handlers)
  - [Chat and Group Management](#chat-and-group-management)
- [Examples and Templates](#examples-and-templates)
- [Contributing](#contributing)
- [License](#license)
- [Support and Contact](#support-and-contact)

---

## Overview

MEchgram is designed to simplify Telegram bot development by providing a consistent and extensible API. Its features include:

- **Simple command and message handling** with support for both synchronous and asynchronous operation.
- **Built-in administrative functions** for muting, banning, promoting, and managing chats.
- **Comprehensive media support** (text, photos, documents, audio, video, voice, stickers, dice, and animations).
- **Inline keyboards and callback query handling** for rich interactive bots.
- **Webhook management and message editing/copying** capabilities.
- **Support for games and payments** (e.g., sending invoices, handling shipping queries).
- **Protection functions** to load external protection code (e.g., custom User-Agent, DNS settings) to help secure API requests.
- **A `Types` module** that wraps common Telegram data structures (Message, Chat, User, etc.) for cleaner code.

---

## Installation

Clone the repository and install via pip (from the root directory where `setup.py` is located):

```bash
pip install .
```

Once installed, MEchgram appears in your package list as **MEchgram**.

---

## Project Structure

A typical project structure might look like this:

```
MEchgram/
├── MEchgram/
│   ├── __init__.py          # Exports Bot and Types
│   ├── bot.py               # Core Bot class
│   └── types.py             # Data types: Message, Chat, User, etc.
├── examples/
│   ├── simple_bot.py        # Basic bot example
│   ├── admin_bot.py         # Advanced bot with admin functions
│   └── async_bot.py         # Example using async_run()
├── README.md                # Overview and quick start
└── setup.py                 # Installation script
```

---

## Getting Started

### A Simple Bot Example

The simplest bot just responds to a command. For example:

```python
from MEchgram import Bot

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("/start")
def start_handler(update):
    return "Hello, world!"

bot.run()
```

This bot registers a handler for the `/start` command and then runs the polling loop.

### Using the Types Module

The `Types` module offers convenient wrappers for Telegram objects. For example:

```python
from MEchgram import Bot, Types

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("/start")
def start_handler(update):
    msg = Types.Message(update["message"])
    user_name = msg.sender.full_name
    return f"Hello, {user_name}! Welcome to MEchgram."

bot.run()
```

Here, `Types.Message` gives you easy access to properties like text, chat, and sender (with `full_name` computed from first and last names).

---

## API Reference

### The Bot Class

The `Bot` class is the central component of MEchgram. Below is a detailed overview of its methods.

#### Initialization

```python
Bot(token: str, polling_interval: float = 1.0)
```

- **token**: Your Telegram bot token.
- **polling_interval**: Delay between polling requests (default: 1 second).

Example:
```python
bot = Bot(token="YOUR_BOT_TOKEN_HERE")
```

#### Handler Registration

```python
on(command: str, handler=None)
```

Registers a handler for a given command. Supports both decorator usage and direct call.

Decorator example:
```python
@bot.on("/start")
def start_handler(update):
    return "Hello!"
```

Direct call example:
```python
def start_handler(update):
    return "Hello!"
bot.on("/start", start_handler)
```

#### Message Sending Methods

- **send_message(chat_id, text, reply_markup=None, parse_mode=None)**  
  Sends a text message.
  
  Example:
  ```python
  bot.send_message(123456789, "Hello, world!", parse_mode="Markdown")
  ```

- **send_photo(chat_id, photo, caption=None, reply_markup=None, parse_mode=None)**  
  Sends a photo. The `photo` parameter may be a file ID, URL, or byte data.
  
- **send_document(chat_id, document, caption=None, reply_markup=None, parse_mode=None)**  
  Sends a document.

#### Media Methods

- **send_dice(chat_id, emoji="🎲", reply_markup=None)**  
  Sends a dice animation. The emoji parameter can be one of: "🎲", "🏀", "⚽", "🎯", "🎳", or "🎰".

- **send_sticker(chat_id, sticker, reply_markup=None)**  
  Sends a sticker (via file ID or URL).

#### Administrative Functions

Methods for managing chat members and messages:

- **mute_user(chat_id, user_id)**  
- **unmute_user(chat_id, user_id)**  
- **mute_user_for_time(chat_id, user_id, until_date)**  
- **ban_user(chat_id, user_id, until_date=None)**  
- **unban_user(chat_id, user_id)**  
- **ban_user_for_time(chat_id, user_id, until_date)**  
- **pin_message(chat_id, message_id, disable_notification=False)**  
- **unpin_message(chat_id, message_id=None)**  
- **promote_chat_member(chat_id, user_id, ...)**  
  _(See source for full parameters)_

#### Webhook Management

- **set_webhook(url, certificate=None, max_connections=None, allowed_updates=None)**
- **delete_webhook(drop_pending_updates=False)**
- **get_webhook_info()**

These methods allow you to switch from polling to webhook mode.

#### Editing and Copying Messages

- **edit_message_media(chat_id, message_id, media, reply_markup=None)**
- **edit_message_caption(chat_id, message_id, caption, parse_mode=None, reply_markup=None)**
- **copy_message(chat_id, from_chat_id, message_id, caption=None, parse_mode=None)**

#### Game and Payment Functions

- **send_game(chat_id, game_short_name, reply_markup=None)**
- **set_game_score(chat_id, user_id, score, force=False, disable_edit_message=False, message_id=None, inline_message_id=None)**
- **get_game_high_scores(chat_id, user_id, message_id=None, inline_message_id=None)**
- **send_invoice(chat_id, title, description, payload, provider_token, start_parameter, currency, prices, need_shipping_address=False, reply_markup=None, parse_mode=None)**
- **answer_shipping_query(shipping_query_id, ok, shipping_options=None, error_message=None)**
- **answer_pre_checkout_query(pre_checkout_query_id, ok, error_message=None)**
- **refund_payment(chat_id, message_id, refund_amount, currency, comment=None)**
  _(Note: Refund functionality is hypothetical.)_

#### Chat Actions and Miscellaneous Methods

- **send_chat_action(chat_id, action)**  
  Sends an action like "typing", "upload_photo", etc.
  
  For convenience, wrapper methods like `typing(chat_id)` and `upload_photo(chat_id)` can be implemented.

- **send_popup(callback_query_id, text, show_alert=True, url=None, cache_time=0)**  
  A convenience method to answer a callback query with a pop-up alert.

#### Protection Functions

- **load_protection()**  
  Downloads external protection code (from a specified URL), which sets variables such as `UserAgentSM` and `dnsSM`.  
- **_send_request(method, url, \*\*kwargs)**  
  A helper method that wraps HTTP requests and automatically includes custom headers (User-Agent from protection and optional DNS settings).

#### Asynchronous Operation

- **async_run()**  
  An asynchronous wrapper that runs the synchronous `run()` method in an executor, so you can integrate MEchgram into an async application.

Example:
```python
import asyncio
from MEchgram import Bot

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("/start")
def start_handler(update):
    return "Hello, async world!"

async def main():
    await bot.async_run()

if __name__ == "__main__":
    asyncio.run(main())
```

---

### The Types Module

The `Types` module contains helper classes for easier data handling:

- **Message(data: dict)**  
  A wrapper for the raw message. Provides properties:
  - `text`: Returns message text.
  - `chat`: Returns a `Chat` object.
  - `sender`: Returns a `User` object.

- **Chat(data: dict)**  
  Provides:
  - `id`: The chat ID.
  - `title`: The chat title.

- **User(data: dict)**  
  Provides:
  - `id`: The user ID.
  - `first_name`: The first name.
  - `last_name`: The last name.
  - `full_name`: A computed full name (combining first and last names).

- **InlineKeyboardButton(text, url=None, callback_data=None)**  
  Represents a single inline button. Use `to_dict()` to convert it for the API.

- **InlineKeyboardMarkup(buttons: list = None)**  
  Represents an inline keyboard. You can add buttons via the `add()` method, and then use `to_dict()` to send it.

- **CallbackQuery**  
  A static helper class to format answers for callback queries.

At the end of `types.py`, a wrapper class is provided:

```python
class Types:
    Message = Message
    Chat = Chat
    User = User
    InlineKeyboardButton = InlineKeyboardButton
    InlineKeyboardMarkup = InlineKeyboardMarkup
    CallbackQuery = CallbackQuery
```

This allows you to import both Bot and Types like so:

```python
from MEchgram import Bot, Types
```

---

## Advanced Features

### Next-Step Handlers

For multi-step interactions (e.g., asking a question and then processing the response), you can implement a next-step handler mechanism using a global dictionary mapping chat IDs to handler functions. This allows you to “register” a handler that will process the next message from that user.

Example implementation:
```python
next_step_handlers = {}

def set_next_step(chat_id, handler):
    next_step_handlers[chat_id] = handler

def run_next_step(chat_id, update):
    if chat_id in next_step_handlers:
        handler = next_step_handlers.pop(chat_id)
        return handler(update)
    return None
```

Integrate this in your general message handler to decide if the message should be processed as a new command or as the next step in an ongoing interaction.

### Chat and Group Management

MEchgram includes methods for chat administration and settings, such as:
- **get_chat(chat_id)**
- **get_chat_administrators(chat_id)**
- **get_chat_members_count(chat_id)**
- **get_chat_member(chat_id, user_id)**
- **export_chat_invite_link(chat_id)**
- **set_chat_title(chat_id, title)**
- **set_chat_description(chat_id, description)**
- **set_chat_photo(chat_id, photo)**
- **delete_chat_photo(chat_id)**
- **set_chat_sticker_set(chat_id, sticker_set_name)**
- **delete_chat_sticker_set(chat_id)**
- **promote_chat_member(chat_id, user_id, …)**

These functions let you manage the chat environment directly from your bot.

---

## Examples and Templates

### Simple Echo Bot
```python
from MEchgram import Bot, Types

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("")
def echo_handler(update):
    msg = Types.Message(update["message"])
    return msg.text

bot.run()
```

### Admin Bot with Archives

(See the detailed admin example in the examples directory for functionalities such as registering users in a database, broadcasting messages, uploading/downloading files, etc.)

### Inline Keyboard Example
```python
from MEchgram import Bot, Types

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("/start")
def start_handler(update):
    chat_id = update["message"]["chat"]["id"]
    keyboard = Types.InlineKeyboardMarkup()
    keyboard.add(Types.InlineKeyboardButton("Donate", url="https://example.com/donate"))
    return f"Welcome! Please choose an option:", keyboard.to_dict()

bot.run()
```

### Asynchronous Bot Example
```python
import asyncio
from MEchgram import Bot

bot = Bot(token="YOUR_BOT_TOKEN_HERE")

@bot.on("/start")
def start_handler(update):
    return "Hello, async world!"

async def main():
    await bot.async_run()

if __name__ == "__main__":
    asyncio.run(main())
```

---

## Contributing

Contributions, bug reports, and feature requests are welcome. Please open an issue or submit a pull request in the GitHub repository.

---

## License

MEchgram is licensed under the MIT License. See the LICENSE file for details.

---

## Support and Contact

If you have any questions or need assistance, please contact the maintainers or open an issue in the GitHub repository.

---

*This documentation provides an extensive overview of the MEchgram library, its API, and how to build Telegram bots with it. Feel free to extend it further as new features are added.*
```

---

This documentation is written in Markdown and covers all aspects of the library, including all major functions and usage examples. You can adjust and expand the content as needed for your website or project documentation.
