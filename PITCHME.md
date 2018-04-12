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

``` python

@match('{}(artist|album|track)/(\w+)'.format(spotify_url))
def spotify(client, channel, nick, message, matches):
    return process_spotify_url(*matches[0])

def process_spotify_url(spotify_type, spotify_id):

    token = get_spotify_token()
    resp = requests.get(
        '{}{}s/{}'.format(
            spotify_api_base,
            spotify_type,
            spotify_id
        ),
        headers={'Authorization': 'Bearer {}'.format(token)}
    )

    resp_json = resp.json()

    if spotify_type == 'artist':
        return '{} ({})'.format(
            resp_json['name'],
            ', '.join(resp_json['genres'])
        )

    return '{} - {}'.format(
        resp_json['artists'][0]['name'],
        resp_json['name']
    )

```

---

### Preprocessor

``` python
@preprocessor
def yelling(client, channel, nick, message, *args):

    if len(args) == 0:
        # Preprocessor
        if is_shout(message):
            count = db.yelling.find({
                'channel': channel,
            }).count()

            if count:
                random_resp = db.yelling.find({
                    'channel': channel
                })[random.randrange(count)]

                client.msg(channel, random_resp['msg'])

            db.yelling.update_one({
                'msg': message,
                'channel': channel,
            }, { '$set': { 'msg': message } }, upsert=True)

        return (channel, nick, message)
```

Note:

helga-yelling simply tests for statements that are in all caps. If it
sees one, it'll add it to the database, and respond with another all
caps statement it's seen previously.

Its Hilarious.

---

### Class-based Example

``` python

class FooCommand(Command):
    command = 'foo'
    aliases = ['f']
    help = 'Return the foo count. Usage: helga (foo|f)'

    def __init__(self, *args, **kwargs):
        super(FooCommand, self).__init__(*args, **kwargs)
        self.foo_count = 0

    def run(self, *args*)
        self.foo_count += 1
        return u'Foo count is {0}'.format(self.foo_count)

```

---

### Plugin Registration

Setuptools and entry_points

``` python
setup(
    name="helga-alias",
    version='0.4.0',
    description=('track nick changes'),
    author='Justin Caratzas',
    py_modules=['helga_alias'],
    entry_points = dict(
        helga_plugins = [
            'alias = helga_alias:alias',
        ]))
```

Note:
The Helga plugin loader will find any modules that register their
entry points as helga_plugins.

The helga-alias plugin tracks nick changes, and provides a list of
aliases as a function that other plugins can use. It does this using
signals that are emitted by the core framework.

---

### Smoke Signals

``` python
import smokesignal

# Emitting a signal
smokesignal.emit('thing_happened', 1, 2, 3, four=4)

# Receiving a Signal
@smokesignal.on('thing_happened')
def my_callback(*args, **kwargs):
    pass
```

https://github.com/shaunduncan/smokesignal

Note:
Easy, right?

---


### A Few Simple Plugins

- Youtube (https://github.com/narfman0/helga-youtube-metadata)
- Mail (https://github.com/bigjust/helga-mail)
- Reminders (https://github.com/shaunduncan/helga-reminders)

Note:

Youtube and spotify links can trigger a call to their respective APIs
and we can return information such as the video title, length,
uploader, etc.

Helga-mail sends messages to other users (of the channel), and
presents them with the message upon re-entering a channel.

Reminders plugin can set a recurring reminder, such as for a morning
standup, and uses judicious use of reactor.callLater() in order to
execute reminders in the future.

---

### Helga, Use Your Words

- Helga-Markovify (https://github.com/narfman0/helga-markovify)
- Helga-Mimic (https://github.com/bigjust/helga-mimic.git)

Note:

Generating conversational text that isn't terrible is a fun
challenge. There are challenges with weird tokens, unbalanced
parenthesis, brackets, quotes, etc. There are two plugins that attempt
this, in slightly different ways.

Helga-Markovify uses the markovify plugin.

Helga-Mimic uses the oral history plugin, which logs the text sent to
the channel in the mongo database. It feeds that into a python library
called Cobe, which is the spiritual successor to the MegaHAL
conversation simulator. It can then build conversational
units by filtering on that table, so it can mimic a specific person,
or the channel as a whole.

It presents fairly realistic, yet still funny text, which can be
seeded by text as well, to make it seem like its "responding". We
trigger this action by directly addressing the bot

---

### Jeopardy... In IRC

- Live Demo!

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

### Even More

- helga-twitter (https://github.com/bigjust/helga-twitter)
- helga-repost (https://github.com/bigjust/helga-repost)
- helga-weather (https://github.com/narfman0/helga-weather)
- helga-clojure (https://github.com/will2dye4/helga-clojure)

Note:
the twitter allows the channel, and other plugins, to tweet. Other
plugins, like the mimic plugin, can trigger a tweet, with say ",mimic
tweet", which will simply tweet the last response that the plugin
generated.

Repost uses the preprocessor to track urls or links, and if someone
who isn't paying attention posts the same link 10 minutes later, it'll
duly report it as a repost, as well as how long ago it was first seen.

---

### Related Projects

- Ansible (https://github.com/bigjust/ansible-helga)
- CookieCutter Template (https://github.com/bigjust/cookiecutter-helga-plugin)

---

### Credits / Further Reading

- Shaun Duncan, for creating Helga
- James Hsiao, for creating / maintaining Olga (Helga's predecessor)
- All the plugin authors (Alfredo Deza, Cary Robbins, Jon Robison, Ken
  Dreyer, Matt Simpson, Michael Orr, etc etc)
- A certain channel on a certain irc network (yeah, you), for QA
