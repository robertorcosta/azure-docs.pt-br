---
title: FAQ de configuração do aplicativo azure
description: Perguntas frequentes sobre a configuração do aplicativo Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348780"
---
# <a name="azure-app-configuration-faq"></a>FAQ de configuração do aplicativo azure

Este artigo responde a perguntas frequentes sobre a configuração do aplicativo Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Como a configuração do aplicativo é diferente do Azure Key Vault?

A configuração do aplicativo ajuda os desenvolvedores a gerenciar as configurações do aplicativo e controlar a disponibilidade de recursos. Ele visa simplificar muitas das tarefas de trabalhar com dados de configuração complexos.

Suporte à configuração do aplicativo:

- Namespaces hierárquicos
- Rotulagem
- Consultas extensivas
- Recuperação em lote
- Operações de gestão especializadas
- Uma interface de usuário de gerenciamento de recursos

A configuração do aplicativo complementa o Key Vault, e os dois devem ser usados lado a lado na maioria das implantações de aplicativos.

## <a name="should-i-store-secrets-in-app-configuration"></a>Devo armazenar segredos na configuração do aplicativo?

Embora a configuração do aplicativo ofereça segurança endurecida, o Key Vault ainda é o melhor lugar para armazenar segredos de aplicativos. O Key Vault fornece criptografia em nível de hardware, políticas de acesso granulares e operações de gerenciamento, como a rotação de certificados.

