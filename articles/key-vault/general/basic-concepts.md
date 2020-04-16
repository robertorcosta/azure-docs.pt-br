---
title: O que é o Azure Key Vault? | Microsoft Docs
description: Saiba como o Azure Key Vault protege chaves e segredos criptográficos que aplicativos e serviços em nuvem usam.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 14eda137d386146d96b6b9aa54e1ed57021db19d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432132"
---
# <a name="azure-key-vault-basic-concepts"></a>Conceitos básicos do Azure Key Vault

O Azure Key Vault é uma ferramenta para armazenar e acessar segredos de forma segura. Um segredo é tudo o que você deseja controlar rigorosamente o acesso, como certificados, senhas ou chaves de API. Um cofre é um grupo lógico de segredos.

Aqui estão outros termos importantes:

- **Locatário**: um locatário é a organização que possui e gerencia uma instância específica de serviços em nuvem da Microsoft. É mais usado para se referir ao conjunto de serviços Do Azure e Office 365 para uma organização.

- **Proprietário do cofre**: pode criar um cofre de chaves e obter acesso e controle totais sobre ele. O proprietário do cofre também pode configurar a auditoria para registrar quem acessa os segredos e as chaves. Os administradores podem controlar o ciclo de vida da chave. Eles podem reverter para uma nova versão da chave, fazer o backup e tarefas relacionadas.

- **Consumidor do cofre**: pode executar ações nos ativos dentro do cofre de chaves quando seu proprietário concede acesso ao cliente. As ações disponíveis dependem das permissões concedidas.

- **Recurso**: trata-se de um item gerenciável que está disponível por meio do Azure. Exemplos comuns são máquina virtual, conta de armazenamento, aplicativo web, banco de dados e rede virtual. Há muitos mais.

- **Grupo de recursos**: trata-se de um contêiner que mantém os recursos relacionados de uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

- **Principal de serviço**: Um principal de serviço do Azure é uma identidade de segurança que aplicativos, serviços e ferramentas de automação criados pelo usuário usam para acessar recursos específicos do Azure. Pense nisso como uma "identidade de usuário" (nome de usuário e senha ou certificado) com uma função específica e permissões rigorosamente controladas. Uma entidade de serviço só precisa fazer coisas específicas, ao contrário de uma identidade de usuário geral. Ele melhora a segurança se você conceder-lhe apenas o nível mínimo de permissão que ele precisa para executar suas tarefas de gerenciamento.

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md): o Azure AD é o serviço do Active Directory de um locatário. Cada diretório tem um ou mais domínios. Um diretório pode ter várias assinaturas associadas a ele, mas apenas um locatário.

- **ID do locatário do Azure**: uma ID de locatário é uma maneira exclusiva para identificar uma instância do Azure AD dentro de uma assinatura do Azure.

