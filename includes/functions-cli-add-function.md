---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673213"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função (HttpExample) e o argumento `--template` especifica o gatilho da função (HTTP). 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new` cria um arquivo de código HttpExample.cs.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.
::: zone-end