# Mycroft en català
Aquesta és una guia completa, pas per pas, per a configurar Mycroft en català.

Nota: aquest document és una traducció de l'original en anglès que podeu consultar [aquí](readme.md).

- [Traducció del nucli (core)](#traducció-del-nucli-core)
- [Traducció de les habilitats (skills)](#traducció-de-les-habilitats-skills)
  * [translate.mycroft.ai](#translatemycroftai)
  * [Exportació manual des de Pootle](#exportació-manual-des-de-pootle)
  * [Traducció manual](#traducció-manual)
    + [Codi de llengua (locale)](#codi-de-llengua-locale)
    + [Fitxers de recursos](#fitxers-de-recursos)
    + [Sol·licitud d'entrada (pull request)](#sollicitud-dentrada-pull-request)
  * [Casos especials](#casos-especials)
  * [Substitució d'habilitats](#substitució-dhabilitats)
    + [Habilitat de bromes (jokes)](#habilitat-de-bromes--jokes-)
    + [Habilitat de notícies](#habilitat-de-notícies)
      - [Configuració del motor d'àudio](#configuració-del-motor-dàudio)
    + [blacklist official skills](#blacklist-official-skills)
- [Instal·lació d'extensions (plugins)](#installació-dextensions-plugins)
  * [Instal·lació manual](#installació-manual)
  * [Mycroft-pip](#mycroft-pip)
  * [A partir del codi font](#a-partir-del-codi-font)
- [Lingua franca](#lingua-franca)
- [mycroft.conf](#mycroftconf)
  * [Llengua (lang)](#llengua-lang)
    + [Configuració de la llengua](#configuració-de-la-llengua)
  * [Reconeixement de la parla (STT, Speech to Text)](#reconeixement-de-la-parla-stt-speech-to-text)
    + [Llista de motors del nucli que admeten el català](#llista-de-motors-del-nucli-que-admeten-el-català)
    + [Llista d'extensions que admeten el català](#llista-dextensions-que-admeten-el-català)
    + [Configuració de l'STT](#configuració-de-l-stt)
      - [Instal·lació de jarbas-stt-plugin-chromium](#installació-de-jarbas-stt-plugin-chromium)
  * [Síntesi de veu (TTS, Text to Speech)](#síntesi-de-veu--tts-text-to-speech)
    + [Llista de motors del nucli que admeten el català](#llista-de-motors-del-nucli-que-admeten-el-català-1)
    + [Llista d'extensions que admeten el català](#llista-dextensions-que-admeten-el-català-1)
    + [Configuració del motor TTS](#configuració-del-motor-tts)
      - [Instal·lació de festival](#installació-de-festival)
  * [Paraules d'activació (wake words)](#paraules-dactivació-wake-words)
    + [Precise 0.3](#precise-03)
    + [Precise 0.2](#precise-02)
    + [Paraula d'activació - Ei ordinador](#paraula-dactivació---ei-ordinador)
    + [Paraula despertadora (stand up) - Desperta](#paraula-despertadora-stand-up---desperta)
- [Configuració final](#configuració-final)


# Traducció del nucli (core)

Cal traduir alguns fitxers de mycroft-core. Aquests fitxers s'utilitzen principalment en els diàlegs predeterminats.

Podeu trobar aquests fitxers a [mycroft-core/mycroft/res/text](https://github.com/MycroftAI/mycroft-core/tree/dev/mycroft/res/text)

- Copieu la carpeta ```en-us``` a  ```ca-es```
- Traduïu qualsevol fitxer que encara no estigui traduït

NOTA: Els fitxers de recursos de mycroft-core ja es van traduir al català, però amb el temps és possible que s'hagin creat fitxers nous o hagin canviat els anteriors i ara calgui fer o actualitzar-ne la traducció.

# Traducció de les habilitats (skills)

El primer que hauríeu de fer és traduir habilitats, altrament encara que Mycroft us entengui, només us respondrà missatge d'error perquè no sabrà què ha de fer.

La traducció d'habilitats de vegades no és senzilla, hi ha molts casos especials.

NOTES ESPECILAS:
- Qualsevol habilitat que tingui un fitxer sense traduir no es carregara. Cal que tots els fitxers estiguin traduïts.
- El codi de llengua ha de coincidir de forma exacta amb el valor global configurat! Vaig enviar una [PR per a millorar-ho](https://github.com/MycroftAI/mycroft-core/pull/1335) l'any 2017, però fou ignorada completament, i la vaig tancar (sense fusionar) després de 3 anys.
- El portal de traducció de Mycroft usa el codi de llengua ```ca```, en comptes de ```ca-es```, això significa que les habilitats traduïdes a [translate.mycroft.ai](https://translate.mycroft.ai/) no funcionaran de sèrie. 

## translate.mycroft.ai

La millor manera de traduir habilitats és usant la plataforma de traducció de Mycroft, aneu a [translate.mycroft.ai](https://translate.mycroft.ai/) i ajudeu a traduir l'habilitat de la botiga.

Després cal que espereu que Mycroft (l'empresa) enviï les traduccions als repositoris de les habilitats.

En el cas del català hi ha un problema, perquè la plataforma de traducció usa el codi de llengua  ```ca``` en comptes de ```ca-es```. Vegueu més avall alguna solució.

## Exportació manual des de Pootle

Com s'ha indicat més amunt, les habilitats traduïdes a [translate.mycroft.ai](https://translate.mycroft.ai/) no funcionaran perquè usen el codi de llengua ```ca```. Però podeu un script personalitzat per a exportar les cadenes del Pootle i posar-les amb el codi de llengua ```ca-es```.

Simplement:
- Cloneu el repositori [mycroft-update-translations](https://github.com/jmontane/mycroft-update-translations) en una carpeta de treball
```
git clone https://github.com/jmontane/mycroft-update-translations
```
- Si cal, editeu el fitxer mycroft-update-translations.py i canvieu-ne els paràmetres. Per defecte, tradueix les habilitats de la carpeta ```/opt/mycroft/skills/```

- Executeu l'script
```
cd mycroft-update-translations
./mycroft-update-translations
```

## Traducció manual

### Codi de llengua (locale)

TODO

### Fitxers de recursos

Algunes habilitats necessiten usar valors diferents en el codi depenent de la llengua. Una aproximació habitual és usar el mètode `translate_namedvalues` des de les habilitats per a trobar recursos. Això és, essencialment, un fitxer .csv amb parells de valors que s'usen per a alimentar de dades un diccionari de Python.

Aquesta és una bona aproximació per a la localització d'habilitats, però no és tan senzill com traduir els fitxers de diàleg, perquè el contingut d'aquests fitxer no és obvi i depèn del codi de l'habilitat en particular.

Si usem l'habilitat de la Viquipèdia oficial com a exemple, cal traduir aquest fitxer que diu al paquet wikipedia quin codi de llengua ha d'usar:
```
- dialog
  - en-us
    - wikipedia_lang.value
  - ca-es
    - wikipedia_lang.value
```

El contingut del fitxer ```wikipedia_lang.value```  del directori ```en-us``` és:
```
# Wikipedia language code used to service queries in the active language
code,en
```

El contingut del fitxer ```wikipedia_lang.value```  del directori ```ca-es``` és:
```
# Codi de llengua de Viquipèdia usat per a fer consultes en la llengua activa
code,ca
```

Això es pot usar en el codi d'aquesta manera:
```
data = self.translate_namedvalues("wikipedia_lang")
wiki.set_lang(data["code"])
``` 

Si el fitxer anterior es tradueix com a ```code,ca-es``` l'habilitat no funcionarà. Com a traductor no teniu cap manera de saber-ho sense revisar el codi. Per tant, recomano deixar un comentar amb un enllaç a documentació rellevant. Com podeu veure en els exemples anteriors, les línies que comencen amb # s'ignoren.

### Sol·licitud d'entrada (pull request)

TODO

## Casos especials

Algunes habilitats no es poden traduir fàcilment i necessiten canvis en el codi per a gestionar-ho:
- usen una biblioteca o API web que espera contingut en anglès
- usen una biblioteca o API web que retora resultats en anglès
- necessiten algun recurs lingüístic específic que no s'ha tingut present per l'autor original

Una aproximació possible per a solucionar-ho és usar traducció en temps d'execució, l'habilitat [wolfram alpha skill](https://github.com/MycroftAI/fallback-wolfram-alpha/blob/20.08/__init__.py#L188) oficial ho fa. Faig el mateix en l'[habilitat icanhazdadjoke](jokeshttps://github.com/JarbasSkills/skill-icanhazdadjoke)

Si desenvolupeu habilitats aquí teniu una plantila que podeu usar com a punt de partida

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
        # fa alguna cosa qeu retorna una frase en anglès
        (...)
        # tradueix la resposta a la llengua de l'usuari
        translated_utt = self.translate(value)
        self.speak(translated_utt)

    @intent_handler("my_other_intent.intent")
    def handle_english_input_intent(self, message):
        # obté el valor que necessita ser en anglès però és la llengua de l'usuari
        value = message.data["value"]
        # tradueix-lo a l'anglès
        tx_value = self.translate(value, "en", self.lang)
        
        # fa alguna cosa amb tx_value
        eng_utt = "this is in english, but will be spoken in catalan"
        
        # tradueix la resposta a la llengua de l'usuari
        translated_utt = self.translate(eng_utt)
        self.speak(translated_utt)

    def translate(self, utterance, lang_tgt=None, lang_src="en"):
        lang_tgt = lang_tgt or self.lang

        # si les llengües són les mateixes, no facis res
        if not lang_tgt.startswith(lang_src):
            if lang_tgt not in self.tx_cache:
                self.tx_cache[lang_tgt] = {}
            # si ja s'ha traduït, no ho tornis a traduir
            if utterance in self.tx_cache[lang_tgt]:
                # get previous translated value
                translated_utt = self.tx_cache[lang_tgt][utterance]
            else:
                # tradueix aquesta frase
                translated_utt = self.translator.translate(utterance, lang_tgt=lang_tgt, lang_src=lang_src).strip()
                # desa la traducció per si la necessitem una altra vegada
                self.tx_cache[lang_tgt][utterance] = translated_utt
            self.log.debug("translated {src} -- {tgt}".format(src=utterance, tgt=translated_utt))
        else:
            translated_utt = utterance.strip()
        return translated_utt

```

NOTE: afegiu ```google_trans_new``` al fitxer requirements.txt de la vostra habilitat.


## Substitució d'habilitats


### Habilitat de bromes (jokes)

L'habilitat oficial jokes encara no té bromes en català, podeu activar l'habilitat (perquè els fitxers s'han traduït), però no funcionarà correctament, hauríeu de blocar-la  per a evitar conflictes.

Podeu usar l'habilitat alternativa, [skill-icanhazdadjoke](https://github.com/JarbasSkills/skill-icanhazdadjoke), que usa l'API de [icanhazdadjoke.com/](https://icanhazdadjoke.com/), blocarà l'habilitat predeterminada automàtica i admet moltes llengües (mitjançant el traductor de Google).

```
msm install https://github.com/JarbasSkills/skill-icanhazdadjoke
```

### Habilitat de notícies

Per a funcionar en català ens cal trobar un proveïdor de notícies en català.

[Aquesta sol·licitud d'entrada](https://github.com/MycroftAI/skill-npr-news/pull/102) afegeix [CCMA Catalunya Informació](https://www.ccma.cat/catradio/directe/catalunya-informacio/), però està aturada per un error en el motor de Mycroft.

Podeu instal·lar l'habilitat [skill-news](https://github.com/JarbasLingua/skill-news), blocarà l'habilitat predeterminada i ja admet el català.

NOTA: això és temporal, quan Mcyroft corregeixi el problema retiraré aquesta habilitat, però intentaré fer-ho de forma transparent, desblocant l'habilitat de notícies predeterminada, no caldrà que feu res.

```
msm install https://github.com/JarbasLingua/skill-news
```

#### Configuració del motor d'àudio

De forma predeterminada, Mycroft usa un motor d'àudio simple que té alguns problemes amb fluxos http. Ens cal que Mycroft usi el motor ```vlc```, a més, això permetrà que altres habilitats funcionin correctament.

```
sudo apt-get install vlc
```

Editeu el vostre fitxer ```mycroft.conf``` i afegiu-hi el següent:

```
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

Mycroft fa difícil de desactivar les habilitats oficials, encara que les esborreu es tornen a instal·lar automàticament.

Si voleu substituir una habilitat oficial, cal que la poseu en la llista negra, que la [bloqueu](https://mycroft-ai.gitbook.io/docs/skill-development/faq#how-do-i-disable-a-skill), així Mycroft no la carregarà.

Per a blocar les habilitats oficials que no són compatibles amb el català, editeu el fitxer ```mycroft.conf``` i afegiu la secció següent. Assegureu-vos d'usar els noms exactament igual com apareixen en la carpeta d'habilitats. Habitualment en el format ```nom_repositori_a_github.autor```

```
  "skills": {
    "blacklisted_skills": [
          "mycroft-npr-news.mycroftai", 
          "mycroft-fallback-duck-duck-go.mycroftai", 
          "mycroft-joke.mycroftai"
    ]
  }
```


Si esteu escrivint una habilitat que explícitament substitueix una habilitat oficial, podeu fer el següent en Python:


```
from mycroft.messagebus.message import Message
from mycroft.configuration import LocalConf, USER_CONFIG


class MyAlternativeSkill(MycroftSkill):

    def initialize(self):
        self.blacklist_default_skill()

    def blacklist_default_skill(self):
        # carrega la llista d'habilitats que actualment estan blocades
        blacklist = self.config_core["skills"]["blacklisted_skills"]
        
        # comprova el nom de la carpeta (skill_id) de l'habilitat que voleu substituir
        skill_id = "mycroft-skill-to-replace.mycroftai"
        
        # afegeix l'habilitat a la llista negra
        if skill_id not in blacklist:
            self.log.debug("Blacklisting official mycroft skill")
            blacklist.append(skill_id)
            
            # carrega el fitxer de configuració de l'usuari (~/.mycroft/mycroft.conf)
            conf = LocalConf(USER_CONFIG)
            if "skills" not in conf:
                conf["skills"] = {}

            # actualitza el camp blacklist
            conf["skills"]["blacklisted_skills"] = blacklist
            
            # desa el fitxer de configuració de l'usauri
            conf.store()

        # indica a al servei d'intents que descarrega l'habilitat en cas que estigués carregada
        # això hauria d'evitar la necessitat de reiniciar
        self.bus.emit(Message("detach_skill", {"skill_id": skill_id}))

```

# Instal·lació d'extensions (plugins)

Aquest tutorial usa algunes extensions. En cada secció teniu enllaços a aquestes extensions i detalls sobre com configurar-les.

Per a instal·lar una extensió, podeu usar ```pip```, però cal que esteu en l'entorn virtual (venv) de Mycroft!

Podeu fer-ho de diverses maneres.

## Instal·lació manual

- Activeu el .venv manualment, ```source mycroft-core/.venv/bin/activate```.
- Useu l'script àlies mycroft (pot ser no és disponible) ```mycroft-venv-activate```.
- Executeu l'script explícitament ```mycroft-core/venv-activate.sh```.

Després executeu l'ordre d'instal·lació:

```
pip install jarbas-stt-plugin-chromium
```

## Mycroft-pip

Mycroft proporciona un embolcall de pip per a facilitar-vos la part anterior, això potser no és disponible en la vostra plataforma.

- Useu l'àlies de Mycroft (potser no és disponible) ```mycroft-pip install jarbas-stt-plugin-chromium```.
- Executeu l'script explícitament ```mycroft-core/bin/mycroft-pip install jarbas-stt-plugin-chromium```.
- En un Mark I cal que useu sudo ```sudo mycroft-pip install jarbas-stt-plugin-chromium```.


## A partir del codi font

Instal·lació des de Github a partir d'un URL usant ```pip```:

```
mycroft-pip install git+https://github.com/JarbasLingua/jarbas-stt-plugin-chromium
```

O manualment:
```
git clone https://github.com/JarbasLingua/jarbas-stt-plugin-chromium
cd jarbas-stt-plugin-chromium
mycroft-pip install .
```

# Lingua franca

TODO

- Com traduir
- Ús
- Actualitzar el nucli per a usar la bifurcació

# mycroft.conf

Si voleu editar la configuració d'usuari, el fitxer és a ```~/.mycroft/mycroft.conf```

Tingueu present que aquest és el directori d'usuari per a l'usuari que executa Mycroft, en un Mark I això significa ```/home/mycroft/.mycroft/mycroft.conf``` NO PAS ```/home/pi/.mycroft/mycroft.conf```

Teniu més informació sobre el fitxer mycroft.conf en la [documentació (en anglès)](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/mycroft-conf)

Després d'editar el fitxer mycroft.con cal que reinicieu Mycroft perquè els canvis tinguin efecte.

## Llengua (lang)

Primer de tot cal definir la llengua global de Mycroft. Aquesta llengua és la que s'usarà en carregar les habilitats i els fitxers de recursos.

NOTES ESPECIALS:
- El codi ha de coincidir amb el [codi de llengua del fitxers de recursos de nucli de mycroft-core](https://github.com/MycroftAI/mycroft-core/tree/dev/mycroft/res/text), en el nostre cas ```ca-es```.
- El codi de llengua ha de coincidir de forma exacta! Vaig enviar una [PR per a millorar-ho](https://github.com/MycroftAI/mycroft-core/pull/1335) l'any 2017, però es va ingorar completament.
- Els habilitats també usaran aquest codi de llengua per a cerca fitxers de recursos.
- Els termes "codi de llengua" i "lang code" és refereixen als [codis de llengua del lBCP-47 (en anglès)](https://en.wikipedia.org/wiki/IETF_language_tag), habitualment usats pels ordinadors per a identificar una llengua i regió. Farem servir el codi de llengua "ca-es" per a referir-nos a la variant de català parlada a l'estat espanyol. Altres exemples familiars són "en-us" (anglès/Estats Units) i "es-es" (espanyol/Espanya)".

### Configuració de la llengua

```
{
  "lang": "ca-es"
}
```

## Reconeixement de la parla (STT, Speech to Text)

Hem de configurar un motor que admeti el català. Google STT admet el català, i aquest és el que usa el motor de Mycroft.

NOTES ESPECIALS:
- El motor de Mycrfot hauria de funcionar de sèrie amb la majoria de llengües.
- El motor chromium STT espera el codi de llengua`ca-es`, no funciona si useu el codi de llengua `ca`.
- La majoria de motors STT [no permeten personalitzar el codi de llengua de forma independent a la llengua global de Mycroft](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L33) si no s'implementa manualment.

El millor que es pot fer és usar extensions que admetin llengües correctament.

### Llista de motors del nucli que admeten el català 

- mycroft: Només funciona si la llengua global definida és ```ca-es```, però pot deixar de funcionar perquè funciona de casualitat. Vegeu [mycroft chat](https://chat.mycroft.ai/community/pl/j3bafyzjkpd3ifioihhwe84e6h) per a més detalls.
- google cloud: Bona qualitat, no és lliure. Permet [especificar un codi de llengua específic](https://github.com/MycroftAI/mycroft-core/blob/dev/mycroft/stt/__init__.py#L109)
- google: Per a usar-lo cal una clau API, però ja no se n'emeten claus. Vegeu els detalls a l'[extensió google chromium](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium). A tots els efectes és inútil.

### Llista d'extensions que admeten el català

Teniu instruccions detallades sobre la instal·lació d'extensions a la [secció d'extensions](#installing-plugins)

- [google chromium](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium)
- [vosk](https://github.com/JarbasLingua/jarbas-stt-plugin-vosk)
- [pocketsphinx](https://github.com/JarbasLingua/jarbas-stt-plugin-pocketsphinx)


### Configuració de l'STT

#### Instal·lació de jarbas-stt-plugin-chromium

Simplement evitem el motor STT de Mycroft perquè pot deixar de funcionar en qualsevol moment.

```
mycroft-pip install jarbas-stt-plugin-chromium
```

Configureu l'[extensió google chromium](https://github.com/JarbasLingua/jarbas-stt-plugin-chromium) i definiu el codi de llengua específic per del motor.

```
{
  "stt": {
    "module": "chromium_stt_plug",
    "chromium_stt_plug": {
        "lang": "ca-ES"
    }
  }
}
```

## Síntesi de veu (TTS, Text to Speech)

Una altra vegada, hem de triar un motor que admeti el català, per defecte el motor de Mycroft només admet l'anglès.

### Llista de motors del nucli que admeten el català

- espeak: cal instal·lar-lo manualment. La veu és massa robòtica!
- festival: cal instal·lar-lo manualment. La qualitat és acceptable.
- gtts: es pot definir des de la interfície web de Mycroft. Sovint té problemes i deixa de funcionar (usa una API no documentada del traductor de Google).

### Llista d'extensions que admeten el català

Teniu instruccions detallades per a fer-ne la instal·lació en la [secció d'extensions](#installing-plugins)

- [catotron](https://github.com/JarbasLingua/jarbas-tts-plugin-catotron): la millor qualitat, però és lent.
- [softcatala](https://github.com/JarbasLingua/jarbas-tts-plugin-softcatala): mateixa qualitat que festival, però funciona en un servidor.
- [voicerss](https://github.com/JarbasLingua/jarbas-tts-plugin-voicerss): requereix una clau API (gratuïta).
- [responsive voice](https://github.com/JarbasLingua/jarbas-tts-plugin-responsivevoice): usa espeak per al català. La veu és massa robòtica.

### Configuració del motor TTS

Configurem Mycroft perquè usi festival

#### Instal·lació de festival

El nucli de Mycroft (mycrot-core) admet festival, però cal instal·lar-lo manualment en el sistema operatiu:

```
sudo apt-get -y install festival festvox-ca-ona-hts lame
```


```
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

## Paraules d'activació (wake words)

Una paraula d'activació és el que heu de dir perquè Mycroft comenci a escoltar.

Mycroft usa el motor [precise](https://github.com/MycroftAI/mycroft-precise) per a fer això. És un model entrenat amb sons, per tant no requereix una "traducció". Si voleu canviar el nom de Mycroft aleshores cal que feu més de 50 enregistraments i [entreneu un model](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/wake-word#training-a-wake-word-model)

Alguns usuaris tenen traça amb l'entreament de models, per tant us recomano de revisar el [repositori comuntari de Precise](https://github.com/MycroftAI/precise-community-data), si hi pugeu els enregistraments la comunitat us entrenarà un model! Això també significa que el model millorarà amb el temps.

Hi ha dues paraules d'activació que cal configurar. El "nom" de Mycroft, i la paraula despertadora "desperta" (teniu més informació sobre això més avall)

### Precise 0.3

Mycroft baixa el [binari de precise](https://github.com/MycroftAI/precise-data/tree/dist) en temps d'execució. Hi ha la versió 0.2 i la 0.3. Per defecte Mycroft usa la versió 0.2, però alguns models estan entrenats per a la 0.3 (la versió de github)

```
 "precise": {
   "dist_url": "https://github.com/MycroftAI/precise-data/raw/dist/{arch}/latest-dev"
 }
```

### Precise 0.2
Si voleu tornar a la versió 0.2

```
 "precise": {
   "dist_url": "https://github.com/MycroftAI/precise-data/raw/dist/{arch}/latest"
 }
```

### Paraula d'activació - Ei ordinador

Per a aquest exemple farem servir un model comunitari, [Ey Ordenador](https://github.com/MycroftAI/Precise-Community-Data/tree/master/heycomputer/es)

Baixeu el model d'[aquí](https://github.com/MycroftAI/Precise-Community-Data/blob/master/heycomputer/models/heycomputer-es-0.3.0-20190815-eltocino.tar.gz) i descomprimiu els fitxers a ```~/.mycroft/precise/models``` (o a qualsevol altra ubicació que vulgueu)

Podeu canviar 2 paràmetres per a millorar la detecció amb la vostra veu
-  ```sensitivity``` Més alt -> més precisió
-  ```trigger_level``` Més alt -> més retard i menys sensible


```
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

### Paraula despertadora (stand up) - Desperta

Si useu l'habilitat [naptime skill](https://github.com/MycroftAI/skill-naptime) podeu dir al Mycroft "vés a dormir", si ho feu, no es processa el reconeixement de la parla STT. Això és una funcionalitat de privadesa.

Això atura les crides al sistema de reconeixement de la parla STT, i garanteix que la vostra veu no s'envia enlloc per una activacio accidental.

Però això vol dir que heu de traduir aquesta paraula d'activació, per defecte és en anglès: "wake up"

NOTES ESPECIALS: 
- Mentre dorm, Mycroft encara reaccionarà a la paraula d'activació, però en comptes d'activar el motor STT escoltarà per la segona paraula despertadora.
- Cal que digueu "ei mycroft, desperta" per a reactivar el STT.
- Atès que això requereix 2 paraules d'activació seguides, la precisió de l'activació pot ser inferior i s'accepten més falses activacions.

Per a traduir tot això al català usem l'[extensió snowboy](https://github.com/JarbasLingua/jarbas-wake-word-plugin-snowboy):
- És molt lleugera
- Es poden carregar diversos models simultàniament.
- Podeu [entrenar un model personal](https://snowboy.kitt.ai/) per a cada membre de la casa.
- Malauradament, snowboy tanca el 31 de desembre de 2020, entreneu models mentre pugueu!!!

Instal·leu l'extensió amb:

```
mycroft-pip install jarbas-wake-word-plugin-snowboy
```

Ara configurem Mycroft perquè usi això:

```		
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

Si no heu entrenat un model, no patiu, [@jmontane](https://github.com/jmontane) va entrenar alguns models que s'inclouen en l'extensió i que haurien de funcionar correctament! Assegureu-vos d'ajustar-ne les precisions.
			
```
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


Ara podeu activar el Mycroft en català! "ei ordinador, desperta"


# Configuració final

Aquest ha estat un tutorial força llarg, però si heu seguit totes les instruccions, el vostre fitxer ```mycroft.conf``` hauria de ser similar a aquest:


```
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
          "mycroft-joke.mycroftai"
    ]
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

