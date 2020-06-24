---
title: Conceitos e definições de comandos personalizados – serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá sobre os conceitos e definições de aplicativos de comandos personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307367"
---
# <a name="custom-commands-concepts-and-definitions"></a>Conceitos e definições de comandos personalizados

Este artigo serve como referência para conceitos e definições de aplicativos de comandos personalizados.

## <a name="commands-configuration"></a>Configuração de comandos
Os comandos são os blocos de construção básicos de um aplicativo de comandos personalizados. Um comando é um conjunto de configurações necessárias para concluir uma tarefa específica definida por um usuário.

### <a name="example-sentences"></a>Sentenças de exemplo
Exemplo declarações são os exemplos de conjunto que o usuário pode dizer para disparar um comando específico. Observe que você precisa fornecer apenas uma amostra de declarações e não uma lista exaustiva. 

### <a name="parameters"></a>Parâmetros
Os parâmetros são informações requeridas pelos comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

### <a name="completion-rules"></a>Regras de conclusão
Série de regras a serem executadas quando o comando estiver pronto para ser cumprido, ou seja, quando todas as condições das regras forem satisfeitas.

### <a name="interaction-rules"></a>Regras de interação
Regras adicionais para lidar com situações mais específicas ou complexas. Você pode adicionar validações adicionais ou configurar recursos avançados, como confirmações ou correção em uma etapa. Você também pode criar suas próprias regras de interação personalizadas.

## <a name="parameters-configuration"></a>Configuração de parâmetros

Os parâmetros são informações requeridas por comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

### <a name="name"></a>Nome
Um parâmetro é identificado pela propriedade Name. Você sempre deve fornecer um nome descritivo para um parâmetro. Um parâmetro pode ser referenciado em diferentes seções, como durante a construção de condições, respostas de fala ou outras ações.
 
### <a name="isglobal"></a>Caso IsGlobal
Caixa de seleção que indica se o escopo desse parâmetro é compartilhado entre todos os comandos no aplicativo. Se um parâmetro for global, seu valor potencialmente poderá ser fornecido de qualquer escopo de comando e, uma vez atribuído um valor, ele poderá ser referenciado de qualquer um dos comandos. 

### <a name="required"></a>Obrigatório
Caixa de seleção que indica se um valor para esse parâmetro é necessário para preenchimento/conclusão de comando. Você deve configurar as respostas para solicitar que o usuário forneça valor se um parâmetro estiver marcado como obrigatório.

### <a name="type"></a>Type
Comandos personalizados dão suporte aos seguintes tipos de parâmetros-


* Datetime
* painel Geografia do app&#39;s selecionado
* Número
* String

Todos esses tipos de parâmetro dão suporte à configuração de valor padrão que você pode configurar no Portal.

### <a name="configuration"></a>Configuração
A configuração é uma propriedade de parâmetro definida somente para o tipo: cadeia de caracteres. Abaixo estão os valores com suporte
* Nenhum
* Aceitar entrada completa: habilitar essa opção, o parâmetro aceita qualquer expressão de entrada, isso é útil quando o usuário precisa de um parâmetro com o expressão completo. por exemplo, endereços postais.
* Aceitar valores de entrada predefinidos do catálogo externo: usado para configurar o parâmetro que pode assumir uma grande variedade de valores. por exemplo, catálogo de vendas. Nesse caso, o catálogo é hospedado em um ponto de extremidade da Web externo e pode ser configurado de forma independente.
* Aceite valores de entrada predefinidos do catálogo interno: usado para configurar o parâmetro que pode assumir alguns valores. Nesse caso, os valores devem ser configurados no Speech Studio.


### <a name="validation"></a>Validação
Validações são construções aplicáveis a determinados tipos de parâmetro que permitem configurar restrições no valor do parâmetro. Atualmente, os comandos personalizados oferecem suporte a validações nos seguintes tipos de parâmetros:
* Datetime
* Número

## <a name="rules-configuration"></a>Configuração de regras
Uma regra em comandos personalizados é definida por um conjunto de **condições**que, quando atendidas, executará um conjunto de **ações**. A regra também permite configurar seu estado e as **expectativas** de **execução posterior** para a próxima vez.

### <a name="types"></a>Tipos
Os comandos personalizados dão suporte às seguintes categorias de regra:
* Regras de conclusão

    Lista de regras a serem executadas após o preenchimento do comando. Todas as regras configuradas nesta seção para as quais as condições são verdadeiras serão executadas.
    
* Regras de interação

    As regras de interação podem ser usadas para configurar validações personalizadas adicionais, confirmações, correção em uma etapa ou para realizar qualquer outra lógica de caixa de diálogo personalizada. Essas regras são avaliadas em cada processamento de ativação e podem ser usadas para disparar regras de conclusão.

As diferentes ações configuradas como parte de uma regra são executadas na ordem em que aparecem no portal de criação.
    
### <a name="conditions"></a>Condições
Conjunto de condições que devem ser atendidas para que uma regra seja executada. A condição de regras pode ser dos seguintes tipos:
* Valor do parâmetro igual a: valor do parâmetro configurado igual a um valor específico.
* Nenhum valor de parâmetro: os parâmetros configurados não devem ter nenhum valor.
* Parâmetros obrigatórios: o parâmetro configurado tem um valor.
* Todos os parâmetros obrigatórios: todos os parâmetros que foram marcados como obrigatórios têm um valor.
* Parâmetros atualizados: um ou mais valores de parâmetro foram atualizados como resultado do processamento da entrada atual (expressão/Activity).
* A confirmação foi bem-sucedida: a expressão de entrada/atividade foi uma confirmação bem-sucedida (Sim).
* Confirmação negada: a expressão de entrada/atividade foi uma confirmação bem-sucedida (não).
* O comando anterior precisa ser atualizado: essa condição é usada em instâncias quando você deseja capturar uma confirmação negada juntamente com uma atualização. Nos bastidores, isso é configurado para quando o mecanismo de caixa de diálogo detecta uma confirmação negativa em que a intenção é a mesma que a anterior, e o usuário respondeu com uma atualização.

### <a name="actions"></a>Ações
* Enviar resposta de fala: Enviar uma resposta de fala de volta ao cliente.
* Atualizar valor do parâmetro: atualizar o valor de um parâmetro de comando para um valor especificado.
* Limpar valor do parâmetro limpar valor do parâmetro de comando.
* Chamar ponto de extremidade da Web: faça uma chamada para um ponto de extremidade da Web.
* Enviar atividade para o cliente: Enviar uma atividade personalizada para o cliente.

### <a name="expectations"></a>As
As expectativas são usadas para configurar dicas para o processamento da próxima entrada do usuário. Há suporte para os seguintes tipos:
* Esperando confirmação do usuário: Especifica que o aplicativo está esperando uma confirmação (Sim/não) para a próxima entrada do usuário.
* Esperando entrada (ões) parâmetro (s) do usuário: especifique um ou mais parâmetros de comando que o aplicativo está esperando da entrada do usuário.

### <a name="post-execution-state"></a>Estado de pós-execução
Estado da caixa de diálogo após o processamento da entrada atual (expressão/Activity). Eles são dos seguintes tipos:
* Comando concluído: conclua o comando e nenhuma regra adicional do comando será processada.
* Executar regras de conclusão: execute todas as regras de conclusão válidas.
* Aguardar a entrada do usuário: Aguarde a próxima entrada do usuário.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)
