---
title: Proteger aplicativos de PaaS usando o Armazenamento do Azure | Microsoft Docs
description: Conheça as melhores práticas de segurança do Armazenamento do Azure para proteger aplicativos móveis e Web de PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: fa23637500755f43bb380a9f20cbe3acc7c3a394
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87925797"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Melhores práticas para proteger aplicativos PaaS móveis e Web usando o Armazenamento do Azure
Neste artigo, discutiremos uma coleção de melhores práticas de segurança do Armazenamento do Azure para proteger seus aplicativos PaaS (plataforma como serviço) móveis e Web. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

O Azure possibilita implantar e usar o armazenamento de formas que não são fáceis de alcançar localmente. Com o Armazenamento do Azure, você pode alcançar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. O Armazenamento do Azure não é somente a base para as Máquinas Virtuais do Azure do Windows e Linux, ele também pode dar suporte a grandes aplicativos distribuídos.

O Armazenamento do Azure fornece os quatro seguintes serviços: Armazenamento de Blobs, Armazenamento de Tabelas, Armazenamento de Filas e Armazenamento de Arquivos. Para saber mais, consulte a [Introdução ao Armazenamento do Microsoft Azure](/azure/storage/common/storage-introduction).

O [Guia de segurança do Armazenamento do Azure](/azure/storage/common/storage-security-guide) é uma ótima fonte de informações detalhadas sobre o Armazenamento do Azure e sua segurança. Este artigo de melhores práticas aborda, em um nível mais amplo, alguns dos conceitos encontrados no guia de segurança e leva a ele, bem como a outras fontes, para mais informações.

Este artigo aborda as seguintes melhores práticas:

- SAS (Assinaturas de Acesso Compartilhado)
- RBAC (Controle de Acesso Baseado em Função)
- Criptografia do lado do cliente para dados de alto valor
- Criptografia do Serviço de Armazenamento


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Use a assinatura de acesso compartilhado em vez de uma chave de conta de armazenamento
O controle de acesso é fundamental. Para ajudá-lo a controlar o acesso ao Armazenamento do Azure, o Azure gera duas SAKs (chaves de conta de armazenamento) de 512 bits quando você cria uma conta de armazenamento. O nível de redundância das chaves possibilita evitar interrupções do serviço durante a rotação de chaves de rotina. 

Chaves de acesso de armazenamento são segredos de alta prioridade e só devem ser acessíveis aos responsáveis pelo controle de acesso de armazenamento. Se as pessoas erradas receberem acesso a essas chaves, elas terão controle total sobre o armazenamento e poderão substituir, excluir ou adicionar arquivos ao armazenamento. Isso inclui malware e outros tipos de conteúdo com potencial para comprometer seus clientes ou sua organização.

Você ainda precisa de uma maneira de fornecer acesso aos objetos no armazenamento. Para fornecer um acesso mais granular, aproveite a SAS (assinatura de acesso compartilhado). A SAS possibilita compartilhar objetos específicos no armazenamento por um intervalo predefinido e com permissões específicas. Uma assinatura de acesso compartilhado permite definir:

- O intervalo no qual a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas pelas SAS. Por exemplo, um SAS em um blob pode conceder a um usuário permissões de leitura e gravação nesse blob, mas não permissões de exclusão.
- Um endereço IP opcional ou um intervalo de endereços IP dos quais o Armazenamento do Azure aceita a SAS. Por exemplo, você pode especificar um intervalo de endereços IP que pertencem à sua organização. Isso fornece outra medida de segurança para a SAS.
- O protocolo em que o Armazenamento do Azure aceita a SAS. Você pode usar esse parâmetro opcional para restringir o acesso a clientes usando HTTPS.

A SAS lhe permite compartilhar conteúdo da maneira desejada sem precisar fornecer suas chaves de conta de armazenamento. Sempre usar SAS em seu aplicativo é uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves de conta de armazenamento.

Para saber mais sobre a assinatura de acesso compartilhado, confira [Usando SAS (Assinaturas de Acesso Compartilhado)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Use o controle de acesso baseado em função
Outra maneira de gerenciar o acesso é usar o [controle de acesso baseado em função do Azure (RBAC do Azure)](/azure/role-based-access-control/overview). Com o RBAC, você se concentra em fornecer aos funcionários as permissões exatas de que eles precisam, com base nos princípios de segurança de privilégio mínimo e do que os usuários precisam saber. Muitas permissões podem expor uma conta a ataques. Permissões insuficientes significa que os funcionários não podem ter seu trabalho feito com eficiência. O RBAC ajuda a resolver esse problema oferecendo gerenciamento de acesso refinado ao Azure. Isso é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados.

Você pode usar funções internas do Azure no Azure para atribuir privilégios a usuários. Por exemplo, use o Colaborador da Conta de Armazenamento para operadores de nuvem que precisarem gerenciar contas de armazenamento e a função de Colaborador de Conta de Armazenamento Clássica para gerenciar contas de armazenamento clássicas. Para operadores de nuvem que precisam gerenciar VMs, mas não a rede virtual ou a conta de armazenamento a que estão conectados, adicione-os à função de Colaborador de Máquina Virtual.

As organizações que não impõem o controle de acesso a dados usando recursos como o RBAC podem estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados por permitir que alguns usuários acessem dados que a não deveriam ter acesso.

Para saber mais sobre o RBAC, consulte:

- [Gerenciar acesso usando o RBAC e o Portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [Funções internas do Azure](/azure/role-based-access-control/built-in-roles)
- [Guia de segurança do Armazenamento do Microsoft Azure](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Use a criptografia do lado do cliente para dados de alto valor
A criptografia do lado do cliente permite criptografar programaticamente dados em trânsito antes de carregar no Armazenamento do Azure, bem como descriptografar os dados programaticamente ao recuperá-los. Isso fornece criptografia de dados em trânsito, mas também fornece criptografia de dados em repouso. A criptografia do lado do cliente é o método mais seguro de criptografar seus dados, mas ela requer que você faça alterações programáticas em seu aplicativo e implemente processos de gerenciamento de chaves.

A criptografia do lado do cliente também permite que você tenha controle exclusivo sobre suas chaves de criptografia. Você pode gerar e gerenciar suas próprias chaves de criptografia. Ela usa uma técnica de envelope em que a biblioteca de cliente do Armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo) que é encapsulada (criptografada) usando a KEK (chave de criptografia de chave). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, e pode ser gerenciada localmente ou armazenada no [Azure Key Vault](/azure/key-vault/key-vault-overview).

A criptografia do lado do cliente é interna nas bibliotecas de cliente de armazenamento Java e .NET. Consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](/azure/storage/common/storage-client-side-encryption) para obter informações sobre como criptografar dados em aplicativos cliente e gerar e gerenciar suas próprias chaves de criptografia.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Habilitar Criptografia do Serviço de Armazenamento para dados em repouso
Quando a [Criptografia do serviço de armazenamento](/azure/storage/common/storage-service-encryption) para o armazenamento de arquivos está habilitada, os dados são criptografados automaticamente usando criptografia AES-256. A Microsoft lida com toda a criptografia, a descriptografia e o gerenciamento de chaves. Este recurso está disponível para os tipos de redundância LRS e GRS.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresentou a você um conjunto de melhores práticas de segurança do Armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)
- [Protegendo bancos de dados de PaaS no Azure](paas-applications-using-sql.md)
