---
title: Como gerenciar uma conta de mercado comercial no Partner Center
description: Aprenda a gerenciar uma conta de marketplace comercial no Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 50ea56a8c743636f9fdd9105e5b07a868e71d3d0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262920"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Como gerenciar sua conta de marketplace comercial no Partner Center

Depois de [criar uma conta do Partner Center,](./create-account.md)você pode gerenciar sua conta e oferece usando o painel do [mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Neste artigo, vamos mergulhar em como gerenciar sua conta do Partner Center, incluindo como:

- [Acesse as configurações da sua conta do Partner Center](#access-your-account-settings)
- [Encontre seu Publisher ID, Symantec ID, ID do Vendedor, ID do usuário, ID MPN e inquilinos Azure AD](#account-details)
- [Atualizar informações de contato](#contact-info)
- [Configurar GUIDs de rastreamento para monitorar o uso do cliente](#tracking-guids)
- [Gerenciar usuários](#manage-users)
- [Gerenciar grupos](#manage-groups)
- [Gerenciar aplicativos AD do Azure](#manage-azure-ad-applications)
- [Definir as permissões e as funções de usuário](#define-user-roles-and-permissions)
- [Gerenciar inquilinos Azure AD (contas de trabalho)](#manage-tenants)
- [Gerenciar contratos de centro de parceiros](#agreements)

## <a name="access-your-account-settings"></a>Acesse as configurações da sua conta

Se você ainda não fez isso, você (ou o administrador da sua organização) deve acessar as [configurações](https://partner.microsoft.com/dashboard/account/management) da conta da sua conta do Partner Center para:
- Verifique o status de verificação da conta da sua empresa
- Confirme seu ID Symantec, ID do Vendedor, ID MPN, ID do Publisher e informações de contato, incluindo o contato do aprovador e do vendedor da empresa
- Crie contas de usuário para quem usará sua conta de negócios no Partner Center

### <a name="open-developer-settings"></a>Abrir configurações de desenvolvedor

As configurações da conta estão localizadas no canto superior direito do painel do [seu marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center. Selecione o ícone de engrenagem (próximo ao canto superior direito do painel) e selecione **configurações do Desenvolvedor**.

![Menu de configurações de conta no Partner Center](./media/dashboard-developer-settings.png)

Configurações **de Dentro da Conta,** você poderá visualizar o seu:
- **Detalhes da conta**: Tipo de conta e status da conta
- **IDs do publisher**: ID do vendedor, ID do usuário, ID do Publisher, inquilinos Azure AD, etc.
- **Informações de contato**: Nome de exibição do editor, nome de contato do vendedor, e-mail, telefone e endereço
- **Rastreamento de GUIDs**: Quaisquer GUIDs de rastreamento associados à sua conta

### <a name="account-details"></a>Detalhes da conta

Na seção de detalhes da conta, você pode ver informações básicas, como o **tipo de Conta** (Empresa ou Pessoa Física) e o status de **Verificação** da sua conta. Durante o processo de verificação da conta, essas configurações exibirão cada etapa necessária, incluindo verificação de e-mail, verificação de emprego e verificação de negócios. Você também pode atualizar seu e-mail aqui e reenviar a verificação, se necessário.

### <a name="publisher-ids"></a>Publisher IDs

Na seção IDs do Publisher, você pode ver seus **id symantec,** **ID do vendedor,** **ID do usuário,** **ID MPN**e **inquilinos Azure AD**. Esses valores são atribuídos pela Microsoft para identificar exclusivamente sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informações de contato

Na seção Informações de Contato, você pode ver o **nome de exibição do**seu Editor, informações de contato do **Vendedor** (nome de contato, e-mail, número de telefone e endereço para o vendedor da empresa) e o **aprovador da Empresa** (nome, e-mail e número de telefone do indivíduo com autoridade para aprovar decisões para a empresa).

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para a qual os rendimentos são enviados de suas vendas. Esta conta bancária deve estar no mesmo país onde você registrou sua conta do Partner Center.

Para configurar sua conta de pagamento, você precisa **associar sua conta Microsoft**:
1. Vá para a [página de visão geral](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) do mercado comercial no Partner Center.
2. Na seção Perfil, **selecione Associar sua Conta Microsoft**.
3. Quando solicitado, faça login com sua Conta Microsoft (MSA). Essa conta já não pode ser associada a outra conta do Partner Center.
4. Para concluir a configuração de sua conta de pagamento, faça login completamente fora do Partner Center e faça login novamente com sua conta Microsoft (em vez de sua conta de trabalho).

Agora que sua conta Microsoft está associada, para adicionar uma conta de pagamento, você precisará:
- **Escolha um método de pagamento**: Conta bancária ou PayPal
- **Adicionar informações de pagamento**: Isso pode incluir a escolha de um tipo de conta (verificação ou poupança), inserir o nome do titular da conta, número da conta e número de roteamento, endereço de cobrança, número de telefone ou endereço de e-mail do PayPal. *Para obter mais informações sobre como usar o PayPal como método de pagamento da sua conta e para saber se ele é suportado em sua região de mercado, consulte [as informações do PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos verificar a alteração da conta, assim como fazemos ao configurar a conta de pagamento pela primeira vez. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil de imposto

Revise sua situação atual do perfil fiscal, confirmando o tipo de **Entidade** e **as Informações do Certificado Fiscal** corretos. Selecione **Editar** para atualizar ou preencher os formulários necessários.

Para estabelecer sua situação fiscal, você deve especificar seu país de residência e cidadania e preencher os formulários fiscais apropriados associados ao seu país/região.

Independentemente do seu país de residência ou cidadania, você deve preencher formulários fiscais dos Estados Unidos para vender quaisquer ofertas através da Microsoft. Os parceiros que satisfazem certos requisitos de residência nos Estados Unidos devem preencher um formulário da Receita Federal W-9. Outros parceiros fora dos Estados Unidos devem preencher um formulário Do IRS W-8. Você pode preencher esses formulários online enquanto preenche seu perfil fiscal.

Não é necessário ter um ITIN (Número de Identificação de Contribuinte Individual) dos Estados Unidos para receber pagamentos da Microsoft ou reivindicar benefícios de tratados fiscais.

Você pode preencher e enviar seus formulários fiscais eletronicamente no Partner Center; na maioria dos casos, você não precisa imprimir e enviar formulários.

Os diversos países e regiões têm requisitos fiscais diferentes. O valor exato que você deve pagar em impostos depende dos países e regiões onde você vende suas ofertas. A Microsoft reemide vendas e use impostos em seu nome em alguns países. Esses países serão identificados no processo de listagem de sua oferta. Em outros países, dependendo de onde você está registrado, você pode precisar remeter as vendas e usar impostos para suas vendas diretamente para a autoridade tributária local. Além disso, os rendimentos das vendas que você recebe podem ser tributáveis como renda. Nós encorajamos fortemente que você entre em contato com a autoridade relevante para o seu país ou região que pode ajudá-lo a determinar melhor as informações fiscais certas para suas transações de vendas microsoft.

##### <a name="withholding-rates"></a>Retenção de impostos
As informações que você envia em seus formulários fiscais determinam a taxa de retenção de impostos apropriada. A retenção de impostos aplica-se somente a vendas realizadas nos Estados Unidos; as vendas feitas em locais fora dos EUA não estão sujeitas à retenção. As taxas de retenção podem variar, mas para a maioria dos desenvolvedores registrados fora dos Estados Unidos, a taxa padrão é de 30%. Você tem a opção de reduzir essa taxa, se o seu país/região tiver firmado um tratado de imposto de renda com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios de tratados fiscais
Se você está fora dos Estados Unidos, talvez possa tirar proveito de benefícios de tratados fiscais. Esses benefícios variam de país para país, e podem permitir que você reduza a quantidade de impostos que a Microsoft retém. Você pode reivindicar benefícios de tratados fiscais preenchendo a Parte II do formulário W-8BEN. Recomendamos que você entre em contato com os recursos apropriados em seu país ou sua região para determinar se esses benefícios se aplicam a você.

[Saiba mais sobre detalhes fiscais para desenvolvedores de aplicativos/jogos do Windows e editores do Azure Marketplace.](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)

#### <a name="payout-hold-status"></a>Status de espera de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, você tem a opção de colocar seus pagamentos em espera, o que impedirá o envio de pagamentos para sua conta. Se você optar por colocar seus pagamentos em espera, continuaremos registrando qualquer receita que você ganhar e forneceros os detalhes em seu **resumo de pagamento .** No entanto, não enviaremos nenhum pagamento para sua conta até que você remova o porão. 

Para colocar seus pagamentos em espera, acesse **Configurações da Conta**. Em **Detalhes financeiros**, na seção **Status de espera de pagamento**, alterne para **Ativado**. Você pode alterar seu status de espera de pagamento a qualquer momento, mas lembre-se de que sua decisão afetará o próximo pagamento mensal. Por exemplo, se você quiser manter o pagamento de Abril, certifique-se de definir seu status de dereção de pagamento para **On** antes do final de março.

Depois de definir o status de espera de pagamento para **Ativado**, todos os pagamentos ficarão em espera até que você alterne novamente para **Desativado**. Quando você fizer isso, você será incluído durante o próximo ciclo de pagamento mensal (desde que quaisquer limites de pagamento aplicáveis tenham sido cumpridos). Por exemplo, se você teve seus pagamentos suspensos, mas gostaria de ter um pagamento gerado em junho, então certifique-se de alternar o status de reclusão de pagamento para **Off** antes do final de maio.

> [!NOTE]
> Sua seleção **de status de reter pagamento** se aplica a todas **as** fontes de receita que são pagas através do Microsoft Partner Center, incluindo Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Você não pode selecionar um status de pagamento em espera diferente para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As configurações de gerenciamento de dispositivos se aplicam apenas à publicação UWP. [Saiba mais](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Rastreamento de GUIDs

Globalmente, os identificadores exclusivos (GUIDs) são números de referência únicos (com 32 dígitos hexadecimais) que podem ser usados para rastrear o uso do Azure. 

Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. A equipe do Armazenamento do Azure criou um [formulário gerador de GUID](https://aka.ms/StoragePartners) que enviará a você por email um GUID no formato correto e que pode ser reutilizado entre os sistemas de controle diferentes.

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar 2 GUIDS distintos:

*    Produto A no Azure Marketplace
*    Produto A no GitHub

Emissão de relatórios é feita, o valor de parceiro (ID de parceiro da Microsoft) e os GUIDs. Você também pode rastrear GUIDs em um nível mais granular alinhando-se a cada plano dentro de sua oferta.

Para obter mais informações, consulte o uso do [cliente Tracking Azure com ASQ GUIDs](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Crie um perfil de faturamento

Se você está publicando uma [oferta dynamics 365 para engajamento do cliente & Power Apps](./create-new-customer-engagement-offer.md) ou [Dynamics 365 for Operations,](./create-new-operations-offer.md) você precisa completar seu **perfil de faturamento**.

O endereço de cobrança é pré-preenchido de sua entidade legal, e você pode atualizar este endereço mais tarde. Os campos de ID do IMPOSTO e do IVA são opcionais.  O nome do país e da empresa não podem ser editados.

## <a name="multi-user-account-management"></a>Gerenciamento de contas multiusuários

O Partner Center aproveita [o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para acesso e gerenciamento de contas de vários usuários. O Azure AD da sua organização é automaticamente associado à sua conta do Partner Center como parte do processo de inscrição.

## <a name="manage-users"></a>Gerenciar usuários

A seção **Usuários** do Partner Center (em **Configurações de conta)** permite que você use o Azure AD para gerenciar os aplicativos usuários, grupos e Azure AD que tenham acesso à sua conta do Partner Center. Para gerenciar os usuários, você deve estar conectado com sua [conta de trabalho](./company-work-accounts.md) (o inquilino Azure AD associado). Para gerenciar os usuários dentro de uma conta de trabalho diferente/ inquilino, você precisará sair e, em seguida, entrar de volta como um usuário com permissões **manager** nessa conta de trabalho / inquilino.

Uma vez que você esteja conectado com sua conta de trabalho (inquilino Azure AD), você pode:
- [Adicionar ou remover usuários](#add-or-remove-users)
- [Alterar uma senha de usuário](#change-a-user-password)
- [Adicionar ou remover grupos](#add-or-remove-users)
- [Adicionar ou remover aplicativos AD do Azure](#add-new-azure-ad-applications)
- [Gerenciar chaves para um aplicativo Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir as permissões e as funções de usuário](#define-user-roles-and-permissions)

Tenha em mente que todos os usuários do Partner Center (incluindo grupos e aplicativos Azure AD) devem ter uma conta de trabalho ativa em um [inquilino Azure AD](#manage-tenants) que esteja associado à sua conta do Partner Center.

### <a name="add-or-remove-users"></a>Adicionar ou remover usuários

Sua conta deve ter permissões [**de nível de gerente**](#define-user-roles-and-permissions) para a conta de trabalho [(inquilino Azure AD)](./company-work-accounts.md) na qual você deseja adicionar ou editar usuários.

#### <a name="add-existing-users"></a>Adicionar usuários existentes

Para adicionar usuários à sua conta do Partner Center que já existem na conta de trabalho da sua empresa [(inquilino Azure AD)](./company-work-accounts.md):

1. Vá para **Usuários** (em **Configurações de conta)** e selecione **Adicionar usuários**.
2. Selecione um ou mais usuários na lista exibida. Você pode usar a caixa de pesquisa para procurar usuários específicos.
*Se você selecionar mais de um usuário para adicionar à sua conta do Partner Center, você deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários usuários com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher usuários, clique **em Adicionar selecionado**.
4. Na seção **Funções**, especifique funções ou permissões personalizadas para os usuários selecionados.
5. Clique em **Salvar**.

#### <a name="create-new-users"></a>Criar novos usuários

Para criar novas contas de usuário, você deve ter uma conta com permissões [**de administrador global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Vá para **Usuários** (em **Configurações de conta),** **selecione Adicionar usuários**e, em seguida, escolha Criar novos **usuários**.
1. Digite um primeiro nome, sobrenome e nome de usuário para cada novo usuário. 
1. Se quiser que o novo usuário tenha uma Conta de administrador global no diretório da organização, marque a caixa rotulada **Tornar esse usuário um Administrador Global no Azure AD, com controle total sobre todos os recursos de diretório**. Isso dará ao usuário acesso completo a todos os recursos administrativos no Azure AD de sua empresa. Eles poderão adicionar e gerenciar usuários na conta de trabalho da sua organização (inquilino Azure AD), embora não no Partner Center, a menos que você conceda à conta a função/permissões apropriadas.
1. Se você checou a caixa para **tornar este usuário um administrador Global,** você precisará fornecer um **e-mail de recuperação de senha** para que o usuário recupere sua senha, se necessário.
1. Na seção **Associação de grupo**, selecione qualquer grupo ao qual o novo usuário deve pertencer.
1. Na seção **Funções**, especifique funções ou permissões personalizadas para o usuário.
1. Clique em **Salvar**.

A criação de um novo usuário no Partner Center também criará uma conta para esse usuário na conta de trabalho (inquilino Azure AD) para a qual você está conectado. Fazer alterações no nome de um usuário no Partner Center fará as mesmas alterações na conta de trabalho da sua organização (inquilino Azure AD).

#### <a name="invite-new-users-by-email"></a>Convide novos usuários por e-mail

Para convidar usuários que não fazem parte da conta de trabalho da empresa (inquilino Azure AD) por e-mail, você deve ter uma conta com permissões [**de administrador global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Vá para **Usuários** (em **Configurações de conta),** **selecione Adicionar usuários**e escolha Convidar usuários por **e-mail**.
2. Insira um ou mais endereços de email (até dez), separados por vírgulas ou pontos e vírgulas.
3. Na seção **Funções**, especifique funções ou permissões personalizadas para o usuário.
4. Clique em **Salvar**.

Os usuários que você convidou receberão um convite por e-mail para entrar na sua conta do Partner Center. Uma nova conta de usuário convidado será criada em sua conta de trabalho (inquilino Azure AD). Cada usuário precisa aceitar o convite para que possa acessar sua conta.

Se você precisar reenviar um convite, visite a página **Usuários,** encontre o convite na lista de usuários, selecione seu endereço de e-mail (ou o texto que diz *Convite pendente*). Em seguida, na parte inferior da página, selecione **Reenviar convite**.

> [!NOTE]
> Se sua organização usar a [integração de diretórios](https://go.microsoft.com/fwlink/p/?LinkID=724033) para sincronizar o serviço de diretório local com seu Azure AD, você não poderá criar novos usuários, grupos ou aplicativos Azure AD no Partner Center. Você (ou outro admin em seu diretório local) precisará criá-los diretamente no diretório local antes de poder vê-los e adicioná-los no Partner Center.

#### <a name="remove-a-user"></a>Remover um usuário

Para remover um usuário da sua conta de trabalho (inquilino Azure AD), vá para **Usuários** (em **Configurações de Conta),** selecione o usuário que você gostaria de remover usando a caixa de seleção na coluna de extrema direita e escolha **Remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar se deseja remover os usuários selecionados.

#### <a name="change-a-user-password"></a>Alterar uma senha de usuário

Se um de seus usuários precisar alterar sua senha, ele pode fazê-lo se você forneceu um **e-mail de recuperação de senha** ao criar a conta de usuário. Você também pode atualizar a senha do usuário seguindo as etapas abaixo. Para alterar a senha de um usuário na conta de trabalho da empresa (inquilino Azure AD), você deve estar conectado em uma conta com permissões [**de administrador Global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Observe que isso mudará a senha do usuário no seu inquilino Azure AD, juntamente com a senha que eles usam para acessar o Partner Center.

1. Na página **Usuários** (em **Configurações de Conta),** selecione o nome da conta de usuário que deseja editar.
2. Selecione o botão **Redefinir senha** na parte inferior da página.
3. Uma página de confirmação aparecerá mostrando as informações de login para o usuário, incluindo uma senha temporária. Certifique-se de imprimir ou copiar essas informações e fornecê-las ao usuário, já que você não conseguirá acessar a senha temporária depois que sair dessa página.

## <a name="manage-groups"></a>Gerenciar grupos

Os grupos permitem que você controle várias funções e permissões do usuário em conjunto.

#### <a name="add-an-existing-group"></a>Adicionar um grupo existente

Para adicionar um grupo que já existe na conta de trabalho da sua organização (inquilino Azure AD) à sua conta do Partner Center:

1. Na página **Usuários** (em **Configurações de Conta),** selecione **Adicionar grupos**.
2. Selecione um ou mais grupos na lista exibida. Você pode usar a caixa de pesquisa para procurar grupos específicos.
Se você selecionar mais de um grupo para adicionar à sua conta do Partner Center, você deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher grupos, clique **em Adicionar selecionado**.
4. Na seção **Funções**, especifique funções ou permissões personalizadas para os grupos selecionados. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplica ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5. Clique em **Salvar**.

Quando você adiciona um grupo existente, cada usuário que é membro desse grupo poderá acessar sua conta do Partner Center, com as permissões associadas à função atribuída do grupo.

#### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo novo à sua conta do Partner Center:

1. Na página **Usuários** (em **Configurações de Conta),** selecione **Adicionar grupos**.
2. Na próxima página, selecione **Novo grupo**.
3. Insira o nome de exibição para o novo grupo.
4. Especifique funções ou permissões personalizadas para o grupo. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplica aqui, independentemente das funções/permissões associadas à sua conta individual.
5. Selecione os usuários para o novo grupo na lista que aparece. Você pode usar a caixa de pesquisa para procurar usuários específicos.
6. Quando terminar de selecionar usuários, clique em **Adicionar selecionado** para adicioná-los ao novo grupo.
7. Clique em **Salvar**.

Observe que esse novo grupo será criado na conta de trabalho da sua organização (inquilino Azure AD) também, não apenas em sua conta do Partner Center.

#### <a name="remove-a-group"></a>Remova um grupo

Para remover um grupo da sua conta de trabalho (inquilino Azure AD), vá para **Usuários** (em **Configurações de Conta),** selecione o grupo que você gostaria de remover usando a caixa de seleção na coluna de extrema direita e escolha **Remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar se deseja remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerenciar aplicativos AD do Azure

Você pode permitir que aplicativos ou serviços que fazem parte do Azure AD da sua empresa acessem sua conta do Partner Center.

#### <a name="add-existing-azure-ad-applications"></a>Adicionar aplicativos AD azure existentes

Para adicionar aplicativos que já existem no Azure Active Directory da sua empresa:

1. Na página **Usuários** (em **Configurações de Conta),** selecione **Adicionar aplicativos Azure AD**.
2. Selecione um ou mais aplicativos Azure AD na lista exibida. Você pode usar a caixa de pesquisa para procurar aplicativos Azure AD específicos. Se você selecionar mais de um aplicativo Azure AD para adicionar à sua conta do Partner Center, você deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários aplicativos Azure AD com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando tiver terminado de selecionar aplicativos Azure AD, clique em **Adicionar selecionado**.
4. Na seção **Funções**, especifique funções ou permissões personalizadas para os aplicativos Azure AD selecionados.
5. Clique em **Salvar**.

#### <a name="add-new-azure-ad-applications"></a>Adicionar novos aplicativos Azure AD

Se você quiser conceder acesso ao Partner Center a uma nova conta de aplicativo Azure AD, você pode criar uma na seção **Usuários.** Observe que isso criará uma nova conta na conta de trabalho da sua empresa (inquilino Azure AD), não apenas na sua conta do Partner Center. Se você estiver usando principalmente este aplicativo Azure AD para autenticação do Partner Center e não precisar que os usuários o acessem diretamente, você pode inserir qualquer endereço válido para o URI **de URL** e **ID do aplicativo,** desde que esses valores não sejam usados por qualquer outro aplicativo Azure AD em seu diretório.

1. Na página **Usuários** (em **Configurações de Conta),** selecione **Adicionar aplicativos Azure AD**.
2. Na próxima página, selecione **o aplicativo New Azure AD**.
3. Insira a **URL de Resposta** para o novo aplicativo Azure AD. Essa é a URL onde os usuários podem entrar e usar seu aplicativo Azure AD (às vezes, também conhecida como a URL do Aplicativo ou a URL de Logon). A **URL de Resposta** não pode ter mais de 256 caracteres e deve ser exclusiva em seu diretório.
4. Insira o **URI da ID do Aplicativo** para o novo aplicativo Azure AD. Este é um identificador lógico para o aplicativo Azure AD que é apresentado quando uma única solicitação de login é enviada ao Azure AD. Observe que o **App ID URI** deve ser único para cada aplicativo Azure AD em seu diretório. Este ID não pode ter mais de 256 caracteres. Para obter mais informações sobre o App ID URI, consulte [Integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. Na seção **Funções**, especifique funções ou permissões personalizadas para o aplicativo Azure AD.
6. Clique em **Salvar**.

Após adicionar ou criar um aplicativo Azure AD, você pode retornar à seção **Usuários** e selecionar o nome do aplicativo para analisar as configurações do aplicativo, incluindo a ID do locatário, a ID do cliente, a URL de resposta e o URI da ID do aplicativo.

#### <a name="remove-an-application"></a>Remover um aplicativo

Para remover um aplicativo da sua conta de trabalho (inquilino Azure AD), vá para **Usuários** (em **Configurações de Conta),** selecione o aplicativo que você gostaria de remover usando a caixa de seleção na coluna de extrema direita e escolha **Remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar se deseja remover os aplicativos selecionados.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gerenciar chaves para um aplicativo Azure AD

Se o aplicativo Azure AD ler e gravar dados no Microsoft Azure AD, ele precisará de uma chave. Você pode criar chaves para um aplicativo Azure AD editando suas informações no Partner Center. Você também pode remover as chaves que não são mais necessárias.

1. Na página **Usuários** (em **Configurações de Conta),** selecione o nome do aplicativo Azure AD. Você verá todas as chaves ativas para o aplicativo Azure AD, incluindo a data em que a chave foi criada e quando ela expirará. 
2. Para remover uma chave que não é mais necessária, **selecione Remover**.
3. Para adicionar uma nova tecla, **selecione Adicionar nova tecla**.
4. Você verá uma tela mostrando os **valores** **de ID do cliente** e da chave . Certifique-se de imprimir ou copiar essas informações, pois você não poderá acessá-la novamente depois de sair desta página.
5. Se você quiser criar mais chaves, selecione **Adicionar outra tecla**.

## <a name="define-user-roles-and-permissions"></a>Definir as permissões e as funções de usuário

Os usuários da sua empresa podem ser atribuídos as seguintes funções e permissões para o programa Commercial Marketplace no Partner Center:

- **Gerente**
  - Pode acessar todos os recursos da conta Microsoft, exceto as configurações de impostos e pagamento
  - Pode gerenciar usuários, funções e contas de trabalho (inquilinos)
- **Desenvolvedor**
  - Pode gerenciar e publicar ofertas
  - Pode ver alguns relatórios de editores

> [!NOTE]
> Para o programa de Mercado Comercial, os papéis de administrador global, contribuinte de negócios, contribuinte financeiro e de marketing não são utilizados. Atribuir essas funções aos usuários não tem efeito. Apenas as funções Manager e Developer concedem permissões aos usuários.

Para obter mais informações sobre o gerenciamento de funções e permissões em outras áreas do Partner Center, como Azure Active Directory (AD), Cloud Solution Provider (CSP), Control Panel Vendor (CPV), Guest users ou Microsoft Partner Network (MPN), consulte [Atribuir funções e permissões de usuários no Partner Center](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Gerenciar inquilinos

Um inquilino do Azure Active Directory (AD), também conhecido como sua "conta de trabalho" ao longo desta documentação, é uma representação de sua organização criada no portal Azure e ajuda você a gerenciar uma instância específica dos serviços de nuvem da Microsoft para seus usuários internos e externos. Se sua organização se inscreveu em um serviço de nuvem da Microsoft, como Azure, Microsoft Intune ou Office 365, um inquilino do Azure AD foi estabelecido para você.

Você pode configurar vários inquilinos para usar com o Partner Center. Qualquer usuário com a função **Gerenciador** na conta do Partner Center terá a opção de adicionar e remover os inquilinos azure AD da conta.  

### <a name="add-an-existing-tenant"></a>Adicionar um inquilino existente

Para associar outro inquilino Azure AD à sua conta do Partner Center:

1. Na página **Inquilinos** (em **Configurações de Conta),** selecione **Associar outro inquilino Azure AD**.
2. Insira suas credenciais do Azure AD para o locatário que deseja associar.
3. Revise os nomes da organização e do domínio da conta do inquilino do Azure AD. Para concluir a associação, selecione **Confirmar**.

Se a associação for bem sucedida, você estará pronto para adicionar e gerenciar usuários de conta na seção **Usuários** no Partner Center.

### <a name="create-a-new-tenant"></a>Crie um novo inquilino

Para criar um novo inquilino Azure AD com sua conta do Partner Center:

1. Na página **Inquilinos** (em **Configurações de Conta),** selecione **Criar um novo inquilino Azure AD**.
2. Insira as informações de diretório para seu novo Azure AD:
    - **Nome de domínio**: O nome exclusivo que usaremos para o seu domínio Azure AD, juntamente com ".onmicrosoft.com". Por exemplo, se você digitou "exemplo", seu domínio Azure AD seria "example.onmicrosoft.com".
    - **Email de contato**: um endereço de email onde possamos contatá-lo sobre a sua conta, se necessário.
    - **Informações de conta de usuário do administrador global**: o nome, sobrenome, nome de usuário e senha que você deseja usar para a nova conta de administrador global.
3. Selecione **Criar** para confirmar as novas informações de domínio e conta.
4. Faça login com seu novo administrador global do Azure AD e senha para começar a [adicionar e gerenciar usuários](#manage-users).

Para obter mais informações sobre a criação de novos inquilinos dentro do portal Azure, em vez de através do portal do Partner Center, consulte o artigo [Criar um novo inquilino no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Remova um inquilino

Para remover um inquilino da sua conta do Partner Center, encontre seu nome na página **Inquilinos** (nas **configurações da conta),** em seguida, **selecione Remover**. Você será solicitado a confirmar que você quer remover o inquilino. Uma vez que você fizer isso, nenhum usuário nesse inquilino poderá entrar na conta do Partner Center, e quaisquer permissões configuradas para esses usuários serão removidas.

Quando você remover um inquilino, todos os usuários que foram adicionados à conta do Partner Center daquele inquilino não poderão mais fazer login na conta.

> [!TIP]
> Você não pode remover um inquilino se você estiver atualmente conectado ao Partner Center usando uma conta no mesmo inquilino. Para remover um inquilino, você deve entrar no Partner Center como **gerente** de outro inquilino que esteja associado à conta. Se houver apenas um locatário associado com a conta, esse locatário só poderá ser removido após o acesso à conta da Microsoft que abriu a conta.

## <a name="agreements"></a>Contratos

A seção **Contratos** do Partner Center (em **Configurações de conta)** permite que você veja uma lista dos contratos de publicação que você autorizou. Esses acordos são listados de acordo com nome e número de versão, incluindo a data em que foi aceito e o nome do usuário que aceitou o acordo.

**As ações necessárias** podem aparecer no topo desta página se houver atualizações de acordo que precisem de sua atenção. Para aceitar um contrato atualizado, leia primeiro a versão do contrato vinculado e selecione **Aceitar acordo**.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de SaaS](./create-new-saas-offer.md)
