# Mycroft in catalan

Bellow is a in depth step by step guide to configure mycroft in catalan

- [Translating Core](#translating-core)
- [Translating Skills](#translating-skills)
  * [translate.mycroft.ai](#translatemycroftai)
  * [Exporting Pootle Manually](#exporting-pootle-manually)
  * [Manual translation](#manual-translation)
    + [Bootstrapping](#bootstrapping)
    + [locale](#locale)
    + [Resource files](#resource-files)
    + [Pull Request](#pull-request)
  * [Corner cases](#corner-cases)
    + [blacklist official skills](#blacklist-official-skills)
- [Installing plugins](#installing-plugins)
  * [Manual install](#manual-install)
  * [Mycroft-pip](#mycroft-pip)
  * [From source](#from-source)
- [Lingua franca](#lingua-franca)
  * [Updating lingua_franca to catalan](#updating-lingua-franca-to-catalan)
  * [Enabling lingua_franca 0.3.X](#enabling-lingua-franca-03x)
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
  * [Standup word - Desperta](#standup-word---desperta)
  * [Final config](#final-config)
- [Additional tweaks](#additional-tweaks)
  * [Wake Words](#wake-words)
    + [Precise 0.3](#precise-03)
    + [Precise 0.2](#precise-02)
    + [Wake word - Ey Ordenador](#wake-word---ey-ordenador)
  * [Replacing skills](#replacing-skills)
    + [Wolfram alpha](#wolfram-alpha)
    + [Duck Duck go](#duck-duck-go)
    + [Jokes](#jokes)
    + [News skill](#news-skill)
      - [Configuring audio backend](#configuring-audio-backend)

# Translating Core

There are some files in mycroft-core that need to be translated, these are used mainly for default dialogs

You can find those files at [mycroft-core/mycroft/res/text](https://github.com/MycroftAI/mycroft-core/tree/dev/mycroft/res/text)

- copy the ```en-us``` folder to ```ca-es```
- translate any files not yet translated

NOTE: mycroft-core resource files have already been translated to catalan, but over time more files might be added

# Translating Skills

The first thing you should do is translate skills, otherwise even if mycroft understands you he will just speak error messages since it won't know how to do anything

Translating skills is not always straighforward, there are many edge cases

SPECIAL NOTES:
- any skill with a single file not translated will not load, all files must be translated
- the language code must match exactly the global config! I submitted a [PR to improve this](https://github.com/MycroftAI/mycroft-core/pull/1335) back in 2017, but it was completely ignored, i closed it (unmerged) after 3 years
 
## translate.mycroft.ai

The best way to translate skills is by using the mycroft translate platform, go to [translate.mycroft.ai](https://translate.mycroft.ai/) and help translating the skill in the marketplace

You will then have to wait for Mycroft (the company) to send the translations to the skills repositories


## Exporting Pootle Manually

Skills translated in [translate.mycroft.ai](https://translate.mycroft.ai/) need to be manually synced by the mycroft team. But you can use a custom script to export strings from Pootle if you don't want to wait!

clone [mycroft-update-translations](https://github.com/jmontane/mycroft-update-translations) repository and run it

```
git clone https://github.com/jmontane/mycroft-update-translations
cd mycroft-update-translations
./mycroft-update-translations
```

NOTE: currently the script does not accept command line arguments so you need to edit the .py file. It is pre-configured for catalan, if needed, edit mycroft-update-translations.py and change settings. By default it translates skills in ```/opt/mycroft/skills/```, if you want to translate a different language or have changed the ```data_dir``` setting in the .conf you need to edit this file


## Manual translation

If your skill is not in the marketplace you need to translate it manually, since it wont be in the translate platform

### Bootstrapping

It is much easier to start translations if you actually have files to translate, instead of creating them manually one by one

You can use a script by [@jmontane](https://github.com/jmontane) to copy the language resources from a different language, usually from ```en-us``` (the default language for most skills) and translate the copied files, other times it will make more sense to copy a different language, eg, ```pt-br``` to ```pt-pt``` or ```es-es``` to ```ca-es```

clone [mycroft-copy-translations](https://github.com/jmontane/mycroft-copy-translations) repository and run it

```
git clone https://github.com/jmontane/mycroft-copy-translations
cd mycroft-copy-translations
./mycroft-copy-translations
```

NOTE: currently the script does not accept command line arguments so you need to edit the .py file. It is pre-configured for catalan, if needed, edit mycroft-update-translations.py and change settings. By default it translates skills in ```/opt/mycroft/skills/```, if you want to translate a different language or have changed the ```data_dir``` setting in the .conf you need to edit this file

### locale

TODO

### Resource files

some skills will need different values to use in the code depending on the language, a common approach is using the `translate_namedvalues` method from skills to lookup resources, this is essentially a .csv file with value pairs used to populate a python dictionary

this is a good approach for localization of skills, however it is not as straightforward as translating dialog files because the content of these files is not obvious and will depend on the code of a specific skill

using the official wikipedia skill as an example, we need to translate this file that tells the wikipedia package what lang-code to use
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


### Pull Request

TODO

## Corner cases

some skills can not be easily translated and need a pull request to handle this in code:
- they use a library / web api that expects english
- they use a web api that returns english results
- they need some language specific resource not accounted for by the original author

A possible work around is using translation at runtime, the official [wolfram alpha skill](https://github.com/MycroftAI/fallback-wolfram-alpha/blob/20.08/__init__.py#L188) does this, i do the same in my [icanhazdadjoke skill](jokeshttps://github.com/JarbasSkills/skill-icanhazdadjoke)

If you are developing skills here is a template you can use as a starting point

```python
from mycroft.skills.core import MycroftSkill, intent_handler
from google_trans_new import google_translator

class MySkill(MycroftSkill):
    def __init__(self):
        super().__init__(name="MySkill")
        self.translator = google_translator()
        self.tx_cache = {}  # avoid translating twice

    @intent_handler("my_intent.intent")
    def handle_intent(self, message):
        # do something that returns english utterance
        (...)
        # translate response to user language
        translated_utt = self.translate(value)
        self.speak(translated_utt)

    @intent_handler("my_other_intent.intent")
    def handle_english_input_intent(self, message):
        # get the value that needs to be in english but is in user language
        value = message.data["value"]
        # translate it to english
        tx_value = self.translate(value, "en", self.lang)
        
        # do something with tx_value
        eng_utt = "this is in english, but will be spoken in catalan"
        
        # translate response to user language
        translated_utt = self.translate(eng_utt)
        self.speak(translated_utt)

    def translate(self, utterance, lang_tgt=None, lang_src="en"):
        lang_tgt = lang_tgt or self.lang

        # if langs are the same do nothing
        if not lang_tgt.startswith(lang_src):
            if lang_tgt not in self.tx_cache:
                self.tx_cache[lang_tgt] = {}
            # if translated before, dont translate again
            if utterance in self.tx_cache[lang_tgt]:
                # get previous translated value
                translated_utt = self.tx_cache[lang_tgt][utterance]
            else:
                # translate this utterance
                translated_utt = self.translator.translate(utterance, lang_tgt=lang_tgt, lang_src=lang_src).strip()
                # save the translation if we need it again
                self.tx_cache[lang_tgt][utterance] = translated_utt
            self.log.debug("translated {src} -- {tgt}".format(src=utterance, tgt=translated_utt))
        else:
            translated_utt = utterance.strip()
        return translated_utt

```

NOTE: add ```google_trans_new``` to requirements.txt of your skill


### blacklist official skills

mycroft makes it hard to disable official skills, if you delete them they will be automatically reinstalled

If you need to replace an official skill you have to [blacklist it](https://mycroft-ai.gitbook.io/docs/skill-development/faq#how-do-i-disable-a-skill) so it will not load

To blacklist the official skills incompatible with catalan edit your .conf and add the following section, make sure to use the exact names of the skill folders, usually of the format ```github_repo_name.author```

```json
  "skills": {
    "blacklisted_skills": [
          "mycroft-npr-news.mycroftai", 
          "mycroft-fallback-duck-duck-go.mycroftai", 
          "mycroft-joke.mycroftai",
          "fallback-wolfram-alpha.mycroftai"
    ]
  }
```

Why have these been blacklisted:
- npr-new - does not include catalan news, a alternative skill for this causes incompatibilities
- duck duck go - no auto translate support, english input/output only
- joke - no catalan jokes, will trigger but throw error during runtime
- wolfram alpha - includes auto translation, but there is some bug and throws error at runtime


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

This tutorial will use some plugins, in each section you will have the links to these plugins and details on how to configure them

To install a plugin you can use pip, but you need to be inside the mycroft venv!

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


## From source

github install from url using pip

```
mycroft-pip install git+https://github.com/JarbasLingua/jarbas-stt-plugin-chromium
```

or manually
```
git clone https://github.com/JarbasLingua/jarbas-stt-plugin-chromium
cd jarbas-stt-plugin-chromium
mycroft-pip install .
```

# Lingua franca

TODO

- how to translate
- usage

## Updating lingua_franca to catalan

@jmontane translated lingua_franca to catalan, this has not yet been released so you need to manually add these changes

NOTE:
- check [Pull Request#161](https://github.com/MycroftAI/lingua-franca/pull/161), is it merged?
- check [Releases page](https://github.com/MycroftAI/lingua-franca/releases) for lingua_franca, has a new version been released that includes catalan?

You only need to run one of the commands bellow, depending on the current status of the 2 items above, at the time of writing lingua_franca was on version 0.3.1

Catalan is expected to be merged ain version 0.3.2 or 0.4.0

if [Pull Request#161](https://github.com/MycroftAI/lingua-franca/pull/161) was *NOT MERGED*, we need to install lingua_franca from [@softcatala fork](https://github.com/Softcatala/lingua-franca)

```
mycroft-pip install git+https://github.com/Softcatala/lingua-franca
```

if *NEW VERSION NOT RELEASED* and [Pull Request#161](https://github.com/MycroftAI/lingua-franca/pull/161) is *MERGED*, then we need to install lingua_franca from source code

```
mycroft-pip install git+https://github.com/MycroftAI/lingua-franca
```

The changelog for the [releases in lingua_franca](https://github.com/MycroftAI/lingua-franca/releases) should tell you if catalan has been merged, if it was *MERGED* and a *NEW VERSION RELEASED* we need to update lingua franca inside mycroft-core

```
mycroft-pip install lingua_franca -U
```


## Enabling lingua_franca 0.3.X

Mycroft-core is packaged with a older version of lingua_franca, the migration to 0.3.0 has been started and should make it into the next major release of mycroft

0.3.X is incompatible with 0.2.X, which means we need to update mycroft-core to use the catalan translation

NOTE:
- check [Pull Request#2772](https://github.com/MycroftAI/mycroft-core/pull/2772), if it is merged you DO NOT need to make changes described bellow

Run the steps bellow and mycroft will be updated to use lingua_franca 0.3.X
```bash
cd mycroft-core
./stop-mycroft.sh
git remote add chance https://github.com/ChanceNCounter/mycroft-core
git fetch chance
git checkout chance/lf-refactor
./dev_setup.sh
```

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

## Standup word - Desperta

If you use the [naptime skill](https://github.com/MycroftAI/skill-naptime) you can tell mycroft to "go to sleep", when you do this STT will NOT be processed, this is a privacy feature

This stops all calls to Speech to Text system, guaranteeing your voice won't be sent anywhere on an accidental activation.

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

If you have not trained a model do not worry, [@jmontane](https://github.com/jmontane) trained some models which are included in the plugin and should work ok! be sure to adjust sensitivities


```json
  "listener": {
      "stand_up_word": "desperta"
  },
  "hotwords": {
    "desperta": {
        "module": "snowboy_ww_plug",
        "models": [
           {"sensitivity": 0.5, "model_path": "desperta_jm.pmdl"},
           {"sensitivity": 0.5, "model_path": "desperta_jm2.pmdl"}
         ]
    }
  }
```


Now you can wake up mycroft in catalan! "hey mycroft, desperta"


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
  "skills": {
    "blacklisted_skills": [
          "mycroft-npr-news.mycroftai", 
          "mycroft-fallback-duck-duck-go.mycroftai", 
          "mycroft-joke.mycroftai",
          "fallback-wolfram-alpha.mycroftai"
    ]
  },
  "listener": {
      "stand_up_word": "desperta"
  },
  "hotwords": {
     "desperta": {
        "module": "snowboy_ww_plug",
        "models": [
            {"sensitivity": 0.5, "model_path": "desperta_jm.pmdl"},
            {"sensitivity": 0.5, "model_path": "desperta_jm2.pmdl"}
         ]
      }
   }
}
```


# Additional tweaks


## Wake Words

A mycroft wake word is what you need to say to make mycroft start listening

Mycrofts uses [precise](https://github.com/MycroftAI/mycroft-precise) for this, it is a model trained on sounds therefore it does not need "translation", if you want to change the name of mycroft then you need to get 50+ recordings and [train a model](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/wake-word#training-a-wake-word-model)

Some users struggle with training so i recommend you check [Precise Community Repo](https://github.com/MycroftAI/precise-community-data), if you upload your recordings the community will train a model for you! This also means the model will keep improving over time

Keep in the mind the quality of the wake word will depend a lot on the ammout of data used for training

- 20-200 samples - this will work well for personal models, the voices in the training data will be recognized well
- 200-2000 samples - this should work quite well for most voices and different microphones!
- 2000+ samples - the model can't get much better than this, from this point on you want to balance the dataset, try to include the same number of female / male / children voices. Different accents are also worth exploring

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


## Replacing skills

### Wolfram alpha

the official wolfram alpha skill has a lot of bugs and will answer all your queries with an error message if not set to english

You can use my alternative skill, [skill-wolfie](https://github.com/JarbasSkills/skill-wolfie), it will blacklist the default skill automatically and supports all languages (via google translate)

```
msm install https://github.com/JarbasSkills/skill-wolfie
```
### Duck Duck go

the official duck duck go skill has no support for languages other than english

You can use my alternative skill, [skill-duck](
https://github.com/JarbasSkills/skill-ddg), it will blacklist the default skill automatically and supports all languages (via google translate)

```
msm install https://github.com/JarbasSkills/skill-duck
```

### Jokes

the official jokes skill does not have jokes in catalan, you can trigger it (resource files have been translated) but it will not work correcly, it should be blacklisted to avoid conflicts

You can use my alternative skill, [skill-icanhazdadjoke](https://github.com/JarbasSkills/skill-icanhazdadjoke), using the [icanhazdadjoke.com](https://icanhazdadjoke.com/) API, it will blacklist the default skill automatically and supports all languages (via google translate)

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
