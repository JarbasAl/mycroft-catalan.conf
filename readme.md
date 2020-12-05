# Mycroft in catalan

Bellow is a step by step guide to configure mycroft in catalan

# mycroft.conf

You want to edit the user config, this is under ```~/.mycroft/mycroft.conf```

Note that is the home directory of the user running mycroft, in a mark one this means ```/home/mycroft/.mycroft/mycroft.conf``` NOT ```/home/pi/.mycroft/mycroft.conf```

You can learn more about the .conf in the [docs](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/mycroft-conf)

## Lang

First we must set the global language of mycroft, this is what will be used to load skills and resource files

SPECIAL NOTES:
- any skill with a single file not translated will not load, all files must be translated
- the language code must match exactly! I submitted a [PR to improve this](https://github.com/MycroftAI/mycroft-core/pull/1335) back in 2017, but it was completely ignored
- this must match the [lang code for mycroft-core resource files](https://github.com/MycroftAI/mycroft-core/tree/dev/mycroft/res/text), in our case ```ca-es```
- mycroft translate uses lang code ```ca``` not ```ca-es```, which means skills translated in [translate.mycroft.ai](https://translate.mycroft.ai/) will not work

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
- chromium STT expects language code `ca-es`
- for catalan we get a conflict because of lang code used in skills is ```ca```
- most STT engines [do not allow overriding the language code](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L33) unless implemented manually

The best way to workaround this is using plugins that support languages correctly

### Installing plugins

To install a plugin you can use pip, this needs to be inside the mycroft venv!

You can do this in several ways

#### Manual install

- manually activate the .venv, ```source mycroft-core/.venv/bin/activate```
- use the mycroft script alias (might not be available) ```mycroft-venv-activate```
- run the script explicitly ```mycroft-core/venv-activate.sh```

then run the install command
```
pip install jarbas-stt-plugin-chromium
```

#### Mycroft-pip

mycroft provides a pip wrapper to do the above for you, this might be or not available in your platform

- use the mycroft alias (might not be available) ```mycroft-pip install jarbas-stt-plugin-chromium```
- run the script explicitly ```mycroft-core/bin/mycroft-pip install jarbas-stt-plugin-chromium```
- in a mark1 you will need to use sudo ```sudo mycroft-pip install jarbas-stt-plugin-chromium```

### STT Config

Let's configure the plugin and override the language code

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

### List of plugins that support catalan

- [google chromium](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium)
- [vosk](https://github.com/JarbasLingua/jarbas-stt-plugin-vosk)
- [pocketsphinx](https://github.com/JarbasLingua/jarbas-stt-plugin-pocketsphinx)

### List of core engines that support catalan 

- mycroft - only works if global lang is ```ca-es```
- google cloud - best accuracy, not free, allows [overriding global language](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L109)
- google - to be used you need to add a api key, but keys are no longer issued, see details in [google chromium plugin](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium), this is effectively useless

## TTS - Text to Speech

once more we need to select an engine that supports catalan, by default mycroft only supports english

### List of core engines that support catalan

- espeak - needs to be manually installed
- festival - needs to be manually installed
- gtts - can be set from web interface, breaks often (uses google translate undocumented web api)
- google cloud - best accuracy, not free

### List of plugins that support catalan

Installing plugins was described in detail in the STT section

- [catotron](https://github.com/JarbasLingua/jarbas-tts-plugin-catotron) - best quality, but slow
- [softcatala](https://github.com/JarbasLingua/jarbas-tts-plugin-softcatala) - same as festival, but running in a server
- [voicerss](https://github.com/JarbasLingua/jarbas-tts-plugin-voicerss) - requires api key (free)
- [responsive voice](https://github.com/JarbasLingua/jarbas-tts-plugin-responsivevoice) - uses espeak for catalan

### Installing festival

Festival is supported by mycroft-core, but you need to install it manually

```
sudo apt-get -y install festival festvox-ca-ona-hts lame
```

### TTS Config

Let's configure mycroft to use festival

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
