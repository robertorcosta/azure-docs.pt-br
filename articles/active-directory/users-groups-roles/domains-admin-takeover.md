---
title: Tomada de administrador de um diretório não gerenciado – Azure AD | Microsoft Docs
description: Como assumir um nome de domínio DNS em uma organização não gerenciada do Azure AD (locatário de sombra).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834179"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Controlar um diretório não gerenciado como administrador no Azure Active Directory

Este artigo descreve duas maneiras de controlar um nome de domínio DNS em um diretório não gerenciado no Azure AD (Azure Active Directory). Quando um usuário de autoatendimento se inscreve em um serviço de nuvem que usa o Azure AD, eles são adicionados a um diretório do Azure AD não gerenciado com base em seu domínio de email. Para obter mais informações sobre a inscrição de autoatendimento ou "viral" para um serviço, consulte [o que é inscrição de autoatendimento para Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decida como você deseja controlar um diretório não gerenciado
Durante o processo de controle do administrador, você pode comprovar a propriedade conforme descrito em [Adicionar um nome de domínio personalizado para o Azure AD](../fundamentals/add-custom-domain.md). As próximas seções explicam a experiência de administração mais detalhadamente, mas aqui está um resumo:

* Ao realizar um [controle do administrador “interno”](#internal-admin-takeover) de um diretório não gerenciado do Azure, você é adicionado como o administrador global do diretório não gerenciado. Usuários, domínios ou planos de serviço não são migrados para outros diretórios que você administra.

* Ao realizar um [controle do administrador “externo”](#external-admin-takeover) de um diretório não gerenciado do Azure, você adiciona o nome de domínio DNS do diretório não gerenciado ao seu diretório do Azure gerenciado. Ao adicionar o nome de domínio, um mapeamento dos usuários para os recursos é criado no diretório gerenciado do Azure, assim os usuários podem continuar a acessar os serviços sem interrupção. 

## <a name="internal-admin-takeover"></a>Controle de administrador interno

Alguns produtos que incluem o SharePoint e OneDrive, como o Office 365, não dão suporte a controle externo. Se esse for o seu cenário ou se você for um administrador e deseja controlar um locatário de “sombra” não gerenciado criado pelos usuários que usaram inscrição de autoatendimento, faça isso por meio do controle de administrador interno.

1. Crie um contexto de usuário no locatário não gerenciado por meio da inscrição para Power BI. Para fins de conveniência do exemplo, essas etapas pressupõem esse caminho.

2. Abra o [site do Power BI](https://powerbi.com) e selecione **Iniciar gratuitamente**. Insira uma conta de usuário que usa o nome de domínio para a organização como, por exemplo, `admin@fourthcoffee.xyz`. Depois que você inserir o código de verificação, confira seu email para ver o código de confirmação.

3. No email de confirmação do Power BI, selecione **Sim, sou eu**.

4. Entre no centro de [Administração do Microsoft 365](https://portal.office.com/admintakeover) com a conta de usuário do Power bi. Você receberá uma mensagem instruindo-lhe a **Tornar-se o administrador** do nome de domínio que já foi verificado no locatário não gerenciado. selecione **Sim, quero ser o administrador**.
  
   ![primeira captura de tela para Tornar-se o administrador](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adicione o registro TXT para provar que você possui o nome de domínio **fourthcoffee.xyz** no seu registrador de nomes de domínio. Neste exemplo, é GoDaddy.com.
  
   ![Adicionar um registro txt para o nome de domínio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando os registros TXT do DNS forem verificados no seu registrador de nomes de domínio, você poderá gerenciar o locatário do Azure AD.

Após concluir as etapas anteriores, você será o administrador global do locatário Fourth Coffee no Office 365. Para integrar o nome de domínio aos outros serviços do Azure, você pode removê-lo do Office 365 e adicioná-lo a um locatário gerenciado diferente no Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Como adicionar o nome de domínio a um locatário gerenciado no Azure AD

1. Abra o [centro de administração do Microsoft 365](https://admin.microsoft.com).
2. Selecione a guia **usuários** e crie uma nova conta de usuário com um nome como o *usuário\@fourthcoffeexyz.onmicrosoft.com* que não usa o nome de domínio personalizado. 
3. Verifique se a nova conta de usuário tem privilégios de administrador global para o locatário do Azure AD.
4. Abra a guia **domínios** no centro de administração Microsoft 365, selecione o nome de domínio e selecione **remover**. 
  
   ![remover o nome de domínio do Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se você tiver usuários ou grupos no Office 365 que fazem referência ao nome de domínio removido, eles deverão ser renomeados para o domínio .onmicrosoft.com. Se você forçar a exclusão do nome de domínio, todos os usuários serão automaticamente renomeados, neste exemplo, para o *usuário\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Entre no [Centro de administração do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global para o locatário para o Azure AD.
  
7. Selecione **Personalizar nomes de domínio** e adicione o nome de domínio. Você precisará inserir os registros TXT do DNS para verificar a propriedade do nome de domínio. 
  
   ![domínio verificado como adicionado ao Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Os usuários do PowerBI ou do serviço Azure Rights Management que têm licenças atribuídas ao locatário do Office 365 devem salvar seus painéis, se o nome de domínio for removido. Eles devem entrar com um nome de usuário como *o\@usuário fourthcoffeexyz.onmicrosoft.com* , em vez de *usuário\@fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Controle do administrador externo

Se você já gerencia um locatário com os serviços do Azure ou o Office 365, não será possível adicionar um nome de domínio personalizado se ele já estiver verificado em outro locatário do Azure AD. No entanto, do seu locatário gerenciado no Azure AD, você poderá controlar locatário não gerenciado como um controle de administrador externo. O procedimento geral segue o artigo [Adicionar um domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md).

Quando você verificar a propriedade do nome de domínio, o Azure AD remove o nome de domínio do locatário não gerenciado e o migra para o locatário existente. O controle de administrador externo de um diretório não gerenciado requer o mesmo processo de validação de TXT do DNS que o controle de administrador interno. A diferença é que os itens a seguir também são movidos com o nome de domínio:

- Usuários
- Assinaturas
- Atribuições de licença

### <a name="support-for-external-admin-takeover"></a>Suporte para controle de administrador externo
O controle de administrador externo tem suporte nos seguintes serviços online:

- Azure Rights Management
- Exchange Online

Os planos de serviço com suporte incluem:

- PowerApps Gratuito
- PowerFlow Gratuito
- RMS para pessoas
- Microsoft Stream
- Avaliação gratuita do Dynamics 365

O tomada de administração externa não tem suporte para nenhum serviço que tenha planos de serviço que incluam o SharePoint, OneDrive ou Skype for Business; por exemplo, por meio de uma assinatura gratuita do Office. 

Opcionalmente, você pode usar a opção [**ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) para remover o nome de domínio do locatário não gerenciado e verificá-lo no locatário desejado. 

#### <a name="more-information-about-rms-for-individuals"></a>Mais informações sobre o RMS para pessoas

Para [RMS para pessoas](/azure/information-protection/rms-for-individuals), quando o locatário não gerenciado está na mesma região que o locatário que você possui, a [chave de locatário da Proteção de Informações do Azure](/azure/information-protection/plan-implement-tenant-key) e os [modelos de proteção padrão](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) criados automaticamente são movidos adicionalmente com o nome de domínio.

A chave e os modelos não são movidos quando o locatário não gerenciado está em uma região diferente. Por exemplo, se o locatário não gerenciado estiver na Europa e a organização que você possui estiver em América do Norte.

Embora o RMS para pessoas seja projetado para oferecer suporte à autenticação do Microsoft Azure Active Directory para abrir o conteúdo protegido, ele não impede que os usuários também protejam o conteúdo. Se os usuários tiverem protegido o conteúdo com a assinatura do RMS para pessoas físicas e a chave e os modelos não tiverem sido movidos, esse conteúdo não poderá ser acessado após o tomada de domínio.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlets do Azure AD PowerShell para a opção ForceTakeover
Você pode ver esses cmdlets usados no [exemplo do PowerShell](#powershell-example).

cmdlet | Uso
------- | -------
`connect-msolservice` | Quando solicitado, entre no seu locatário gerenciado.
`get-msoldomain` | Mostra os nomes de domínio associados ao locatário atual.
`new-msoldomain –name <domainname>` | Adiciona o nome de domínio ao locatário como Não verificado (nenhuma verificação de DNS foi executada ainda).
`get-msoldomain` | O nome de domínio agora está incluído na lista de nomes de domínio associados com seu locatário gerenciado, mas está listado como **Não verificado**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornece informações que serão colocadas no novo registro TXT do DNS para o domínio (MS=xxxxx). A verificação pode não acontecer imediatamente porque demora algum tempo para o registro TXT se propagar, portanto, aguarde alguns minutos antes de considerar a opção **-ForceTakeover**. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se seu nome de domínio ainda não foi verificado, você pode prosseguir com a opção **-ForceTakeover**. Ele verifica se o registro TXT foi criado e inicia o processo de tomada de controle.<li>A opção **-ForceTakeover** deve ser adicionada ao cmdlet apenas ao forçar um controle de administrador externo, como quando o locatário não gerenciado tem serviços do Office 365 que bloqueiam o controle.
`get-msoldomain` | A lista de domínios agora mostra o nome de domínio como **Verificado**.

> [!NOTE]
> A organização não gerenciada do Azure AD é excluída 10 dias depois que você exercita a opção de força de tomada externa.

### <a name="powershell-example"></a>Exemplo de PowerShell

1. Conecte o Azure AD usando as credenciais utilizadas para responder à oferta de autoatendimento:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Obter uma lista de domínios:
  
   ```powershell
   Get-MsolDomain
   ```
3. Execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Por exemplo:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copie o valor (o desafio) que é retornado deste comando. Por exemplo:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. No seu namespace DNS público, crie um registro DNS txt que contenha o valor que você copiou na etapa anterior. O nome deste registro é o nome do domínio pai, então se você criar este registro de recurso usando a função DNS do Windows Server, deixe o nome do Registro em branco e cole apenas o valor na caixa de texto.
6. Execute o cmdlet Confirm-MsolDomain para verificar o desafio:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Por exemplo:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Um desafio bem-sucedido fará com que você retorne ao prompt sem erros.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar Azure PowerShell](/powershell/azure/overview)
* [PowerShell do Azure](/powershell/azure/overview)
* [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
