# Mycroft in catalan

Bellow is a in depth step by step guide to configure mycroft in catalan

- [Translating Skills](#translating-skills)
  * [translate.mycroft.ai](#translatemycroftai)
  * [Exporting Pootle Manually](#exporting-pootle-manually)
  * [Manual translation](#manual-translation)
    + [Resource files](#resource-files)
  * [Corner cases](#corner-cases)
  * [Replacing skills](#replacing-skills)
    + [Jokes](#jokes)
    + [News skill](#news-skill)
      - [Configuring audio backend](#configuring-audio-backend)
    + [blacklist official skills](#blacklist-official-skills)
- [Installing plugins](#installing-plugins)
  * [Manual install](#manual-install)
  * [Mycroft-pip](#mycroft-pip)
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
- [Wake Words](#wake-words)
    + [Precise 0.3](#precise-03)
    + [Precise 0.2](#precise-02)
    + [Wake word - Ey Ordenador](#wake-word---ey-ordenador)
    + [Standup word - Desperta](#standup-word---desperta)
  * [Final config](#final-config)


# Translating Skills

The first thing you should do is translate skills, otherwise even if mycroft understands you he will just speak error messages since it won't know how to do anything

Translating skills is not always straighforward, there are many edge cases

SPECIAL NOTES:
- any skill with a single file not translated will not load, all files must be translated
- the language code must match exactly the global config! I submitted a [PR to improve this](https://github.com/MycroftAI/mycroft-core/pull/1335) back in 2017, but it was completely ignored, i closed it (unmerged) after 3 years
- mycroft translate uses lang code ```ca``` not ```ca-es```, which means skills translated in [translate.mycroft.ai](https://translate.mycroft.ai/) will not work. 

## translate.mycroft.ai

The best way to translate skills is by using the mycroft translate platform, go to [translate.mycroft.ai](https://translate.mycroft.ai/) and help translating the skill in the marketplace

You will then have to wait for Mycroft (the company) to send the translations to the skills repositories

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

if you translated the file above as ```code,ca-es``` the skill will not work, as a translator you have no way to know this without checking the code, so i recommend leaving a comment with link to relevant documentation, as you can see in examples above lines starting with # are ignored

## Corner cases

some skills can not be easily translated and need a pull request to handle this in code:
- they use a librayr / web api that expects english
- they use a web api that returns english results
- they need some language specific resource not accounted for by the original author

A possible work around is using translation at runtime, see [wolfram alpha skill](https://github.com/MycroftAI/fallback-wolfram-alpha/blob/20.08/__init__.py#L188) which does this


## Replacing skills


### Jokes

the official jokes skill does not have jokes in catalan, you can trigger it (resource files have been translated) but it will not work correcly, it should be blacklisted to avoid conflicts

You can use my alternative skill, [skill-icanhazdadjoke](https://github.com/JarbasSkills/skill-icanhazdadjoke), using the [icanhazdadjoke.com/](https://icanhazdadjoke.com/) API, it will blacklist the default skill automatically and supports all languages (via google translate)

```
msm install https://github.com/JarbasSkills/skill-icanhazdadjoke
```

### News skill

To support catalan we need to find a news provider for Catalonia

[this Pull Request](https://github.com/MycroftAI/skill-npr-news/pull/102) adds [CCMA Catalunya Informació](https://www.ccma.cat/catradio/directe/catalunya-informacio/), but it has been blocked due to a mycroft backend bug

You can install [skill-news](https://github.com/JarbasLingua/skill-news), it will blacklist the default skill automatically and already supports catalan

NOTE: this is temporary, once mycroft supports this i will deprecate this skill, but i intend to do this gracefully and whitelist the original again automatically, no action needed from your part

```
msm install https://github.com/JarbasLingua/skill-news
```

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


### blacklist official skills

mycroft makes it hard to disable official skills, if you delete them they will be automatically reinstalled

If you need to replace an official skill you have to [blacklist it](https://mycroft-ai.gitbook.io/docs/skill-development/faq#how-do-i-disable-a-skill) so it will not load

Edit your .conf and add the following section, make sure to use the exact names of the skill folders, usually of the format ```github_repo_name.author```

```json
  "skills": {
    "blacklisted_skills": ["mycroft-wiki.mycroftai", "mycroft-fallback-duck-duck-go.mycroftai"]
  }
```

If you are writing a skill that explicitly replaces an official skill you can do the following in python


```python
from mycroft.messagebus.message import Message
from mycroft.configuration import LocalConf, USER_CONFIG


class MyAlternativeSkill(MycroftSkill):

    def initialize(self):
        self.blacklist_default_skill()

    def blacklist_default_skill(self):
        # load the current list of already blacklisted skills
        blacklist = self.config_core["skills"]["blacklisted_skills"]
        
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

            # update the blacklist field
            conf["skills"]["blacklisted_skills"] = blacklist
            
            # save the user config file
            conf.store()

        # tell the intent service to unload the skill in case it was loaded already
        # this should avoid the need to restart
        self.bus.emit(Message("detach_skill", {"skill_id": skill_id}))

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
- The terms "language code" and "lang code" refer to a [BCP-47 language code](https://en.wikipedia.org/wiki/IETF_language_tag), commonly used by computers to identify a language and region. Today, we'll be using the language code "ca-es", referring to the dialect of Catalan commonly spoken in Spain. Other familiar examples include "en-us" (English/United States) and "es-es" (Castillian/Spain)"

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

- espeak - needs to be manually installed, very robotic!
- festival - needs to be manually installed, acceptable quality
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

# Wake Words

A mycroft wake word is what you need to say to make mycroft start listening

Mycrofts uses [precise](https://github.com/MycroftAI/mycroft-precise) for this, it is a model trained on sounds therefore it does not need "translation", if you want to change the name of mycroft then you need to get 50+ recordings and [train a model](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/wake-word#training-a-wake-word-model)

Some users struggle with training so i recommend you check [Precise Community Repo](https://github.com/MycroftAI/precise-community-data), if you upload your recordings the community will train a model for you! This also means the model will keep improving over time

There are two wake words we need to configure, the "name" of mycroft, and the stand up word "wake up"  (more info on this bellow)

### Precise 0.3

Mycroft downloads the [precise binary](https://github.com/MycroftAI/precise-data/tree/dist) at runtime, there is version 0.2 and 0.3, by default it uses 0.2 but some models are trained on 0.3 (github version)

```json
 "precise": {
   "dist_url": "https://github.com/MycroftAI/precise-data/raw/dist/{arch}/latest-dev"
 }
```

### Precise 0.2

If you want to revert to version 0.2

```json
 "precise": {
   "dist_url": "https://github.com/MycroftAI/precise-data/raw/dist/{arch}/latest"
 }
```

### Wake word - Ey Ordenador

For this example we will use a community model, [Ey Ordenador](https://github.com/MycroftAI/Precise-Community-Data/tree/master/heycomputer/es)

Download the model from [here](https://github.com/MycroftAI/Precise-Community-Data/blob/master/heycomputer/models/heycomputer-es-0.3.0-20190815-eltocino.tar.gz) amd extract the files to ```~/.mycroft/precise/models``` (or any other location of your choice)

You can change 2 parameters to improve detection with your voice
-  ```sensitivity``` Higher = more sensitive
-  ```trigger_level``` Higher = more delay & less sensitive


```json
  "listener": {
      "wake_word": "ey_ordenador"
  },
  "hotwords": {
    "ey_ordenador": {
        "module": "precise",
        "local_model_file": "~/.mycroft/precise/models/hey-computer-es.pb",
        "sensitivity": 0.5, 
        "trigger_level": 3  
        }
  }
```

### Standup word - Desperta

If you use the [naptime skill](https://github.com/MycroftAI/skill-naptime) you can tell mycroft to "go to sleep", when you do this STT will NOT be processed, this is a privacy feature

But the implication of this is that you must translate this wake word, by default it is in english "wake up"

SPECIAL NOTES: 
- when sleeping mycroft will still react to the wake-word but instead of STT it will listen for the standup word
- you must say "hey mycroft, wake up" to activate STT again
- since this requires 2 wake words in a row, the sensitivity of "wake up" can be lower and more false activations are acceptable

To translate this to catalan we will use the [snowboy plugin](https://github.com/JarbasLingua/jarbas-wake-word-plugin-snowboy):
- it is very lightweight
- multiple models can be loaded at once
- you can [train a personal model](https://snowboy.kitt.ai/) for each person in your house
- unfortunately snowboy is shutting down on December 31st 2020, train models while you can!!!

Install the plugin with

```bash
mycroft-pip install jarbas-wake-word-plugin-snowboy
```

Now lets configure mycroft to use this

```json
 "listener": {
      "stand_up_word": "desperta"
  },
  "hotwords": {
    "desperta": {
        "module": "snowboy_ww_plug",
        "models": [
            {"sensitivity": 0.5, "model_path": "path/to/first_person.pmdl"},
            {"sensitivity": 0.5, "model_path": "path/to/second_person.pmdl"},
            {"sensitivity": 0.5, "model_path": "path/to/third_person.pmdl"}
         ]
    }
  }
```

If you have not trained a model do not worry, [@jmontane](https://github.com/jmontane) and XXX trained some models which are included in the plugin and should work ok! be sure to adjust sensitivities

TODO - finish this section, models not yet ready

```json
  "listener": {
      "stand_up_word": "desperta"
  },
  "hotwords": {
    "desperta": {
        "module": "snowboy_ww_plug",
        "models": [
            {"sensitivity": 0.5, "model_path": "desperta_jmontane.pmdl"},
            {"sensitivity": 0.5, "model_path": "desperta_XXX.pmdl"},
            {"sensitivity": 0.5, "model_path": "desperta_YYY.pmdl"}
         ]
    }
  }
```


Now you can wake up mycroft in catalan! "ey ordenador, desperta"


## Final config

This was a long tutorial, but if you following everything your .conf should look like this


```json
{
  "lang": "ca-es",
  "stt": {
      "module": "chromium_stt_plug",
      "chromium_stt_plug": {
          "lang": "ca-ES"
      }
   },
  "tts": {
      "module": "festival",
      "festival": {
          "lang": "catalan",
          "encoding": "ISO-8859-15//TRANSLIT"
      }
   },
  "listener": {
      "wake_word": "ey_ordenador",   
      "stand_up_word": "desperta"
  },
  "precise": {
   "dist_url": "https://github.com/MycroftAI/precise-data/raw/dist/{arch}/latest-dev"
  },
  "hotwords": {
    "ey_ordenador": {
        "module": "precise",
        "local_model_file": "~/.mycroft/precise/models/hey-computer-es.pb",
        "sensitivity": 0.5,  
        "trigger_level": 3 
        },
     "desperta": {
        "module": "snowboy_ww_plug",
        "models": [
            {"sensitivity": 0.5, "model_path": "desperta_jmontane.pmdl"},
            {"sensitivity": 0.5, "model_path": "desperta_XXX.pmdl"},
            {"sensitivity": 0.5, "model_path": "desperta_YYY.pmdl"}
         ]
      }
   },
   "Audio": {
    "backends": {
      "vlc": {
        "active": true
      }
    },
    "default-backend": "vlc"
  }
}
```




