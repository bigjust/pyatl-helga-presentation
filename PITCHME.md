# Helga Chatbot Framework

A survey of the Helga chatbot framework

---

### Helga, A Chatbot Framework

- IRC is life
- Pluggable communication backends
- Productivity Win
- Simple AND Fun!

Note:
Helga allows easy creation of chatbots, whether they have very
specific use-cases, like scrapping ##news for keywords, or as a
testing ground for Natural Language Processing.

Adding behaviors to the chatbot is accomplished through adding
plugins. Plugins are typically python modules registered on pypi
(though don't necessarily have to be). There are some builtins, like
`help`, `ping`, and `manager`

---

### A Short History...

@ul

- Olga, Perl like its 1999
- Helga, the lunch picker
- IMDB, dpaste, haiku, trivia, dice-rolling
- ReviewBoard, Jira, PagerDuty, Jenkins

@ulend

Note:
Olga was an irc bot that was always 'just around' @ CMG, which was a
python shop. It was decided that "We Ain't Got No Time For No Perl",
so Shaun waved his hands and created Helga. Olga Functionality was
re-implemented as Helga plugins, and Olga was retired.

The last bullet point shows when Helga became mission critical.

---

### Helga Features

@ul

- Can connect to IRC, HipChat, XMPP
- MongoDB
- Logging
- Webhooks
- Plugins!

@ulend

Note:
Out of the box, Helga comes with some pretty cool features that don't
do much by themselves, but abstract common things like databases and
webhooks to make writing plugins simpler, reducing boilerplate.

Based on the Twisted Framework, bundles MongoDB for easy access.
Webhooks also provide a web interface for plugins, as well as,
well.. Webhook abilities.

Interestingly, the logging and webhooks features are simply bundled
plugins. And we'll see other examples of how to log.

---

### Types of Plugins

- Command
- Match
- Preprocessor

---

### Commands

```
from helga.plugins import command

@command('foo', aliases=['f'], help='The foo command')
def foo(client, channel, nick, message, cmd, args):
    return u'You said "helga foo"'
```

---

### Match


---

### Preprocessor


---

### Class-based Example

from helga.plugins import Command

``` python

class FooCommand(Command):
    command = 'foo'
    aliases = ['f']
    help = 'Return the foo count. Usage: helga (foo|f)'

    def __init__(self, *args, **kwargs):
        super(FooCommand, self).__init__(*args, **kwargs)
        self.foo_count = 0

    def run(self, client, channel, nick, message, cmd, args):
        self.foo_count += 1
        return u'Foo count is {0}'.format(self.foo_count)

```

---

### Plugin Registration

Setuptools and entry_points

``` python

from setuptools import setup, find_packages

setup(
    name='helga_my_plugin',
    version='0.0.1',
    description='A foo plugin',
    author="Jane Smith"
    author_email="jane.smith@example.com",
    packages=find_packages(),
    py_modules=['helga_my_plugin'],
    include_package_data=True,
    zip_safe=True,
    entry_points=dict(
        helga_plugins=[
            'my_plugin = helga_my_plugin:foo',
        ],
    ),
)

```

Note:
The Helga plugin loader will find any modules that register their
entry points as helga_plugins.

---

### Smoke Signals

Emitting a Signal

``` python
import smokesignal

smokesignal.emit('foo', 1, 2, 3, four=4)
```

Receiving a Signal

``` python
@smokesignal.on('foo')
def my_callback(*args, **kwargs):
    pass
```

https://github.com/shaunduncan/smokesignal

Note:
Easy, right?

---


### A Few Short (and useful) Examples

- Youtube, Spotify Snarfing
- iMail, irc mail


---

### Markov Fun


Note:
Wouldn't be a chatbot without a markov plugin


---

### Jeopardy... In IRC

- Based on the

---

### Async / Background Tasks



---

### More Novel plugins

- Haskell REPL (https://github.com/carymrobbins/helga-haskell.git)
-
- Interactive Fiction???

Note:
Adam Coddington created an empty helga-interactive-fiction repo, the tease feels
are real.

---

### Plugins using Plugins

- Oral History -> Mimic
- Mimic -> Reddit Posting
- Jeopardy -> Urban Jeopardy

Note:
There are a couple ways to chain plugins together. One is to simply expose a
function that can be imported by other plugins (assuming dependencies have been
defined properly). The other is to use Smoke Signal.

---


### Proof-of-Conversation


---

### Related Projects

- Ansible (https://github.com/bigjust/ansible-helga)
- CookieCutter Template (https://github.com/bigjust/cookiecutter-helga-plugin)

---

### Credits / Further Reading

- Shaun Duncan, for creating Helga
- James Hsiao, for creating / maintaining Olga (Helga's predecessor)
- A certain channel on a certain irc network (yeah, you), for QA
