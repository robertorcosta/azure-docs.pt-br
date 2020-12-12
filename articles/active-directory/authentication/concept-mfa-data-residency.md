---
title: Residência de dados da autenticação multifator do Azure AD
description: Saiba quais dados pessoais e organizacionais a autenticação multifator do Azure AD armazena sobre você e seus usuários e quais dados permanecem dentro do país/região de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788512db242bf1a1c6f18ffc0ee773bd3372aa42
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355855"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Dados de residência de dados e de cliente para autenticação multifator do Azure AD

Os dados do cliente são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados do cliente estão armazenados, você pode usar a seção [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) do Microsoft Trust Center.

A autenticação multifator do Azure AD baseada em nuvem e o processo de Servidor de Autenticação Multifator do Azure e armazenar uma quantidade de dados pessoais e dados organizacionais. Este artigo descreve o que são os dados e onde eles são armazenados.

O serviço de autenticação multifator do Azure AD tem data centers nos EUA, Europa e Pacífico Asiático. As seguintes atividades se originam dos data centers regionais, exceto onde indicado:

* A autenticação multifator usando chamadas telefônicas se originam de data centers dos EUA e são roteadas por provedores globais.
* As solicitações de autenticação de usuário de uso geral de outras regiões, como Europa ou Austrália, são processadas no momento com base no local do usuário.
* As notificações por push usando o aplicativo Microsoft Authenticator são processadas no momento nos data centers regionais com base no local do usuário.
    * Serviços específicos do fornecedor do dispositivo, como notificações por push da Apple, podem estar fora do local do usuário.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Dados pessoais armazenados pela autenticação multifator do Azure AD

Dados pessoais são informações de nível de usuário associadas a uma pessoa específica. Os seguintes armazenamentos de dados contêm informações pessoais:

* Usuários bloqueados
* Usuários ignorados
* Solicitações de alteração de token do dispositivo Microsoft Authenticator
* Relatórios de atividade da Autenticação Multifator
* Ativações do Microsoft Authenticator

Essas informações são mantidas por 90 dias.

A autenticação multifator do Azure AD não registra dados pessoais, como nome de usuário, número de telefone ou endereço IP, mas há um *Userobjectid* que identifica as tentativas de autenticação multifator para os usuários. Os dados de log são armazenados por 30 dias.

### <a name="azure-ad-multi-factor-authentication"></a>Autenticação Multifator do Azure AD

Para nuvens públicas do Azure, excluindo a autenticação do Azure B2C, a Extensão do NPS e o Adaptador do AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de Autenticação Multifator     |
| SMS unidirecional                          | Em logs de Autenticação Multifator     |
| Chamada de voz                           | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

> [!NOTE]
> O armazenamento de dados do relatório de atividade da Autenticação Multifator é armazenado nos Estados Unidos para todas as nuvens, independentemente da região que processa a solicitação de autenticação. Microsoft Azure Alemanha, o Microsoft Azure operado pela 21Vianet e a nuvem do Microsoft Government têm os próprios armazenamentos de dados independentes separados de armazenamentos de dados de região de nuvem pública, no entanto esses dados são sempre armazenados no Estados Unidos. Esses armazenamentos de dados contêm informações de identificação pessoal (PII), como nome principal do usuário (UPN) e número de telefone completo. 

Para o Microsoft Azure Government, o Microsoft Azure Alemanha, o Microsoft Azure operado pela 21Vianet, a autenticação do Azure B2C, a Extensão NPS e o Adaptador do AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator |
| SMS unidirecional                          | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator |
| Chamada de voz                           | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

### <a name="multi-factor-authentication-server"></a>Servidor de Autenticação Multifator

Se você implantar e executar o Servidor de Autenticação Multifator do Azure, os seguintes dados pessoais serão armazenados:

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o Servidor de Autenticação Multifator para novas implantações. Os novos clientes que desejarem exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes existentes que têm o Servidor de Autenticação Multifator antes de 1º de julho poderão baixar a versão mais recente e atualizações futuras e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator |
| SMS unidirecional                          | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator |
| Chamada de voz                           | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de Autenticação Multifator<br />Armazenamento de dados do relatório de atividade da Autenticação Multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Dados organizacionais armazenados pela autenticação multifator do Azure AD

Os dados organizacionais são informações de nível de locatário que podiam expor a instalação da configuração ou do ambiente. As configurações de locatário das seguintes páginas da Autenticação Multifator do portal do Azure podem armazenar dados organizacionais como limites de bloqueio ou informações sobre a chamada para solicitações de autenticação do telefone de entrada:

* Bloqueio de conta
* Alerta de fraude
* Notificações
* Configurações de chamada telefônica

Para o Servidor de Autenticação Multifator do Azure, as seguintes páginas do portal do Azure podem conter dados organizacionais:

* Configurações do servidor
* Desvio único
* Regras de cache
* Status do Servidor de Autenticação Multifator

## <a name="log-data-location"></a>Registrar local de dados

O local em que as informações de log são armazenadas depende de em qual região elas são processadas. A maioria das geografias tem recursos nativos de autenticação multifator do Azure AD, portanto, os dados de log são armazenados na mesma região que processa a solicitação de autenticação multifator. Em geografias sem suporte nativo à autenticação multifator do Azure AD, eles são atendidos pelo Estados Unidos ou pelas geografias da Europa e os dados de log são armazenados na mesma região que processa a solicitação de autenticação multifator.

Alguns dados importantes de log de autenticação são armazenados apenas nos Estados Unidos. O Microsoft Azure Alemanha e o Microsoft Azure operado pela 21Vianet sempre são armazenados na respectiva nuvem. Os dados de log da nuvem do Microsoft Government sempre estão disponíveis nos Estados Unidos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre quais informações de usuário são coletadas pela autenticação multifator do Azure AD baseada em nuvem e pelo Azure Servidor de Autenticação Multifator, consulte [coleta de dados de usuário da autenticação multifator do Azure ad](howto-mfa-reporting-datacollection.md).
