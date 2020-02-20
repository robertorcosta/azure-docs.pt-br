---
title: Perguntas frequentes sobre configuração do Azure App | Microsoft Docs
description: Perguntas frequentes sobre a configuração de Azure App
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4bcd8f042563fa381832fd629061a822f71e844a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467582"
---
# <a name="azure-app-configuration-faq"></a>Perguntas frequentes sobre configuração do Azure App

Este artigo aborda as perguntas frequentes sobre a configuração do Azure App.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Como a configuração do aplicativo é diferente da Azure Key Vault?

A configuração do aplicativo foi projetada para um conjunto distinto de casos de uso: ela ajuda os desenvolvedores a gerenciar configurações de aplicativos e controlar a disponibilidade de recursos. Ele visa simplificar muitas das tarefas de trabalhar com dados de configuração complexos.

A configuração do aplicativo dá suporte a:

- Namespaces hierárquicos
- rotulagem
- Consultas extensivas
- Recuperação em lote
- Operações de gerenciamento especializadas
- Uma interface do usuário de gerenciamento de recursos

A configuração do aplicativo é complementar à Key Vault e as duas devem ser usadas lado a lado na maioria das implantações de aplicativo.

## <a name="should-i-store-secrets-in-app-configuration"></a>Devo armazenar segredos na configuração do aplicativo?

Embora a configuração de aplicativo forneça segurança protegida, Key Vault ainda é o melhor lugar para armazenar os segredos do aplicativo. O Key Vault fornece criptografia no nível de hardware, políticas de acesso granulares e operações de gerenciamento, como a rotação de certificado.

Você pode criar valores de configuração de aplicativo que fazem referência a segredos armazenados em Key Vault. Para obter mais informações, consulte [usar referências de Key Vault em um aplicativo ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>A configuração do aplicativo criptografa meus dados?

Sim. A configuração de aplicativo criptografa todos os valores de chave que ele contém e criptografa a comunicação de rede. Nomes de chave e rótulos são usados como índices para recuperar dados de configuração e não são criptografados.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Como a configuração do aplicativo é diferente das configurações do Azure App Service?

Azure App serviço permite que você defina as configurações do aplicativo para cada instância do serviço de aplicativo. Essas configurações são passadas como variáveis de ambiente para o código do aplicativo. Você pode associar uma configuração a um slot de implantação específico, se desejar. Para obter mais informações, consulte [definir configurações de aplicativo](/azure/app-service/configure-common#configure-app-settings).

Por outro lado, Azure App configuração permite que você defina as configurações que podem ser compartilhadas entre vários aplicativos. Isso inclui aplicativos em execução no serviço de aplicativo, bem como outras plataformas. O código do aplicativo acessa essas configurações por meio dos provedores de configuração para .NET e Java, por meio do SDK do Azure ou diretamente por meio de APIs REST.

Você também pode importar e exportar configurações entre o serviço de aplicativo e a configuração de aplicativo. Esse recurso permite que você configure rapidamente um novo repositório de configuração de aplicativo com base nas configurações existentes do serviço de aplicativo. Você também pode compartilhar a configuração com um aplicativo existente que se baseia nas configurações do serviço de aplicativo.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Há alguma limitação de tamanho em chaves e valores armazenados na configuração do aplicativo?

Há um limite de 10 KB para um único item de chave-valor.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como devo armazenar configurações para vários ambientes (teste, preparo, produção e assim por diante)?

Atualmente, você controla quem pode acessar a configuração de aplicativo em um nível por loja. Use um armazenamento separado para cada ambiente que exija permissões diferentes. Essa abordagem oferece o melhor isolamento de segurança.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as maneiras recomendadas de usar a configuração de aplicativo?

Consulte [práticas recomendadas](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a configuração do aplicativo?

Há dois tipos de preço: 1) uma camada gratuita e 2) uma camada padrão.

Se você criou um repositório antes da introdução da camada Standard, ele será automaticamente movido para a camada gratuita após a disponibilidade geral. Você pode optar por atualizar para a camada Standard ou permanecer na camada gratuita se atender às suas necessidades.

## <a name="which-app-configuration-tier-should-i-use"></a>Qual camada de configuração de aplicativo devo usar?

As duas camadas de configuração de aplicativo oferecem a funcionalidade básica, incluindo configurações, sinalizadores de recursos, referências de Key Vault, operações básicas de gerenciamento, métricas e logs.

Veja a seguir as considerações para escolher uma camada.

- **Recursos por assinatura**: um recurso consiste em um único repositório de configuração. Cada assinatura é limitada a um repositório de configuração na camada gratuita. As assinaturas podem ter um número ilimitado de repositórios de configuração na camada Standard.
- **Armazenamento por recurso**: na camada gratuita, cada repositório de configuração é limitado a 10 MB de armazenamento. Na camada Standard, cada repositório de configuração pode usar até 1 GB de armazenamento.
- **Histórico de chaves**: a configuração de aplicativo armazena um histórico de todas as alterações feitas nas chaves. Na camada gratuita, esse histórico é armazenado por sete dias. Na camada Standard, esse histórico é armazenado por 30 dias.
- **Solicitações por dia**: os repositórios de camada gratuita são limitados a 1.000 solicitações por dia. Depois que uma loja atingir 1.000 solicitações, ele retornará o código de status HTTP 429 para todas as solicitações até a meia-noite UTC.

    Para repositórios de camada Standard, as primeiras 200.000 solicitações por dia são incluídas no encargo diário. Solicitações adicionais são cobradas como excedentes.

- **Contrato de nível de serviço**: a camada Standard tem um SLA de 99,9% de disponibilidade. A camada gratuita não tem um SLA.
- **Recursos de segurança**: ambas as camadas incluem funcionalidade de segurança básica, incluindo criptografia com chaves gerenciadas pela Microsoft, autenticação por meio de HMAC ou Azure Active Directory, suporte a RBAC e identidade gerenciada. A camada Standard oferece funcionalidade de segurança mais avançada, incluindo suporte a link privado e criptografia com chaves gerenciadas pelo cliente.
- **Custo**: os armazenamentos de camada Standard têm um encargo de uso diário. Há também um encargo excedente para solicitações após a alocação diária. Não há nenhum custo para usar um armazenamento de camada gratuita.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Posso atualizar um armazenamento da camada gratuita para a camada Standard? Posso fazer downgrade de um armazenamento da camada Standard para a camada gratuita?

Você pode atualizar da camada gratuita para a camada Standard a qualquer momento.

Não é possível fazer downgrade de um armazenamento da camada Standard para a camada gratuita. Você pode criar um novo repositório na camada gratuita e, em seguida, [importar dados de configuração para esse armazenamento](howto-import-export-data.md).

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Como posso receber comunicados sobre novas versões e outras informações relacionadas à configuração do aplicativo?

Assine nosso [repositório de comunicados do GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso relatar um problema ou dar uma sugestão?

Você pode contatá-los diretamente no [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Próximas etapas

* [Sobre a configuração de Azure App](./overview.md)