Você pode criar valores de configuração de aplicativos que referenciam segredos armazenados no Key Vault. Para obter mais informações, consulte [Use key vault referências em um aplicativo ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>A configuração do aplicativo criptografa meus dados?

Sim. A configuração do aplicativo criptografa todos os valores-chave que possui e criptografa a comunicação de rede. Os nomes e rótulos-chave são usados como índices para recuperar dados de configuração e não são criptografados.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Como a configuração do aplicativo é diferente das configurações do Azure App Service?

O Azure App Service permite definir as configurações do aplicativo para cada instância do App Service. Essas configurações são passadas como variáveis de ambiente para o código do aplicativo. Você pode associar uma configuração a um slot de implantação específico, se quiser. Para obter mais informações, consulte [Configurar configurações do aplicativo](/azure/app-service/configure-common#configure-app-settings).

Em contraste, a configuração do aplicativo Azure permite definir configurações que podem ser compartilhadas entre vários aplicativos. Isso inclui aplicativos em execução no App Service, bem como outras plataformas. O código do aplicativo acessa essas configurações através dos provedores de configuração para .NET e Java, através do Azure SDK, ou diretamente via APIs REST.

Você também pode importar e exportar configurações entre o Serviço de Aplicativo e a Configuração do Aplicativo. Esse recurso permite configurar rapidamente uma nova loja de configuração de aplicativos com base nas configurações existentes do App Service. Você também pode compartilhar a configuração com um aplicativo existente que depende das configurações do App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existem alguma limitação de tamanho nas chaves e valores armazenados na configuração do aplicativo?

Há um limite de 10 KB para um único item de valor de chave.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como devo armazenar configurações para vários ambientes (teste, encenação, produção e assim por diante)?

Você controla quem pode acessar a configuração do aplicativo em um nível por loja. Use uma loja separada para cada ambiente que exija permissões diferentes. Esta abordagem proporciona o melhor isolamento de segurança.

Se você não precisar de isolamento de segurança entre ambientes, você pode usar etiquetas para diferenciar entre valores de configuração. [O uso de etiquetas para habilitar diferentes configurações para diferentes ambientes](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as formas recomendadas de usar a configuração do aplicativo?

Veja [as melhores práticas](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a configuração do aplicativo?

Existem dois níveis de preços:

- Camada gratuita
- Nível padrão.

Se você criou uma loja antes da introdução do nível Standard, ele automaticamente se moveu para o nível Free mediante disponibilidade geral. Você pode optar por atualizar para o nível Padrão ou permanecer no nível Free.

Você não pode rebaixar uma loja do nível Padrão para o nível Free. Você pode criar uma nova loja no nível Free e, em seguida, importar dados de configuração para esse armazenamento.

## <a name="which-app-configuration-tier-should-i-use"></a>Qual nível de configuração do aplicativo devo usar?

Ambos os níveis de configuração do aplicativo oferecem funcionalidade principal, incluindo configurações de configuração, sinalizadores de recursos, referências do Key Vault, operações básicas de gerenciamento, métricas e logs.

A seguir, considerações para escolher um nível.

- **Recursos por assinatura**: Um recurso consiste em uma única configuração de armazenamento. Cada assinatura é limitada a uma configuração loja no nível gratuito. As assinaturas podem ter um número ilimitado de lojas de configuração no nível padrão.
- **Armazenamento por recurso**: No nível gratuito, cada configuração é limitada a 10 MB de armazenamento. No nível padrão, cada configuração pode usar até 1 GB de armazenamento.
- **Histórico da chave**: A configuração do aplicativo armazena um histórico de todas as alterações feitas nas teclas. No nível livre, esse histórico é armazenado por sete dias. No nível padrão, esse histórico é armazenado por 30 dias.
- **Solicitações por dia**: As lojas de nível grátis são limitadas a 1.000 solicitações por dia. Uma vez que uma loja atinja 1.000 solicitações, ela retornará o código de status HTTP 429 para todas as solicitações até meia-noite UTC.

    Para lojas de nível padrão, as primeiras 200.000 solicitações por dia são incluídas na taxa diária. Pedidos adicionais são cobrados como excesso de idade.

- **Contrato de nível de serviço**: O nível padrão tem um SLA de 99,9% de disponibilidade. O nível livre não tem um SLA.
- **Recursos de segurança**: Ambos os níveis incluem funcionalidade básica de segurança, incluindo criptografia com chaves gerenciadas pela Microsoft, autenticação via HMAC ou Azure Active Directory, suporte ao RBAC e identidade gerenciada. O nível Standard oferece funcionalidades de segurança mais avançadas, incluindo suporte ao Private Link e criptografia com chaves gerenciadas pelo cliente.
- **Custo**: As lojas de nível padrão têm uma taxa de uso diária. Há também uma taxa de excesso para pedidos além da alocação diária. Não há custo para usar uma loja de nível gratuito.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Posso atualizar uma loja do nível Free para o nível Padrão? Posso rebaixar uma loja do nível Padrão para o nível Free?

Você pode atualizar do nível Livre para o nível Padrão a qualquer momento.

Você não pode rebaixar uma loja do nível Padrão para o nível Free. Você pode criar uma nova loja no nível Free e, em seguida, [importar dados de configuração para esse armazenamento](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Existem limites no número de solicitações feitas à Configuração do Aplicativo?

As lojas de configuração no nível Free são limitadas a 1.000 solicitações por dia. As lojas de configuração no nível Standard podem sofrer estrangulamento temporário quando a taxa de solicitação exceder 20.000 solicitações por hora.

Quando uma loja atinge seu limite, ela retornará o código de status HTTP 429 para todas as solicitações feitas até o período expirar. O `retry-after-ms` cabeçalho na resposta dá um tempo de espera sugerido (em milissegundos) antes de tentar novamente a solicitação.

Se o aplicativo experimentar regularmente as respostas do código de status HTTP 429, considere reemprojetá-lo para reduzir o número de solicitações feitas. Para obter mais informações, consulte [Reduzir solicitações feitas à configuração do aplicativo](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Meu aplicativo recebe respostas do código de status HTTP 429. Por quê?

Você receberá uma resposta http de código de status 429 nestas circunstâncias:

* Excedendo o limite diário de solicitação para uma loja no nível Free.
* Estrangulamento temporário devido a uma alta taxa de solicitação para uma loja no nível Standard.
* Uso excessivo de largura de banda.
* Tentando criar ou modificar uma chave quando a cotação de armazenamento for excedida.

Verifique o corpo da resposta 429 para o motivo específico pelo qual a solicitação falhou.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Como posso receber anúncios sobre novos lançamentos e outras informações relacionadas à configuração do aplicativo?

Inscreva-se no nosso [repo de anúncios do GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso relatar um problema ou dar uma sugestão?

Você pode nos contatar diretamente no [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Próximas etapas

* [Sobre a Configuração de Aplicativos do Azure](./overview.md)
