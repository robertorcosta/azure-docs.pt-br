---
title: Modelo de segurança dos Hubs de Notificação
description: Conheça o modelo de segurança dos Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263754"
---
# <a name="notification-hubs-security"></a>Segurança dos Hubs de Notificação

## <a name="overview"></a>Visão geral

Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure.

## <a name="shared-access-signature-security"></a>Segurança de assinatura de acesso compartilhada

O Notification Hubs implementa um esquema de segurança em nível de entidade chamado SAS *(SAS).* Cada regra contém um nome, um valor-chave (segredo compartilhado) e um conjunto de direitos, conforme explicado posteriormente em [reivindicações de segurança](#security-claims). 

Ao criar um hub, duas regras são criadas automaticamente: uma com direitos **de escuta** (que o aplicativo cliente usa) e outra com **todos os** direitos (que o backend do aplicativo usa):

- **DefaultListenSharedAccessSignature**: concede somente permissão **de escuta.**
- **DefaultFullSharedAccessSignature**: concede **permissões Listen,** **Manage**e **Send.** Esta política deve ser usada apenas no seu backend de apêndice. Não usá-lo em aplicativos clientes; usar uma diretiva com apenas **acesso ouvir.** Para criar uma nova política de acesso personalizado com um novo token SAS, consulte [tokens SAS para políticas](#sas-tokens-for-access-policies) de acesso mais tarde neste artigo.

Ao realizar o gerenciamento de registro dos aplicativos clientes, se as informações enviadas por meio de notificações não forem confidenciais (por exemplo, atualizações de clima), uma maneira comum de acessar um Hub de notificação é fornecer o valor da chave da regra de acesso de somente escuta para o aplicativo cliente e fornecer o valor de chave da regra de acesso completo para o back-end do aplicativo.

Os aplicativos não devem incorporar o valor-chave nos aplicativos clientes da Windows Store; em vez disso, faça com que o aplicativo do cliente recupere-o do backend do aplicativo na inicialização.

A chave com **o listen** access permite que um aplicativo cliente se registre para qualquer tag. Se o aplicativo deve restringir registros a tags específicas para clientes específicos (por exemplo, quando as tags representam IDs do usuário), o backend do aplicativo deve realizar os registros. Para obter mais informações, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md). Observe que, dessa maneira, o aplicativo cliente não terá acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Declarações de segurança

Semelhante a outras entidades, as operações do Notification Hub são permitidas para três reclamações de segurança: **Listen**, **Send**e **Manage**.

| Declaração   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/atualizar, ler e excluir registros simples | Criar/Atualizar o registro<br><br>Ler registro<br><br>Ler todos os registros para um identificador<br><br>Excluir registro |
| Enviar    | Envie mensagens para o Centro de Notificação                | Enviar mensagem |
| Gerenciar  | CRUDs nos Hubs de notificação (incluindo atualização de credenciais PNS e chaves de segurança) e ler registros baseados em marcas |Criar/Atualizar/Ler/Excluir hubs<br><br>Ler registros por marca |

O Notification Hubs aceita tokens SAS gerados com chaves compartilhadas configuradas diretamente no hub.

Não é possível enviar uma notificação para mais de um namespace. Namespaces são contêineres lógicos para Hubs de Notificação e não estão envolvidos no envio de notificações.

Use as políticas de acesso em nível de namespace (credenciais) para operações em nível de namespace; por exemplo: listagem de hubs, criação ou exclusão de hubs, etc. Apenas as políticas de acesso em nível de hub permitem enviar notificações.

### <a name="sas-tokens-for-access-policies"></a>Tokens SAS para políticas de acesso

Para criar uma nova reivindicação de segurança ou visualizar as chaves SAS existentes, faça o seguinte:

1. Entre no portal do Azure.
2. Selecione **Todos os recursos**.
3. Selecione o nome do Centro de Notificação para o qual deseja criar a reclamação ou exibir a chave SAS.
4. No menu à esquerda, selecione Políticas de **acesso**.
5. Selecione **Nova diretiva** para criar uma nova reivindicação de segurança. Dê um nome à apólice e selecione as permissões que deseja conceder. Em seguida, selecione **OK**.
6. A seqüência completa de conexões (incluindo a nova tecla SAS) é exibida na janela Políticas de acesso. Você pode copiar esta seqüência para a área de transferência para uso posterior.

Para extrair a tecla SAS de uma diretiva específica, selecione o botão **Copiar** ao lado da diretiva que contém a tecla SAS desejada. Cole esse valor em um local temporário e copie a parte de chave SAS da seqüência de conexão. Este exemplo usa um namespace do Notification Hubs chamado **mytestnamespace1**e uma diretiva chamada **policy2**. A chave SAS é o valor perto do final da string, especificado pelo **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Obter chaves SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos Hubs de Notificação](notification-hubs-push-notification-overview.md)
