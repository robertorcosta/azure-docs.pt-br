---
title: Conjuntos fonéticos de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a fazer os mapas do alfabeto fonético do serviço de fala para o Alfabeto Fonético Internacional (IPA) e quando usar qual conjunto.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675335"
---
# <a name="speech-service-phonetic-sets"></a>Conjuntos fonéticos do serviço de fala

O serviço de fala define alfabetos fonéticos ("conjuntos telefônicos" para abreviar), compostos por sete idiomas; `en-US` `fr-FR` `de-DE` `es-ES`e. `ja-JP` `zh-CN` `zh-TW` Os conjuntos telefônicos de serviço de fala normalmente mapeiam para o <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto <span class="docon docon-navigate-external x-hidden-focus"> </span>Fonético Internacional (IPA) </a>. Os conjuntos de telefones de serviço de fala são usados em conjunto com o [SSML (Speech Synthesis Markup Language,](speech-synthesis-markup.md)linguagem de marcação de síntese de fala), como parte da oferta de serviço text-to-speech. Neste artigo, você aprenderá como esses conjuntos telefônicos são mapeados e quando usar qual conjunto de telefone.

# <a name="en-us"></a>[en-EUA](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentais ingleses

| Exemplo 1 (Início para consoante, palavra inicial para vogal) | Exemplo 2 (Intervocalic para consoante, palavra núcleo medial para vogal) | Exemplo 3 (Coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| hambúrguer /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | guitarra /g ih - t aa **1** r/ | Conjunto de telefone de serviço de fala colocar estresse após a vogal da sílaba estressada |
| inoportuno /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilaridade /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | força de trabalho /w er 1 r k - f ao **2** r s/ | Conjunto de telefone de serviço de fala colocar estresse após a vogal da sílaba sub-estressada |

### <a name="english-vowels"></a>Vogais inglesas

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2 | Exemplo 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**ii**  |                             |
| Ey     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| Eh     | `ɛ`   | **e**muito     | p**e**t   | m**eh** (palavra rara finalmente) |
| ae     | `æ`   | **um**ctive    | c**a**t   | n**ah** (palavra rara finalmente) |
| aa     | `ɑ`   | **o**bstinato | p**o**ppy | r**ah** (palavra rara finalmente) |
| ao     | `ɔ`   | **o**intervalo    | c**au**se | Ut**ah**                    |
| uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| Uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| Ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**homem | f**ew**                     |
| Machado     | `ə`   | **um**ir       | wom**a**n | são**um**                    |

### <a name="english-r-colored-vowels"></a>Vogais inglesas de cor R

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2      | Exemplo 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ouvido**s     | t**ir**amisu   | n**ouvido**   |
| eh r   | `ɛɹ`  | avião **aéreo** | app**ar**ently | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**o seu**e   |
| ay r   | `aɪɹ` | **Terra da ira**  | f**ir**lugar  | ch**oir**  |
| aw r   | `aʊɹ` | **horas**s    | p**deveful**   | é**o nosso**   |
| ao r   | `ɔɹ`  | **ou**ange   | m**ou**al      | remo**s**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **ouvido**th    | b**ir**d       | f**o seu**    |
| ax r   | `ɚ`   |              | tudo**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Semivowels inglês

| `sapi` | `ipa` | Exemplo 1           | Exemplo 2  | Exemplo 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | em**i**em  |           |

### <a name="english-aspirated-oral-stops"></a>Paradas orais aspiradas em inglês

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2   | Exemplo 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | correu**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Paradas nasal inglesas

| `sapi` | `ipa` | Exemplo 1        | Exemplo 2  | Exemplo 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**em, smash   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| Ng     | `ŋ`   |                  | li**n**k   | **sing**    |

### <a name="english-fricatives"></a>Fricativos ingleses

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2        | Exemplo 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**em    | empa**th**y      | mon**th**  |
| Dh     | `ð`   | **th**en    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**ele     | ri**s**k         | fato**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | criança**s**   |
| sh     | `ʃ`   | **sh** e    | abreviatura**ti**em | ru**sh**   |
| zh     | `ʒ`   | **J**acques | **ure**apelação     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | **a-h**a!  |

### <a name="english-affricates"></a>Affricas inglesas

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**em  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>Aproximações inglesas

| `sapi` | `ipa` | Exemplo 1          | Exemplo 2  | Exemplo 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **L**id, g**l**anúncio  | pa**l**ace | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Suprasegmentais franceses

O conjunto telefônico de serviço de fala coloca o estresse após a vogal da sílaba estressada, no entanto; o `fr-FR` conjunto de telefone de serviço de fala não suporta o subestresse IPA 'י'. Se o subestresse IPA for necessário, você deve usar o IPA diretamente.

### <a name="french-vowels"></a>Vogais francesas

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2       | Exemplo 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **um**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| Machado     | `ə`   |               | p**e**tite      | l**e**    |
| Eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Ue     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | mat**em** |
| Iy     | `i`   | **i**dée      | pet**i**te      | sou**eu sou eu**   |
| Oe     | `œ`   | **œu**f       | p**eu**r        |           |
| oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| Oh ~   | `ɔ̃`  | **em**ze      | r**em**deur     | b**em**   |
| Ow     | `o`   | **au**diteur  | b**eau**golpe    | p**ô**    |
| oe ~   | `œ̃ ` | **Un**        | l**un**di       | br**un**  |
| Uw     | `u`   | **ou**raiva   | intr**ou**vable | **uo**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Consoantes francesas

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2     | Exemplo 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parque** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | ser**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**como     | ca**p**                          |
| r      | `ʁ`   | **r**em     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | em**v**entrar  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**Ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | homem**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Apenas para algumas palavras estrangeiras.*

> [!TIP]
> O `fr-FR` conjunto de telefones de serviço de fala não `n‿`suporta `t‿`as `z‿`seguintes ligações francesas, e . Se forem necessários, você deve considerar o uso direto do IPA.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Suprasegmentais alemães

| Exemplo 1 (Início para consoante, palavra inicial para vogal) | Exemplo 2 (Intervocalic para consoante, palavra núcleo medial para vogal) | Exemplo 3 (Coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Conjunto de telefone de serviço de fala colocar estresse após a vogal da sílaba estressada |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computermographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Conjunto de telefone de serviço de fala colocar estresse após a vogal da sílaba sub-estressada |

### <a name="german-vowels"></a>Vogais alemãs

| `sapi` | `ipa`     | Exemplo 1                             | Exemplo 2     | Exemplo 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| Um:     | `aː`      | **Um**ber                              | Maßst**a**b   | Esquema**a**                         |
| a      | `a`       | **Uma**queda                            | B**a**ch      | Agath**um**                         |
| oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1) Fasci**ae** |
| Eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| Machado     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| Iy     | `iː`      | **Eu**corri                              | abb**ie**gt   | Relativitätstheor**ou seja**            |
| Ih     | `ɪ`       | **Eu**nnung                            | s**i**ngen    | Madeira**y**                          |
| Ue     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| Ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| Oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| Uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Homens**ü**                           |
| Uy     | `ʏ`       | **ü**ppig                             | S**y**caule    |                                    |

<a id="de-v-1"></a>
**1** *Apenas em palavras de origem estrangeira, tais como: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Palavra-intially apenas em palavras de origem estrangeira, como **Um**ppointment. Sílaba-in- inicialmente em: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Ditongo alemão

| `sapi` | `ipa`       | Exemplo 1    | Exemplo 2          | Exemplo 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu**forie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Semivowels alemães

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | **locker** |

### <a name="german-consonants"></a>Consoantes alemãs

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2 | Exemplo 3 |
|--|--|--|--|--|
| b | `b` | **Ank B** |  | [<sup>1</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5) Chan**g**e |
| f | `f` | **F**ahrtdauer | com raiva**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**em | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **L**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | **Ring** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| Pf | `pf` | **PF**erd | represa**pf**t | Para**a PF** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | Sagacidade**z** |  |
| ch | `tʃ` | **Tsch**Echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10,</sup>](#de-c-10)`x`[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Apenas em palavras de origem estrangeira, tais como: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *"ch" macio após "e" e "i"*<br>
<a id="de-c-3"></a>
**3** *Apenas em palavras de origem estrangeira, tais como: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Apenas em palavras de origem estrangeira como: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Apenas em palavras de origem estrangeira como: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Palavra-terminalmente apenas em palavras de origem estrangeira como Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Apenas em palavras de origem estrangeira como: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Apenas em palavras de origem estrangeira como: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Apenas em palavras de origem estrangeira, tais como: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *O `x` IPA é um "ch" duro depois de todas as vogais não frontais (a, aa, oh, ow, uh, uw e o ditongo aw).*<br>
<a id="de-c-11"></a>
**11** *O `ç` IPA é um 'ch' macio após vogais frontais (ih, iy, eh, ae, uy, ue, oe, eu também em ditongos ay, oy) e consoantes*<br>
<a id="de-c-12"></a>
**12** *Palavra-inicialmente apenas em palavras de origem estrangeira, tais como: **J**uan. Sílaba-inicialmente também em palavras como: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Consoantes orais alemãs

| `sapi` | `ipa` | Exemplo 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Precisamos adicionar um [telefone\] gs entre duas vogais distintas, exceto que as duas vogais são um ditongo genuíno. Esta consoante oral é uma parada glotal, para obter mais informações, consulte <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">a parada <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>glotal .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Vogais espanholas

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2     | Exemplo 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **um**lto     | c**a**ntar    | cas**um**     |
| i      | `i`   | **i**bérica  | **av i**spa    | imposto**i**     |
| e      | `e`   | **e**lefante | em**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Consoantes espanholas

| `sapi` | `ipa`      | Exemplo 1  | Exemplo 2      | Exemplo 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | sou**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | **Marraque ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | um**ua g**       | tuare**g**     |
| j      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **L**ápiz  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ia**av  | desarro**ll**o |                |
| m      | `m`        | **m**ordem | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| Nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | para**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | um**ar t**       | disque**t**    |
| th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> O `es-ES` conjunto de telefones de serviço speech não `β` `ð`suporta `ɣ`o seguinte IPA espanhol, e . Se forem necessários, você deve considerar o uso direto do IPA.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

O telefone de `zh-CN` serviço speech set para é baseado no telefone nativo <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="tone"></a>Tom

| Tom pinyin | `sapi` | Exemplo de personagem |
|-------------|--------|-------------------|
| mā          | ma 1  | Em 1998                 |
| má          | ma 2  | A iM.                 |
| mə          | ma 3  | O que é isso?                 |
| mà          | ma 4  | Em 1998                 |
| ma          | ma 5  | Em 1998                 |

#### <a name="example"></a>Exemplo

| Caractere | Serviço de Fala                |
|-----------|-------------------------------|
| O que é isso?      | zu 3 - zhi 1 - guan 1 - xi 5 |
| Eu não acho que você        | lei 3 -jin 4                 |
| A iM.       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

O conjunto de `zh-TW` telefone de serviço speech é baseado no conjunto <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do telefone nativo.

### <a name="tone"></a>Tom

| Tom de serviço de fala | Tom bopomofo | Exemplo (palavra) | Telefones de serviço de fala | Bopomofo | Pinyin (のの) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| Em 1998                   | empty         | Em 1998              | O que é isso?                   | O que é isso?       | Zhēn        |
| Em 1998                   | Em 1998             | Em 1998              | O que é isso?                   | O que é isso?      | chá         |
| Em 29 99                   | Em 29 99             | a iM.              | É o que eu disse.                   | É o que eu disse.      | Də də          |
| Em 1998                   | Em 1998             | A iCarros Ai              | O que é isso?                   | O que é isso?      | wàng        |
| ˙                   | ˙             | Eu não vou fazer isso.             | É o que diz.               | É o que diz.  | yṭng zi    |

#### <a name="example"></a>Exemplo

| Caractere | `sapi`   |
|-----------|----------|
| Eu não vou         | É o que eu disse.      |
| Eu não estou        | O que é isso?   |
| Eu não acho que você        | O que é isso? |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

O telefone de `ja-JP` serviço de fala definido para é baseado no telefone nativo <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`mainstress |
| `+`    | `ˌ`subestresse  |

#### <a name="example"></a>Exemplo

| Caractere | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| Eu não vou        | "A i'のののの    | goיwəseji   |
| Iii       | "A.M.D.? | "Jojəwə", |
| A iM       | A iMA.  | sajitecika |

***