---
title: Localização de Player de Mídia do Azure
description: Suporte a vários idiomas para usuários de localidades não inglesas.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727171"
---
# <a name="localization"></a>Localização #

O suporte a vários idiomas permite que os usuários de localidades não inglesas interajam nativamente com o jogador. Player de Mídia do Azure criará uma instância com um dicionário global de idiomas, que localizará as mensagens de erro com base no idioma da página. Um desenvolvedor também pode criar uma instância de Player com uma linguagem de conjunto forçada. O idioma padrão é inglês (EN).

> [!NOTE]
> Esse recurso ainda está passando por alguns testes e, como tal, está sujeito a bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

O Player de Mídia do Azure atualmente dá suporte aos seguintes idiomas com seus códigos de idioma correspondentes:

| Linguagem            | Código | Linguagem                | Código   | Linguagem                | Código         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Inglês {padrão}   | en   | Croata                | hr     | Romeno                | ro           |
| Árabe              | ar   | Húngaro               | hu     | Eslovaco                  | sk           |
| Búlgaro           | bg   | Indonésio              | id     | Slovene                 | sl           |
| Catalão             | AC   | Islandês               | is     | Sérvio - Cirílico      | Sr-Cyrl-CS   |
| Tcheco               | cs   | Italiano                 | it     | Sérvio - latino         | sr-latn-rs   |
| Dinamarquês              | da   | Japonês                | ja     | Russo                 | ru           |
| Alemão              | de   | Cazaque                  | kk     | Sueco                 | sv           |
| Grego               | el   | Coreano                  | ko     | Tailandês                    | th           |
| Espanhol             | es   | Lituano              | lt     | Tagalo                 | TL           |
| Estoniano            | et   | Letão                 | lv     | Turco                 | tr           |
| Basco              | UE   | Ringgit               | ms     | Ucraniano               | uk           |
| Farsi               | ativo   | Norueguês-BokmÃ ¥ l     | nb     | Urdu                    | uma           |
| Finlandês             | fi   | Holandês                   | nl     | Vietnamita              | vi           |
| Francês              | fr   | Norueguês - Nynorsk     | nn     | Chinês-simplificado    | zh-hans      |
| Galego            | gl   | Polonês                  | pl     | Chinês-tradicional   | zh-hant      |
| Hebraico              | he   | Português - Brasil     | pt-br  |                         |              |
| Híndi               | hu   | Português - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Se você não quiser que nenhuma localização ocorra, você deve forçar o idioma para inglês

## <a name="next-steps"></a>Próximas etapas ##

- [Guia de início rápido Player de Mídia do Azure](azure-media-player-quickstart.md)