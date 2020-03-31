---
title: Como corrigir regras padrão modificadas - Azure AD Connect | Microsoft Docs
description: Saiba como corrigir regras padrão modificadas que vêm com o Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036971"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras padrão modificadas no Azure AD Connect

Azure Active Directory (Azure AD) O Connect usa regras padrão para sincronização.  Infelizmente, essas regras não se aplicam universalmente a todas as organizações. Com base em suas necessidades, você pode precisar modificá-los. Este artigo discute dois exemplos das personalizações mais comuns, e explica a maneira correta de alcançar essas personalizações.

>[!NOTE] 
> A modificação das regras padrão existentes para alcançar uma personalização necessária não é suportada. Se você fizer isso, ele impede a atualização dessas regras para a versão mais recente em versões futuras. Você não terá as correções de erro que você precisa, ou novos recursos. Este documento explica como alcançar o mesmo resultado sem modificar as regras padrão existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras padrão modificadas
Começando com a versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra padrão modificada. Vá para **Apps on Desktop**e selecione **Synchronization Rules Editor**.

![Azure AD Connect, com o Editor de Regras de Sincronização destacado](media/how-to-connect-fix-default-rules/default1.png)

No Editor, quaisquer regras padrão modificadas são mostradas com um ícone de aviso na frente do nome.

![Ícone de aviso:](media/how-to-connect-fix-default-rules/default2.png)

 Uma regra desativada com o mesmo nome ao lado também aparece (esta é a regra padrão padrão).

