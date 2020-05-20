---
title: Definição de esquema de aplicativo
description: O aplicativo LUIS é representado no `.json` ou `.lu` e inclui todas as intenções, entidades, exemplos de declarações, recursos e configurações.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684347"
---
# <a name="app-schema-definition"></a>Definição de esquema de aplicativo

O aplicativo LUIS é representado no `.json` ou `.lu` e inclui todas as intenções, entidades, exemplos de declarações, recursos e configurações.

## <a name="format"></a>Formatar

Ao importar e exportar o aplicativo, escolha `.json` ou `.lu` .

|Formatar|Informações|
|--|--|
|`.json`| Formato de programação padrão|
|`.lu`|Com suporte nas [ferramentas de bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)da estrutura de bot.|

## <a name="version-7x"></a>Versão 7. x

* Mudando para a versão 7. x, as entidades são representadas como entidades de aprendizado de máquina aninhadas.
* Suporte para a criação de entidades de aprendizado de máquina aninhadas com `enableNestedChildren` Propriedade nas seguintes APIs de criação:
    * [Adicionar rótulo](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Adicionar rótulo de lote](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Examinar rótulos](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Sugerir consultas de ponto de extremidade para entidades](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Sugerir consultas de ponto de extremidade para tentativas](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>Versão 6. x

* Passando para a versão 6. x, use a nova [entidade Machine-Learning](reference-entity-machine-learned-entity.md) para representar suas entidades.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Versão 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Próximas etapas

* Migrar para as [APIs de criação v3](luis-migration-authoring-entities.md)