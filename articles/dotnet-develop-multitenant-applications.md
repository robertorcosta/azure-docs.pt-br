---
title: Padrão de aplicativo Web multilocatário | Microsoft Docs
description: Encontre visões gerais de arquitetura e padrões de design que descrevem como implementar um aplicativo Web multilocatário no Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d1441ede9f448b3e6ffb0726c2ee92f192369e9a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481845"
---
# <a name="multitenant-applications-in-azure"></a>Aplicativos multilocatários no Azure
Um aplicativo multilocatário é um recurso compartilhado que permite que "usuários em inquilinos separados" visualizem o aplicativo como se fosse seu próprio. Um cenário típico que se presta a um aplicativo multilocatário é aquele em que todos os usuários do aplicativo de diferentes inquilinos podem desejar personalizar a experiência do usuário, mas de outra forma têm os mesmos requisitos básicos de negócios. Exemplos de grandes aplicativos multilocatários são o Office 365, o Outlook.com e o visualstudio.com.

Da perspectiva de um provedor de um aplicativo, os benefícios da multilocação na maioria das vezes estão relacionados à eficiência operacional e de custo. Uma versão do seu aplicativo pode atender às necessidades de muitos locatários/clientes permitindo a consolidação das tarefas de administração do sistema, como monitoramento, ajuste de desempenho, manutenção de software e backups de dados.

A lista a seguir fornece os objetivos e requisitos mais significativos de uma perspectiva de provedor.

* **Provisionamento**: você precisa ser capaz de provisionar novos locatários para o aplicativo.  Para aplicativos multilocatários com um grande número de locatários, geralmente é necessário automatizar esse processo permitindo provisionamento por autoatendimento.
* **Facilidade de manutenção**: você deve ser capaz de atualizar o aplicativo e executar outras tarefas de manutenção enquanto vários locatários estiverem usando-o.
* **Monitoramento**: você deve ser capaz de monitorar o aplicativo em todos os momentos para identificar os problemas e solucioná-los. Isso inclui o monitoramento de como cada locatário está usando o aplicativo.

Um aplicativo multilocatário implementado adequadamente oferece os seguintes benefícios aos usuários.

* **Isolamento**: as atividades de locatários individuais não afetam o uso do aplicativo por outros locatários. Os locatários não podem acessar os dados uns dos outros. Ao locatário, parece que o aplicativo é de seu uso exclusivo.
* **Disponibilidade**: locatários individuais querem que o aplicativo esteja constantemente disponível, talvez com garantias definidas em um Contrato de Nível de Serviço. Novamente, as atividades de outros locatários não devem afetar a disponibilidade do aplicativo.
* **Escalabilidade**: o aplicativo é dimensionado para atender à demanda de locatários individuais. A presença e as ações de outros locatários não devem afetar o desempenho do aplicativo.
* **Custos**: os custos são menores do que a execução de um aplicativo dedicado de único locatário, porque a multilocação permite o compartilhamento de recursos.
* **Personalizabilidade**. A capacidade de personalizar o aplicativo para um locatário individual de várias maneiras, como adicionar ou remover recursos, alterar cores e logotipos ou até mesmo adicionar seu próprio código ou script.

Em suma, embora existam muitas considerações que você deve levar em conta para fornecer um serviço altamente escalável, há também uma série de metas e requisitos que são comuns a muitos aplicativos multilocatários. Alguns podem não ser relevantes em cenários específicos, e a importância de requisitos e metas individuais será diferente em cada cenário. Como provedor do aplicativo multilocatário, você também terá metas e requisitos como, atendendo às metas e requisitos do inquilino, rentabilidade, faturamento, múltiplos níveis de serviço, provisionamento, monitoramento de manutenção e automação.

Para obter mais informações sobre considerações de design adicionais de um aplicativo multilocatário, consulte [Hospedando um aplicativo multilocatário no Azure][Hosting a Multi-Tenant Application on Azure]. Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

O Azure oferece muitos recursos que permitem resolver os principais problemas encontrados durante a criação de um sistema multilocatário.

**Isolamento**

* Inquilinos do site do segmento por cabeçalhos de host com ou sem comunicação TLS
* Segmentar locatários de site por parâmetros de consulta
* Serviços web em funções de trabalho
  * Funções do trabalhador que normalmente processam dados no backend de um aplicativo.
  * Funções Web que geralmente agem como o front-end de aplicativos.

**Storage**