![Editor de regras de sincronização, mostrando regra padrão padrão e regra padrão modificada](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
As seguintes personalizações comuns às regras padrão:

- Alterar o fluxo de atributos
- Alterar filtro de escopo
- Mudança condição de adesão

Antes de mudar qualquer regra:

- Desative o agendador de sincronização. O Agendador é executado a cada 30 minutos por padrão. Certifique-se de que não está começando enquanto você está fazendo mudanças e solucionando suas novas regras. Para desativar temporariamente o agendador, inicie o `Set-ADSyncScheduler -SyncCycleEnabled $false`PowerShell e execute .
 ![Os comandos PowerShell para desativar o agendador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de escopo pode resultar na exclusão de objetos no diretório de destino. Tenha cuidado antes de fazer qualquer alteração no escopo dos objetos. Recomendamos que você faça alterações em um servidor de estágio antes de fazer alterações no servidor ativo.
- Execute uma visualização em um único objeto, conforme mencionado na seção [Validar regra de sincronização,](#validate-sync-rule) depois de adicionar qualquer nova regra.
- Execute uma sincronização completa depois de adicionar uma nova regra ou modificar qualquer regra de sincronização personalizada. Essa sincronização aplica novas regras a todos os objetos.

## <a name="change-attribute-flow"></a>Alterar o fluxo de atributos
Existem três cenários diferentes para alterar o fluxo de atributos:
- Adicionando um novo atributo.
- Substituindo o valor de um atributo existente.
- Optando por não sincronizar um atributo existente.

Você pode fazer isso sem alterar as regras padrão padrão.

### <a name="add-a-new-attribute"></a>Adicione um novo atributo
Se você descobrir que um atributo não está fluindo do diretório de origem para o diretório de destino, use as [extensões azure AD Connect: extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para corrigi-lo.

Se as extensões não funcionarem para você, tente adicionar duas novas regras de sincronização, descritas nas seções a seguir.


#### <a name="add-an-inbound-sync-rule"></a>Adicionar uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a fonte do atributo é um espaço conector, e o alvo é o metaverso. Por exemplo, para ter um novo fluxo de atributos do Active Directory on-premises para o Azure Active Directory, crie uma nova regra de sincronização de entrada. Inicie o **Editor de Regras de Sincronização,** selecione **Entrada** como direção e selecione Adicionar **nova regra**. 

 ![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3a.png)

Siga sua própria convenção de nomeação para nomear a regra. Aqui, usamos **Custom In de AD - Usuário**. Isso significa que a regra é uma regra personalizada, e é uma regra de entrada do espaço conector do Active Directory para o metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Forneça sua própria descrição da regra, para que a manutenção futura da regra seja fácil. Por exemplo, a descrição pode ser baseada no objetivo da regra e por que ela é necessária.

Faça suas seleções para os campos **Sistema Conectado,** **Tipo de Objeto do Sistema Conectado**e Tipo de Objeto **Metaverso.**

Especifique o valor de precedência de 0 a 99 (quanto menor o número, maior a precedência). Para os campos **Tag**, **Habilitar sincronização de**senhas e **desativada,** use as seleções padrão.

Mantenha **o filtro de escopo** vazio. Isso significa que a regra se aplica a todos os objetos unidos entre o Sistema Conectado do Diretório Ativo e o metaverso.

Mantenha **as regras do Join** vazias. Isso significa que esta regra usa a condição de adesão definida na regra padrão padrão. Esta é outra razão para não desativar ou excluir a regra padrão padrão. Se não houver condição de adesão, o atributo não fluirá. 

Adicione transformações apropriadas para o seu atributo. Você pode atribuir uma constante, para fazer um fluxo de valor constante para o seu atributo de destino. Você pode usar mapeamento direto entre a origem ou o atributo de destino. Ou, você pode usar uma expressão para o atributo. Aqui estão várias [funções de expressão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) que você pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicionar uma regra de sincronização de saída
Para vincular o atributo ao diretório de destino, você precisa criar uma regra de saída. Isso significa que a fonte é o metaverso, e o alvo é o sistema conectado. Para criar uma regra de saída, inicie o **Synchronization Rules Editor,** altere a **Direção** para **Saída**e selecione **Adicionar nova regra**. 

![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Como com a regra de entrada, você pode usar sua própria convenção de nomeação para nomear a regra. Selecione o **Sistema Conectado** como inquilino do Azure AD e selecione o objeto do sistema conectado ao qual deseja definir o valor do atributo. Defina a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Mantenha **o filtro escopo** e as regras de **Ademadas** estejam vazias. Preencha a transformação como constante, direta ou expressora. 

Agora você sabe como fazer um novo atributo para um fluxo de objeto de usuário do Active Directory para o Azure Active Directory. Você pode usar essas etapas para mapear qualquer atributo de qualquer objeto para origem e destino. Para obter mais informações, consulte [Criando regras de sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [Prepare-se para provisionar os usuários](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Substituir o valor de um atributo existente
Você pode querer substituir o valor de um atributo que já foi mapeado. Por exemplo, se você sempre quiser definir um valor nulo a um atributo no Azure AD, basta criar uma regra de entrada apenas. Faça o valor `AuthoritativeNull`constante, flua para o atributo de destino. 

>[!NOTE] 
> Use `AuthoritativeNull` em `Null` vez de neste caso. Isso porque o valor não nulo substitui o valor nulo, mesmo que tenha menor precedência (um valor numério maior na regra). `AuthoritativeNull`, por outro lado, não é substituído por um valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincronize o atributo existente
Se você quiser excluir um atributo da sincronização, use o recurso de filtragem de atributos fornecido no Azure AD Connect. Inicie **o Azure AD Connect** a partir do ícone da área de trabalho e, em seguida, **selecione Personalizar opções de sincronização**.

![Azure AD Conectar opções adicionais de tarefas](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se de que **o aplicativo Azure AD e a filtragem de atributos** estão selecionados e selecione **Next**.

![Recursos opcionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Limpe os atributos que deseja excluir da sincronização.

![Atributos do Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Alterar filtro de escopo
O Azure AD Sync cuida da maioria dos objetos. Você pode reduzir o escopo de objetos e reduzir o número de objetos a serem exportados, sem alterar as regras padrão de sincronização padrão. 

Use um dos seguintes métodos para reduzir o escopo dos objetos que você está sincronizando:

- atributo cloudFiltered
- Filtragem da unidade de organização

Se você reduzir o escopo dos usuários que estão sendo sincronizados, a sincronização de hash de senha também será para os usuários filtrados. Se os objetos já estiverem sincronizados, depois de reduzir o escopo, os objetos filtrados serão excluídos do diretório de destino. Por essa razão, certifique-se de que você escopo com muito cuidado.

>[!IMPORTANT] 
> Não é recomendado aumentar o escopo de objetos configurados pelo Azure AD Connect. Isso dificulta que a equipe de suporte da Microsoft entenda as personalizações. Se você deve aumentar o escopo dos objetos, edite a regra existente, clone-a e desative a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo cloudFiltered
Você não pode definir esse atributo no Active Directory. Defina o valor deste atributo adicionando uma nova regra de entrada. Em seguida, você pode usar **Transformação** e **Expressão** para definir esse atributo no metaverso. O exemplo a seguir mostra que você não deseja sincronizar todos os usuários cujo nome de departamento começa com **HRD** (case-insensitive):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Primeiro convertemos o departamento de origem (Active Directory) para minúscula. Então, usando `Left` a função, pegamos apenas os três `hrd`primeiros caracteres e comparamos com . Se corresponder, o valor é `True`definido `NULL`como , caso contrário . Ao definir o valor como nulo, alguma outra regra com menor precedência (um valor numério mais alto) pode escrever para ele com uma condição diferente. Executar a visualização em um objeto para validar a regra de sincronização, conforme mencionado na seção [Validar regra de sincronização.](#validate-sync-rule)

![Criar opções de regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem de unidades organizacionais
Você pode criar uma ou mais unidades organizacionais (OUs) e mover os objetos que você não deseja sincronizar com essas OUs. Em seguida, configure a filtragem OU no Azure AD Connect. Inicie **o Azure AD Connect** a partir do ícone da área de trabalho e selecione as seguintes opções. Você também pode configurar a filtragem OU no momento da instalação do Azure AD Connect. 

![Azure AD Conectar tarefas adicionais](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e limpe os OUs que você não deseja sincronizar.

![Opções de filtragem de domain e OU do Azure AD Connect](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Mudança condição de adesão
Use as condições padrão de adesão configuradas pelo Azure AD Connect. Alterar as condições de adesão padrão torna difícil para o suporte da Microsoft entender as personalizações e suportar o produto.

## <a name="validate-sync-rule"></a>Validar a regra de sincronização
Você pode validar a regra de sincronização recém-adicionada usando o recurso de visualização, sem executar o ciclo de sincronização completo. No Azure AD Connect, selecione **Serviço de sincronização**.

![Azure AD Connect, com serviço de sincronização destacado](media/how-to-connect-fix-default-rules/default10.png)

Selecione **Pesquisa metaversa**. Selecione o objeto de escopo como **pessoa,** **selecione Adicionar cláusula**e mencione seus critérios de pesquisa. Em seguida, **selecione Pesquisar**e clique duas vezes no objeto nos resultados da pesquisa. Certifique-se de que seus dados no Azure AD Connect estão atualizados para esse objeto, executando importação e sincronização na floresta antes de executar esta etapa.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Em **Propriedades de objeto metaverso,** selecione **Conectores,** selecione o objeto no conector correspondente (floresta) e selecione **Propriedades...**.

![Propriedades de Objeto do Metaverso](media/how-to-connect-fix-default-rules/default12.png)

Selecione **Pré-visualização...**

![Propriedades de objeto do espaço do conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela Visualização, selecione **Gerar visualização** e **fluxo de atributo de importação** no painel esquerdo.

![Visualização](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, observe que a regra recém-adicionada é executada `cloudFiltered` no objeto e definiu o atributo como verdadeiro.

![Visualização](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra padrão, exporte ambas as regras separadamente, como arquivos de texto. Essas regras são exportadas como um arquivo de script PowerShell. Você pode compará-los usando qualquer ferramenta de comparação de arquivos (por exemplo, windiff) para ver as alterações. 
 
Observe que na regra `msExchMailboxGuid` modificada, o atributo é alterado para o tipo **Expressão,** em vez de **Direct**. Além disso, o valor é alterado para **NULL** e **executeOnce** opção. Você pode ignorar as diferenças identificadas e de precedência. 

![saída da ferramenta windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para corrigir suas regras para alterá-las de volta às configurações padrão, exclua a regra modificada e habilite a regra padrão. Certifique-se de que você não perca a personalização que está tentando alcançar. Quando estiver pronto, execute **a Sincronização Completa**.

## <a name="next-steps"></a>Próximas etapas
- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)



