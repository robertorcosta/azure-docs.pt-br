---
title: Bibliotecas de cliente e APIs REST para os serviços de comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as bibliotecas de cliente dos serviços de comunicação do Azure e as APIs REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2d5637be547b28c231ef757b5dd4355692dab88f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318816"
---
# <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

As funcionalidades dos serviços de comunicação do Azure são organizadas conceitualmente em seis áreas. Algumas áreas têm bibliotecas de cliente totalmente abertas. A biblioteca de cliente de chamada usa interfaces de rede proprietárias e está fechada no momento e a biblioteca de chat inclui uma dependência de código-fonte fechado. Os links para todos os SDKs e exemplos são mantidos no [repositório GitHub dos serviços de comunicação do Azure](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Bibliotecas de cliente

| Assembly               | Protocolos             |Abrir vs. fonte fechada| Namespaces                          | Capacidades                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Aberto            | Azure. ResourceManager. comunicação | Provisionar e gerenciar recursos de serviços de comunicação             |
| Comum                 | REST | Aberto               | Azure. Communication. comum          | Fornece tipos de base para outras bibliotecas de cliente |
| Administração         | REST | Aberto               | Azure. Communication. Administration  | Gerenciar usuários, tokens de acesso e números de telefone, alocar STUN em conformidade com os padrões e transformar servidores |
| Chat                   | REST com sinalização proprietária | Abrir com pacote de sinalização de origem fechado    | Azure. Communication. chat            | Adicionar chat com base em texto em tempo real aos seus aplicativos  |
| SMS                    | REST | Aberto              | Azure. Communication. SMS             | Enviar e receber mensagens SMS |
| Chamando                | Transporte proprietário | Fechadas |Azure. Communication. chamando         | Aproveite o uso de voz, vídeo, compartilhamento de tela e outros recursos de comunicação de dados em tempo real          |

### <a name="client-library-language-support"></a>Suporte ao idioma da biblioteca do cliente

Orientações de disponibilidade e cronogramas para pacotes de biblioteca de cliente individuais são detalhados abaixo. O [roteiro do Azure](https://azure.microsoft.com/updates/) fornece informações adicionais sobre os recursos futuros.

| Área           | JavaScript | .NET | Python | Java | Swift ou obj-C | Java (Android) | Outro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Ainda não compatível*  | GO e CLI do Azure *ainda não têm suporte* |
| Comum         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Administração | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | CLI                            |
| Chat           | ✔️         | ✔️    | ✔️      | ✔️   | *Ainda não compatível*  | *Ainda não compatível*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Chamando        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Suporte ao repositório público da biblioteca de clientes

Os serviços de comunicação publicam bibliotecas criadas em vários repositórios públicos.

| Idioma       | Otimizado para...                       | Empacotamento |
| -------------- | ------------------------------------ | --------- |
| .NET           | Plataforma cruzada                       | NuGet     |
| Python         | Servidores Windows & Linux              | PyPI      |
| Java (J2EE)    | JVM em servidores Windows ou Linux      | Maven     |
| Java (Android) | Aplicativos cliente Android          | Maven     |
| JavaScript     | Nó e aplicativos cliente do navegador | NPM       |

## <a name="rest-apis"></a>APIs REST

As APIs de serviços de comunicação são documentadas junto com outras APIs REST do Azure no [docs.Microsoft.com](https://docs.microsoft.com/rest/api/azure/). Esta documentação lhe dirá como estruturar suas mensagens HTTP e oferece orientação para usar o postmaster. Esta documentação também é oferecida no formato Swagger no [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Detalhes de suporte adicionais

### <a name="ios-and-android-support-details"></a>detalhes de suporte do iOS e Android

- Serviços de comunicação bibliotecas de cliente do iOS destino versão 13 + e Xcode 11 +.
- Bibliotecas de cliente Java do Android destino nível da API do Android 21 + e Android Studio 4.0 +

### <a name="net-support-details"></a>Detalhes do suporte do .NET

Com exceção da chamada, os pacotes de serviços de comunicação são direcionados .NET Standard 2,0, que oferece suporte às plataformas listadas abaixo.

Suporte via .NET Framework 4.6.1
- Windows 10, 8,1, 8 e 7
- Windows Server 2012 R2, 2012 e 2008 R2 SP1

Suporte por meio do .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1+
- SO máximo X 10.12 +
- Várias versões/distribuições do Linux
- UWP 10.0.16299 (RS3) de setembro de 2017
- Unity 2018,1
- Mono 5.4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>Expectativas de estabilidade da API 

> [!IMPORTANT]
> Esta seção fornece diretrizes sobre APIs REST e bibliotecas de cliente marcadas como **estáveis**. As APIs marcadas pré-lançamento, visualização ou beta podem ser alteradas ou preteridas **sem aviso prévio**. Atualmente, os serviços de comunicação do Azure estão em uma **Visualização pública**e as APIs são marcadas como tal.

No futuro, podemos desativar versões das bibliotecas de cliente dos serviços de comunicação e podemos introduzir alterações significativas em nossas APIs REST e bibliotecas de cliente lançadas. Os serviços de comunicação do Azure *geralmente* seguirão duas políticas de suporte para desativar versões de serviço:

- Você será notificado pelo menos três anos antes de ser necessário alterar o código devido a uma alteração na interface dos serviços de comunicação. Todas as APIs REST documentadas e APIs de biblioteca de cliente geralmente aproveitam um aviso de pelo menos três anos antes de as interfaces serem encerradas.
- Você será notificado pelo menos um ano antes de atualizar os assemblies da biblioteca de cliente para a versão secundária mais recente. Essas atualizações necessárias não devem exigir nenhuma alteração de código porque estão na mesma versão principal. Isso é especialmente verdadeiro para as bibliotecas de chamada e de chat que têm componentes em tempo real que frequentemente exigem atualizações de segurança e desempenho. É altamente recomendável que você mantenha as bibliotecas de cliente dos serviços de comunicação atualizadas.

### <a name="api-and-client-library-decommissioning-examples"></a>Exemplos de descomissionamento da biblioteca de cliente e API

**Você integrou a versão v24 da API REST do SMS em seu aplicativo. A comunicação do Azure libera v25.**

Você receberá três anos de aviso antes que essas APIs parem de funcionar e sejam forçadas a atualizar para o v25. Essa atualização pode exigir uma alteração de código.

**Você integrou a versão v 2.02 da biblioteca de cliente que está chamando em seu aplicativo. A comunicação do Azure libera v 2.05.**

Talvez seja necessário atualizar para a versão v 2.05 da biblioteca de cliente de chamada em 12 meses após o lançamento do v 2.05. Isso deve ser uma simples substituição do artefato sem a necessidade de uma alteração de código porque v 2.05 está na versão principal V2 e não tem alterações significativas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte as visões gerais da biblioteca de cliente a seguir:

- [Visão geral da biblioteca de cliente de chamada](../concepts/voice-video-calling/calling-sdk-features.md)
- [Visão geral da biblioteca de cliente de chat](../concepts/chat/sdk-features.md)
- [Visão geral da biblioteca de cliente SMS](../concepts/telephony-sms/sdk-features.md)

Para começar a usar os serviços de comunicação do Azure:

- [Criar recursos de comunicação do Azure](../quickstarts/create-communication-resource.md)
- Gerar [tokens de acesso do usuário](../quickstarts/access-tokens.md)
