# Helga Chatbot Framework

A survey of the Helga chatbot framework

---

### A Short History...

- Olga, Perl like its 1999
- Helga, the lunch picker
- IMDB, dpaste, haiku, trivia, dice-rolling
- ReviewBoard, Jira, PagerDuty, Jenkins

Note:
The last bullet point shows when Helga became mission critical. Ops team took
over the running instance.

---

### Helga Features

- Can connect to IRC, HipChat, XMPP
- MongoDB
- Webhooks
- Logging
- Webhooks
- Plugins!

Note:
Based on the Twisted Framework, bundles MongoDB for easy access.
Webhooks also provide a web interface for plugins
Bundled logging, but of course plugins provide more flexibility

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

### A Few Short (and useful) Examples

- Youtube, Spotify Snarfing
- iMail, irc mail
-

---

### Markov Fun


Note:
Wouldn't be a chatbot without a markov plugin


---

### Jeopardy... In IRC

- Based on the

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

### Smoke Signals

Emitting a Signal

``` python
@smokesignal.once('foo')
def my_callback():
    pass
```

Receiving a Signal

``` python
@smokesignal.on('foo')
def my_callback():
    pass
```

Note:
Easy, right?

---

### Related Projects

- Ansible (https://github.com/bigjust/ansible-helga)
- CookieCutter Template (https://github.com/bigjust/cookiecutter-helga-plugin)

---

### Credits / Further Reading

- Shaun Duncan, for creating Helga
- James Hsiao, for creating / maintaining Olga (Helga's predecessor)
- A certain channel on a certain irc network (yeah, you), for QA