O gerenciamento de dados, como o Banco de Dados Azure SQL ou os serviços de armazenamento do Azure, como o serviço Table, que fornece serviços para armazenamento de grandes quantidades de dados não estruturados e blob, que fornece serviços para armazenar grandes quantidades de texto não estruturado ou dados binários, como vídeo, áudio e imagens.

* Protegendo dados multilocatários em logins SQL Database por inquilino do SQL Server.
* Usando o Azure Tables for Application Resources especificando uma política de acesso ao nível de contêiner, você pode ter a capacidade de ajustar permissões sem ter que emitir novas URL's para os recursos protegidos com assinaturas de acesso compartilhada.
* Filas do Azure para recursos do aplicativo. As filas do Azure geralmente são usadas para processamento de unidades em nome de locatários, mas também podem ser usadas para distribuir o trabalho necessário para provisionamento ou gerenciamento.
* Filas do Service Bus para recursos do aplicativo que enviam trabalho por push para um serviço compartilhado, você pode usar uma única fila onde cada remetente locatário tem apenas permissões (conforme derivado das declarações emitidas no ACS) para envio por push àquela fila, enquanto somente os receptores do serviço têm permissão para efetuar pull na fila dos dados provenientes de vários locatários.

**Serviços de conexão e segurança**

* Service Bus do Azure, uma infraestrutura de mensagens situada entre aplicativos que permite que eles troquem mensagens de uma maneira vagamente acoplada para fornecer escala e resiliência melhoradas.

**Serviços de rede**

O Azure fornece vários serviços de rede que oferecem suporte à autenticação e melhoram a capacidade de gerenciamento dos aplicativos hospedados. esses serviços incluem o seguinte:

* A Rede Virtual do Azure permite que você provisione e gerencie VPNs (redes virtuais privadas) no Azure e vincule-as com segurança à infraestrutura de TI local.
* O Gerenciador de Tráfego de Rede Virtual permite que você equilibre o tráfego de entrada entre os vários serviços hospedados do Azure, quer eles estejam em execução no mesmo datacenter ou em diferentes datacenters ao redor do mundo.
* O Active Directory do Azure (AD do Azure) é um serviço moderno e baseado em REST que fornece recursos de gerenciamento de identidade e de controle de acesso para seus aplicativos na nuvem. O uso do Azure AD for Application Resources fornece uma maneira fácil de autenticar e autorizar os usuários a obter acesso aos seus aplicativos e serviços web, permitindo que os recursos de autenticação e autorização sejam contabilizados fora do seu código.
* O Service Bus do Azure fornece um serviço de mensagens seguro e o recurso de fluxo de dados para aplicativos distribuídos e híbridos, como a comunicação entre aplicativos hospedados do Azure e aplicativos e serviços locais, sem a necessidade de firewall complexo e de infraestruturas de segurança. Usar o Relé de Ônibus de Serviço para Recursos de Aplicação para acessar os serviços expostos como pontos finais pode pertencer ao inquilino (por exemplo, hospedado fora do sistema, como no local), ou eles podem ser serviços fornecidos especificamente para o inquilino (porque dados confidenciais e específicos do inquilino viajam através deles).

**Provisionando recursos**

O Azure fornece uma série de maneiras de fornecer novos inquilinos para o aplicativo. Para aplicativos multilocatários com um grande número de locatários, geralmente é necessário automatizar esse processo permitindo provisionamento por autoatendimento.

* A funções de trabalho permitem provisionar e desprovisionar recursos por locatário (como quando um novo locatário faz ou cancela uma assinatura), coletar métricas para uso de medição e gerenciar a escala seguindo uma determinada agenda ou em resposta ao cruzamento de limites dos principais indicadores de desempenho. Essa mesma função também pode ser usada para enviar atualizações e upgrades por push para a solução.
* Os Blobs do Azure podem ser usados para provisionar computação ou recursos de armazenamento pré-inicializados para novos locatários fornecendo, ao mesmo tempo, políticas de acesso em nível de contêiner para proteger os pacotes, imagens VHD e outros recursos do serviço de computação.
* As opções para provisionamento de recursos de Banco de Dados SQL para um locatário incluem:
  
  * DDL em scripts ou incorporados como recursos dentro de assembléias.
  * Pacotes de DAC do SQL Server 2008 R2 implantados programaticamente.
  * Copiando de um banco de dados de referência mestre.
  * Uso de importação e exportação de banco de dados para provisionar novos bancos de dados de um arquivo.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
