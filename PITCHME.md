# Helga

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

def do_isup(client, channel, domain):
    resp = requests.get('http://isup.me/{domain}'.format(domain=domain))
    resp.raise_for_status()

    # Find the content we want
    status = re.findall(r'<div id="container">(.*?)<p>', resp.content.replace('\n', ''))
    client.msg(channel, status)


@command('isup', aliases=['downforeveryoneorjustme'],
         help='Is foo.com up? Usage: helga [isup|downforeveryoneorjustme] <domain>')
def isup(client, channel, nick, message, cmd, args):
    reactor.callLater(0, do_isup, client, channel, args[0])
    raise ResponseNotReady

```


Note:
Standard Command plugin. Triggered by the command name `isup` and
takes 1 argument for the domain. Calls the isup.me web service and
returns the output, which typically looks like "Looks like its just
you." or "Down for everyone"?

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

# setup.py

setup(
    name="helga-alias",
    version='0.4.0',
    description=('track nick changes'),
    author='Justin Caratzas',
    py_modules=['helga_alias'],
    entry_points = dict(
        helga_plugins = [
            'alias = helga_alias:alias',
        ],
    ),
)

```

Note:
The Helga plugin loader will find any modules that register their
entry points as helga_plugins.

The helga-alias plugin tracks nick changes, and provides a list of
aliases as a function that other plugins can use. It does this using
signals that are emitted by the core framework.

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