- **Identidades gerenciadas**: O Azure Key Vault fornece uma maneira de armazenar com segurança credenciais e outras chaves e segredos, mas seu código precisa autenticar-se no Key Vault para recuperá-las. Usar a identidade gerenciada torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Microsoft Azure Active Directory. Você pode usar essa identidade para autenticar o Key Vault ou qualquer serviço que dê suporte à autenticação do Azure AD sem ter as credenciais no código. Para obter mais informações, consulte a imagem a seguir e a [visão geral das identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagrama de como funcionam as identidades gerenciadas para os recursos do Azure](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autenticação
Para fazer qualquer operação com o Key Vault, primeiro você precisa autenticar para ele. Existem três maneiras de autenticar para Key Vault:

- [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md): Quando você implanta um aplicativo em uma máquina virtual no Azure, você pode atribuir uma identidade à sua máquina virtual que tenha acesso ao Key Vault. Você também pode atribuir identidades a [outros recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). A vantagem dessa abordagem é que o aplicativo ou serviço não está gerenciando a rotação do primeiro segredo. Azure gira automaticamente a identidade. Recomendamos essa abordagem como uma prática recomendada. 
- **Principal de serviço e certificado**: Você pode usar um diretor de serviço e um certificado associado que tenha acesso ao Key Vault. Não recomendamos essa abordagem porque o proprietário ou desenvolvedor do aplicativo deve girar o certificado.
- **Diretor de serviço e segredo**: Embora você possa usar um diretor de serviço e um segredo para autenticar para Key Vault, nós não recomendamos isso. É difícil girar automaticamente o segredo bootstrap que é usado para autenticar para Key Vault.


## <a name="key-vault-roles"></a>Funções do Key Vault

Use a tabela a seguir para entender melhor como o Cofre da Chave pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

| Função | Problema declarado | Solucionado pelo Cofre da Chave do Azure |
| --- | --- | --- |
| Desenvolvedor de um aplicativo do Azure |"Quero escrever um aplicativo para o Azure que use chaves para assinatura e criptografia. Mas eu quero que essas chaves sejam externas da minha aplicação para que a solução seja adequada para uma aplicação que seja distribuída geograficamente. <br/><br/>Quero que essas chaves e segredos sejam protegidos, sem precisar escrever o código sozinho. Também quero que essas chaves e segredos sejam fáceis para eu usar dos meus aplicativos, com ótimo desempenho." |√ As chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são protegidas pelo Azure, usando módulos de segurança de hardware, comprimentos da chave e algoritmos padrão da indústria.<br/><br/>  √ As chaves são processadas em HSMs que residem nos mesmos datacenters do Azure que os aplicativos. Esse método permite uma maior confiabilidade e uma latência reduzida em comparação às chaves que residem em um local separado, como no local. |
| Desenvolvedor de SaaS (software como serviço) |"Não quero a responsabilidade ou a responsabilidade potencial pelas chaves e segredos dos meus clientes. <br/><br/>Quero que os clientes possuam e gerenciem suas chaves para que eu possa me concentrar em fazer o que faço de melhor, que é fornecer os principais recursos de software." |√ Os clientes podem importar suas próprias chaves para o Azure e gerenciá-las. Quando um aplicativo SaaS precisa realizar operações criptográficas usando as chaves dos clientes, o Key Vault faz essas operações em nome do aplicativo. O aplicativo não vê as chaves dos clientes. |
| Diretor-chefe de segurança (CSO) |"Quero saber se nossos aplicativos estão em conformidade com os HSMs de Nível 2 do FIPS 140-2 para um gerenciamento seguro de chaves. <br/><br/>Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso. <br/><br/>E embora usemos vários serviços e recursos do Azure, quero gerenciar as chaves de um único local no Azure." |√ Os HSMs têm certificação FIPS 140-2 Nível 2.<br/><br/>√ O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia suas chaves.<br/><br/>√ Uso de chave é registrado praticamente em tempo real.<br/><br/>√ O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure, quais sejam as regiões com suporte e quais aplicativos as usem. |

Qualquer pessoa com uma assinatura do Azure pode criar e usar cofres de chaves. Embora o Key Vault beneficie desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador de uma organização que gerencia outros serviços do Azure. Por exemplo, esse administrador pode fazer login com uma assinatura do Azure, criar um cofre para a organização em que armazenar chaves e, em seguida, ser responsável por tarefas operacionais como essas:

- Criar ou importar uma chave ou segredo
- Revogar ou excluir uma chave ou segredo
- Autorizar usuários ou aplicativos a acessar o cofre da chave para que eles possam gerenciar ou usar suas chaves e segredos
- Configurar o uso de chaves (por exemplo, assinar ou criptografar)
- Monitorar o uso de chaves

Esse administrador, então, dá aos desenvolvedores URIs para chamar de seus aplicativos. Este administrador também fornece informações de registro de uso chave ao administrador de segurança. 

![Visão geral do funcionamento do Azure Key Vault][1]

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para saber mais, confira o [guia do desenvolvedor do Cofre da Chave](developers-guide.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [proteger seu cofre](secure-your-key-vault.md)).

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).
