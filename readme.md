# Mycroft in catalan

Bellow is a step by step guide to configure mycroft in catalan

- [mycroft.conf](#mycroftconf)
  * [Lang](#lang)
    + [Lang Config](#lang-config)
  * [STT - Speech to Text](#stt---speech-to-text)
    + [List of core engines that support catalan](#list-of-core-engines-that-support-catalan)
    + [List of plugins that support catalan](#list-of-plugins-that-support-catalan)
    + [STT Config](#stt-config)
      - [Installing jarbas-stt-plugin-chromium](#installing-jarbas-stt-plugin-chromium)
  * [TTS - Text to Speech](#tts---text-to-speech)
    + [List of core engines that support catalan](#list-of-core-engines-that-support-catalan-1)
    + [List of plugins that support catalan](#list-of-plugins-that-support-catalan-1)
    + [TTS Config](#tts-config)
      - [Installing festival](#installing-festival)
  * [Final config](#final-config)
- [Installing plugins](#installing-plugins)
  * [Manual install](#manual-install)
  * [Mycroft-pip](#mycroft-pip)
- [Translating Skills](#translating-skills)
  * [translate.mycroft.ai](#translatemycroftai)
  * [Exporting Pootle Manually](#exporting-pootle-manually)
  * [Manual translation](#manual-translation)
  * [Corner cases](#corner-cases)
  * [Replacing skills](#replacing-skills)
    + [News skill](#news-skill)
    + [Jokes](#jokes)
    + [Wikipedia](#wikipedia)
    + [blacklist official skills](#blacklist-official-skills)


# mycroft.conf

You want to edit the user config, this is under ```~/.mycroft/mycroft.conf```

Note that is the home directory of the user running mycroft, in a mark one this means ```/home/mycroft/.mycroft/mycroft.conf``` NOT ```/home/pi/.mycroft/mycroft.conf```

You can learn more about the .conf in the [docs](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/mycroft-conf)

After you edit your .conf you should restart mycroft for changes to take effect

## Lang

First we must set the global language of mycroft, this is what will be used to load skills and resource files

SPECIAL NOTES:
- this must match the [lang code for mycroft-core resource files](https://github.com/MycroftAI/mycroft-core/tree/dev/mycroft/res/text), in our case ```ca-es```
- the language code must match exactly! I submitted a [PR to improve this](https://github.com/MycroftAI/mycroft-core/pull/1335) back in 2017, but it was completely ignored
- skills will also use this lang code to search for resource files

### Lang Config

```json
{
  "lang": "ca-es"
}
```

## STT - Speech to Text

We need to configure an engine that supports Catalan, google chrome STT supports catalan, and this is what is used by the mycroft backend

SPECIAL NOTES:
- mycroft backend should just work with most languages
- chromium STT expects language code `ca-es`, it doesn't work if you use `ca` language code
- most STT engines [do not allow overriding the language code](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L33) unless implemented manually

The best way to workaround this is using plugins that support languages correctly

### List of core engines that support catalan 

- mycroft - only works if global lang is ```ca-es```, but this might stop working because its accidental, read [mycroft chat](https://chat.mycroft.ai/community/pl/j3bafyzjkpd3ifioihhwe84e6h) for details
- google cloud - best accuracy, not free, allows [overriding global language](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L109)
- google - to be used you need to add a api key, but keys are no longer issued, see details in [google chromium plugin](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium), this is effectively useless

### List of plugins that support catalan

Detailed plugin installation instructions available in [plugins section](#installing-plugins)

- [google chromium](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium)
- [vosk](https://github.com/JarbasLingua/jarbas-stt-plugin-vosk)
- [pocketsphinx](https://github.com/JarbasLingua/jarbas-stt-plugin-pocketsphinx)


### STT Config

#### Installing jarbas-stt-plugin-chromium

Let's avoid using mycroft STT because it can stop working at any time

```
mycroft-pip install jarbas-stt-plugin-chromium
```

configure the [google chromium plugin](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium) and override the language code

```json
{
  "lang": "ca-es",
   "stt": {
    "module": "chromium_stt_plug",
    "chromium_stt_plug": {
        "lang": "ca-ES"
    }
  }
}
```

## TTS - Text to Speech

once more we need to select an engine that supports catalan, by default mycroft only supports english

### List of core engines that support catalan

- espeak - needs to be manually installed
- festival - needs to be manually installed
- gtts - can be set from web interface, breaks often (uses google translate undocumented web api)
- google cloud - best accuracy, not free

### List of plugins that support catalan

Detailed plugin installation instructions available in [plugins section](#installing-plugins)

- [catotron](https://github.com/JarbasLingua/jarbas-tts-plugin-catotron) - best quality, but slow
- [softcatala](https://github.com/JarbasLingua/jarbas-tts-plugin-softcatala) - same as festival, but running in a server
- [voicerss](https://github.com/JarbasLingua/jarbas-tts-plugin-voicerss) - requires api key (free)
- [responsive voice](https://github.com/JarbasLingua/jarbas-tts-plugin-responsivevoice) - uses espeak for catalan

### TTS Config

Let's configure mycroft to use festival


#### Installing festival

Festival is supported by mycroft-core, but you need to install it manually

```
sudo apt-get -y install festival festvox-ca-ona-hts lame
```


```json
{
   "tts": {
      "module": "festival",
      "festival": {
          "lang": "catalan",
          "encoding": "ISO-8859-15//TRANSLIT"
      }
   }
}
```

## Final config

```json
{
  "lang": "ca-es",
   "stt": {
      "module": "chromium_stt_plug",
      "chromium_stt_plug": {
          "lang": "ca-ES"
   },
   "tts": {
      "module": "festival",
      "festival": {
          "lang": "catalan",
          "encoding": "ISO-8859-15//TRANSLIT"
      }
   }

}
```
# Installing plugins

To install a plugin you can use pip, this needs to be inside the mycroft venv!

You can do this in several ways

## Manual install

- manually activate the .venv, ```source mycroft-core/.venv/bin/activate```
- use the mycroft script alias (might not be available) ```mycroft-venv-activate```
- run the script explicitly ```mycroft-core/venv-activate.sh```

then run the install command
```
pip install jarbas-stt-plugin-chromium
```

## Mycroft-pip

mycroft provides a pip wrapper to do the above for you, this might be or not available in your platform

- use the mycroft alias (might not be available) ```mycroft-pip install jarbas-stt-plugin-chromium```
- run the script explicitly ```mycroft-core/bin/mycroft-pip install jarbas-stt-plugin-chromium```
- in a mark1 you will need to use sudo ```sudo mycroft-pip install jarbas-stt-plugin-chromium```

# Translating Skills

Translating skills is not straighforward, there are many edge cases

SPECIAL NOTES:
- any skill with a single file not translated will not load, all files must be translated
- the language code must match exactly the global config! I submitted a [PR to improve this](https://github.com/MycroftAI/mycroft-core/pull/1335) back in 2017, but it was completely ignored
- mycroft translate uses lang code ```ca``` not ```ca-es```, which means skills translated in [translate.mycroft.ai](https://translate.mycroft.ai/) will not work. See below.

## translate.mycroft.ai

The best way to translate skills is by using the mycroft translate platform, go to [translate.mycroft.ai](https://translate.mycroft.ai/) and help translating the skill in the marketplace

You will then have to wait for Mycroft (the company) to send the translations to the skills

In the case of catalan there is a problem, because the translate platform is using the lang code  ```ca``` instead of  ```ca-es```, read section bellow for workarounnds

## Exporting Pootle Manually

As pointed above, skills translated in [translate.mycroft.ai](https://translate.mycroft.ai/) will not work because they use ```ca``` locale. But, you can use a custom script to export strings from Pootle and put them with ```ca-es``` locale.

Just:
- clone [mycroft-update-translations](https://github.com/jmontane/mycroft-update-translations) repository in a working dir
```
git clone https://github.com/jmontane/mycroft-update-translations
```
- if needed, edit mycroft-update-translations.py and change settings. By default it translates skills in ```/opt/mycroft/skills/```

and then run the install command
```
cd mycroft-update-translations
./mycroft-update-translations
```

## Manual translation

TODO

### Resource files

some skills will need different values to use in the code depending on the language, a common approach is using the `translate_namedvalues` method from skills to lookup resources, this is essentially a .csv file with value pairs used to populate a python dictionary

this is a good approach for localization of skills, however it is not as straightforward as translating dialog files because the content of these files is not obvious and will depend on the code of a specific skill

using the official wikipedia skill as an example, the following directory structure will include the data to translate
```
- dialog
  - en-us
    - wikipedia_lang.value
  - ca-es
    - wikipedia_lang.value
```

the contents of (en-us) wikipedia_lang.value are
```
# Wikipedia language code used to service queries in the active language
code,en
```

the contents of (ca-es) wikipedia_lang.value are
```
# Codi de llengua de Viquipèdia usat per a fer consultes en la llengua activa
code,ca
```

this can be used in code this way
```python
data = self.translate_namedvalues("wikipedia_lang")
wiki.set_lang(data["code"])
``` 

if you translated the file above as ```code, ca-es``` the skill will not work, as a translator you have no way to know this without checking the code, so i recommend leaving a comment with link to relevant documentation, as you can see in examples above lines starting with # are ignored

## Corner cases

some skills can not be easily translated and need a pull request to handle this in code:
- they use a librayr / web api that expects english
- they use a web api that returns english results
- they need some language specific resource not accounted for by the original author

A possible work around is using translation at runtime, see [wolfram alpha skill](https://github.com/MycroftAI/fallback-wolfram-alpha/blob/20.08/__init__.py#L188) which does this


## Replacing skills

### News skill

To support catalan we need to find a news provider for atalonia

[this Pull Request](https://github.com/MycroftAI/skill-npr-news/pull/102) has been blocked due to a backend error

You can install [skill-news](https://github.com/JarbasLingua/skill-news) and blacklist the official skill

#### Configuring audio backend

By default mycroft uses a simple audio backend that has issues with http streams, we need to make mycroft use vlc instead, this will also allow many other skills to also work

```bash
sudo apt-get install vlc
```

edit your .conf and add the following

```json
  "Audio": {
    "backends": {
      "vlc": {
        "active": true
      }
    },
    "default-backend": "vlc"
  }
```

### Jokes

the official jokes skill does not have jokes in catalan, you can trigger it (resource files have been translated) but it will not work correcly, it should be blacklisted to avoid conflicts

TODO alternative skill

### Wikipedia

The official skill is supposed to support other languages, it uses the `translate_namedvalues` method described above, however there have been reports in the chat that the official skill was not working properly for catalan, i have not tested this

You can use my alternative skill, [wikipedia-for-humans skill](https://github.com/JarbasSkills/skill-wikipedia-for-humans), using the [wikipedia_for_humans](https://github.com/HelloChatterbox/wikipedia_for_humans) package from [chatterbox](https://hellochatterbox.com), it will blacklist the default skill automatically and already supports catalan

### blacklist official skills

mycroft makes it hard to disable official skills, if you delete them they will be automatically reinstalled

If you need to replace an official skill you have to blacklist it so it will not load

Edit your .conf and add the following section, make sure to use the exact names of the skill folders, usually of the format ```github_repo_name.author```

```json
  "skills": {
    "blacklisted_skills": ["mycroft-wiki.mycroftai", "mycroft-fallback-duck-duck-go.mycroftai"]
  }
```

If you are writing a skill that explicitly replaces an official skill you can do the following in python


```python
from mycroft.messagebus.message import Message
from mycroft.configuration import LocalConf, USER_CONFIG, Configuration


class MyAlternativeSkill(MycroftSkill):

    def initialize(self):
        self.blacklist_default_skill()

    def blacklist_default_skill(self):
        # load the current list of already blacklisted skills
        core_conf = Configuration.load_config_stack()
        blacklist = core_conf["skills"]["blacklisted_skills"]
        
        # check the folder name (skill_id) of the skill you want to replace
        skill_id = "mycroft-skill-to-replace.mycroftai"
        
        # add the skill to the blacklist
        if skill_id not in blacklist:
            self.log.debug("Blacklisting official mycroft skill")
            blacklist.append(skill_id)
            
            # load the user config file (~/.mycroft/mycroft.conf)
            conf = LocalConf(USER_CONFIG)
            if "skills" not in conf:
                conf["skills"] = {}
            conf["skills"]["blacklisted_skills"] = blacklist
            
            # save the user config file
            conf.store()

        # tell the intent service to unload the skill in case it was loaded already
        # this should avoid the need to restart
        self.bus.emit(Message("detach_skill", {"skill_id": skill_id}))

```
