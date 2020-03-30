---
title: Recomendações de segurança
description: Implemente as recomendações de segurança para ajudar a cumprir suas obrigações de segurança, conforme estabelecido em nosso modelo de responsabilidade compartilhada. Melhore a segurança do seu aplicativo.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684121"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o Serviço de Aplicativos

Este artigo contém recomendações de segurança para o Azure App Service. A implementação dessas recomendações ajudará você a cumprir suas obrigações de segurança conforme descrito em nosso modelo de responsabilidade compartilhada e melhorará a segurança geral de suas soluções de Web App. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do provedor de serviços, leia [a segurança da infra-estrutura do Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|----|
| Mantenha-se atualizado | Use as versões mais recentes de plataformas suportadas, linguagens de programação, protocolos e frameworks. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendação | Comentários |
|-|----|
| Desativar o acesso anônimo | A menos que você precise apoiar solicitações anônimas, desabilite o acesso anônimo. Para obter mais informações sobre as opções de autenticação do Azure App Service, consulte [Autenticação e autorização no Azure App Service](overview-authentication-authorization.md).|
| Exigir autenticação | Sempre que possível, use o módulo de autenticação do App Service em vez de escrever código para lidar com autenticação e autorização. Consulte [Autenticação e autorização no Azure App Service](overview-authentication-authorization.md). |
| Proteja os recursos back-end com acesso autenticado | Você pode usar a identidade do usuário ou usar uma identidade de aplicativo para autenticar um recurso back-end. Quando você optar por usar uma identidade de aplicativo, use uma [identidade gerenciada](overview-managed-identity.md).
| Exigir autenticação de certificado de cliente | A autenticação do certificado do cliente melhora a segurança, permitindo apenas conexões de clientes que podem autenticar usando certificados que você fornece. |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Redirecione http para HTTPs | Por padrão, os clientes podem se conectar a aplicativos web usando http ou HTTPS. Recomendamos redirecionar http para HTTPs porque o HTTPS usa o protocolo SSL/TLS para fornecer uma conexão segura, que é criptografada e autenticada. |
| Criptografe a comunicação com os recursos do Azure | Quando seu aplicativo se conecta aos recursos do Azure, como [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/) ou [Armazenamento Azure,](/azure/storage/)a conexão permanece no Azure. Uma vez que a conexão passa pela rede compartilhada no Azure, você deve sempre criptografar toda a comunicação. |
| Requerer a versão TLS mais recente possível | Desde 2018, os novos aplicativos do Azure App Service usam o TLS 1.2. As versões mais recentes do TLS incluem melhorias de segurança em relação às versões de protocolo mais antigas. |
| Use FTPS | O Serviço de Aplicativo dá suporte ao FTP e FTPS para implantar os arquivos. Use FTPS em vez de FTP quando possível. Quando um ou ambos os protocolos não estiverem em uso será necessário [desabilitá-los](deploy-ftp.md#enforce-ftps). |
| Proteger dados do aplicativo | Não armazene segredos de aplicativos, como credenciais de banco de dados, tokens de API ou chaves privadas em seus arquivos de código ou configuração. A abordagem comumente aceita é acessá-los como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão standard na linguagem de sua escolha. No Azure App Service, você pode definir variáveis de ambiente através [de configurações de aplicativos](web-sites-configure.md) e [strings de conexão](web-sites-configure.md). As configurações do aplicativo e as seqüências de conexão são armazenadas criptografadas no Azure. As configurações do aplicativo são descriptografadas somente antes de serem injetadas na memória do processo do aplicativo quando o aplicativo é iniciado. As chaves de criptografia são giradas regularmente. Alternativamente, você pode integrar seu aplicativo Azure App Service com [o Azure Key Vault](/azure/key-vault/) para gerenciamento avançado de segredos. Ao [acessar o Key Vault com uma identidade gerenciada](../key-vault/tutorial-web-application-keyvault.md), o aplicativo do Serviço de Aplicativo poderá acessar com segurança os segredos que você precisa. |

## <a name="networking"></a>Rede

| Recomendação | Comentários |
|-|-|
| Use restrições de IP estáticas | O Azure App Service no Windows permite definir uma lista de endereços IP que podem acessar seu aplicativo. A lista permitida pode incluir endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [Restrições de IP estático do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md).  |
| Use o nível de preços isolado | Exceto pelo nível de preços isolado, todos os níveis executam seus aplicativos na infra-estrutura de rede compartilhada no Azure App Service. O nível isolado oferece um isolamento completo da rede executando seus aplicativos dentro de um ambiente dedicado [ao App Service.](environment/intro.md) Um ambiente do Serviço de Aplicativo é executado na própria instância da [Rede Virtual do Microsoft Azure](/azure/virtual-network/).|
| Use conexões seguras ao acessar recursos no local | Você pode usar [conexões híbridas,](app-service-hybrid-connections.md) [integração de rede virtual](web-sites-integrate-with-vnet.md)ou [ambiente de Serviço de Aplicativo](environment/intro.md) para se conectar aos recursos locais. |
| Limitar a exposição ao tráfego de rede de entrada | Os grupos de segurança da rede permitem restringir o acesso à rede e controlar o número de pontos finais expostos. Para obter mais informações, consulte [Como controlar o tráfego de entrada em um ambiente de serviço de aplicativo.](environment/app-service-app-service-environment-control-inbound-traffic.md) |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários |
|-|-|
|Use o nível padrão do Azure Security Center | [O Azure Security Center](../security-center/security-center-app-services.md) é integrado nativamente ao Azure App Service. Ele pode executar avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Próximas etapas

Verifique com o provedor de aplicativos se há requisitos adicionais de segurança. Para obter mais informações sobre o desenvolvimento de aplicativos seguros, consulte [Documentação de Desenvolvimento Seguro](../security/fundamentals/abstract-develop-secure-apps.md).
