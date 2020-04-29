---
title: Criar uma conta de desenvolvedor da Microsoft | Azure Marketplace
description: Requisitos e etapas para criar uma conta de Desenvolvedor da Microsoft.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280364"
---
<a name="create-a-microsoft-developer-account"></a>Criar uma conta de Desenvolvedor da Microsoft
====================================

Este artigo descreve como se tornar um Desenvolvedor da Microsoft aprovado para publicação no Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Criar uma conta da Microsoft

Para iniciar o processo de publicação, você precisa concluir o registro no **Centro de Desenvolvimento da Microsoft**. Você usará a mesma conta registrada no **[Portal do Cloud Partner](https://cloudpartner.azure.com/)** para iniciar o processo de publicação.

### <a name="general-account-guidelines"></a>Diretrizes gerais de conta

Recomendamos que você tenha apenas uma conta Microsoft para suas ofertas do Azure Marketplace. Esta conta não deve ser específica para serviços ou ofertas.

O endereço que forma o nome de usuário deve estar no seu domínio e ser controlado por sua equipe de TI. Todas as atividades de publicação relacionadas devem ser feitas por meio dessa conta.

>[!WARNING]
>Palavras como "Azure" e "Microsoft" não têm suporte para o registro da conta Microsoft. Evite usar essas palavras para concluir a criação da conta e o processo de registro.

### <a name="company-account-guidelines"></a>Diretrizes de conta corporativa

Siga estas diretrizes se mais de uma pessoa precisar acessar a conta ao efetuar logon com a conta Microsoft que abriu a conta.

>[!IMPORTANT]
>Para permitir que vários usuários acessem sua conta do Centro de Desenvolvimento, é recomendável usar o Azure Active Directory para atribuir funções aos usuários individuais. Eles podem acessar a conta entrando com as credenciais individuais do Microsoft Azure Active Directory. Saiba mais em [Gerenciar usuários de contas](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Crie sua conta Microsoft usando um endereço de email que pertença ao domínio da empresa, mas não a um único indivíduo. Por exemplo, windowsapps\@fabrikam.com.
-   Limite o acesso a essa conta da Microsoft ao menor número possível de desenvolvedores.
-   Configure uma lista de distribuição por email corporativo que inclua cada pessoa que precisar acessar a conta do desenvolvedor e adicione esse endereço de email às informações de segurança. Isso permite que todos os funcionários da lista recebam códigos de segurança quando necessário e gerenciem as informações de segurança da respectiva conta Microsoft. Se não for possível configurar uma lista de distribuição, o proprietário da conta de email individual precisará estar disponível para acessar e compartilhar o código de segurança quando solicitado (por exemplo, quando novas informações de segurança são adicionadas à conta ou quando ela precisar ser acessada de um novo dispositivo).
-   Adicione um número de telefone da empresa que não exija um ramal e que possa ser acessado pelos principais membros da equipe.
-   De modo geral, os desenvolvedores usam dispositivos confiáveis para entrar na conta de desenvolvedor da empresa. Todos os principais membros da empresa devem ter acesso a esses dispositivos confiáveis. Isso reduzirá a necessidade de códigos de segurança que precisam ser enviados durante o acesso à conta.
-   Se precisar permitir acesso à conta de um PC não confiável, limite esse acesso a um máximo de cinco desenvolvedores. De modo ideal, esses desenvolvedores devem acessar a conta de computadores que compartilham o mesmo local geográfico e de rede.
-   Com frequência, revise as [informações de segurança da empresa](https://account.live.com/proofs/Manage) para garantir que tudo esteja atualizado.

>[!IMPORTANT]
>Sua conta de desenvolvedor deve ser acessada basicamente de PCs confiáveis. Isso é essencial, pois há um limite para o número de códigos gerados por conta, por semana. Além de garantir uma experiência de logon mais eficiente.
>
>Confira mais informações nas [diretrizes adicionais para contas da empresa e segurança](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Para criar uma conta Microsoft

1.  Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta existente.
2.  Registre o email (de acordo com as diretrizes) como uma conta Microsoft usando este [link](https://signup.live.com/signup.aspx). Siga as seguintes instruções de criação de conta:

    - Quando registrar sua conta como uma conta Microsoft, você deverá fornecer um número de telefone válido para que o sistema envie um código de verificação de conta como uma mensagem de texto ou uma chamada automática.
    - Quando registrar sua conta como uma conta Microsoft, você deverá fornecer uma identificação de email válida para receber um email automatizado para verificação da conta.
    - Verifique o endereço de email enviado à DL.

    Agora, você está pronto para usar uma nova conta Microsoft no Centro de Desenvolvimento da Microsoft.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrar sua conta na Central de Desenvolvedores da Microsoft

O Centro de Desenvolvimento da Microsoft é usada para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e fornecer suas informações pessoais como uma maneira de validar sua identidade. A pessoa que fizer o registro deverá usar uma conta da Microsoft que seja compartilhada com a empresa **e a mesma conta deve ser usada no Portal do Cloud Partner.** Você deve verificar se sua empresa ainda não tem uma conta do Centro de Desenvolvimento da Microsoft antes de tentar criar uma. Durante o processo, coletaremos informações sobre o endereço da empresa, a conta bancária e as informações fiscais. Esses dados são geralmente obtidos por meio de contatos de finanças ou negócios.

>[!IMPORTANT]
>Você deve completar os seguintes componentes de perfil do Desenvolvedor para prosseguir pelas diversas fases da criação e implantação da oferta.

| Perfil do desenvolvedor     | Para iniciar o rascunho    | Preparando       | Publicar modelo gratuito e de solução   | Publicar comercial   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registro da empresa  | Imprescindível         | Imprescindível     | Imprescindível                             | Imprescindível             |
| ID do perfil fiscal        | Opcional          | Opcional      | Opcional                              | Imprescindível             |
| Conta bancária          | Opcional          | Opcional      | Opcional                              | Imprescindível             |

>[!NOTE]
>Há suporte para BYOL (Traga sua Própria Licença) apenas para máquinas virtuais, e é considerado uma oferta gratuita.

### <a name="register-your-company-account"></a>Registrar sua conta da empresa

1. Abra uma nova sessão de navegação do Internet Explorer InPrivate ou Chrome Incognito para garantir que você não tenha conectado a sessão em uma conta pessoal.

2. Acesse o [Centro de Desenvolvimento do Windows](https://dev.windows.com/registration?accountprogram=azure) para se registrar como um vendedor. Leia a observação importante a seguir antes de continuar.

   ![Verificação de conta Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Não deixe de registrar como uma conta da Microsoft a identificação de email ou a lista de distribuição (uma lista de distribuição é recomendável para remover a dependência de indivíduos) que você usará para registrar-se no Centro de Desenvolvimento. Caso contrário, faça o registro usando este link. Além disso, qualquer ID de email no domínio da empresa da Microsoft não poderá ser utilizado para o registro do Centro de Desenvolvimento.

   ![Entrada no Centro de Desenvolvimento](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Conclua o assistente “Ajude-nos a proteger sua conta” para verificar sua identidade pelo número de telefone ou endereço de email.

4. Em Informações de Registro de Conta, selecione o **País/região da conta** no menu suspenso e selecione **Avançar**.

   ![Selecionar país/região](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Vender-de" países/regiões: para vender seus serviços no Azure Marketplace, sua entidade registrada precisa ser de um dos países/regiões "vender-de" aprovados mostrados na lista suspensa. Essa restrição é por razões de pagamento e tributação. Para saber mais, confira as políticas de participação no Marketplace.

5. Selecione **Empresa** como seu "Tipo de conta" e selecione **Avançar**.

    >[!IMPORTANT]
    >Para entender melhor os tipos de conta e decidir qual é o melhor para você, confira a página “Tipos de conta, locais e tarifas” mostrada na próxima captura de tela.

    ![Tipos de conta para vendedores](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Insira o **Nome de exibição do editor**. Normalmente, esse é o nome da sua empresa.

    >[!NOTE]
    >O nome de exibição do editor inserido no Centro de Desenvolvimento não será exibido no Azure Marketplace depois que sua oferta for listada. Mas essas informações são necessárias para concluir o processo de registro.

7. Insira as **Informações de contato** para a verificação da conta.

    >[!IMPORTANT]
    >Você deve fornecer informações de contato precisas porque elas serão usadas em nosso processo de verificação para que sua empresa seja aprovada no Centro de Desenvolvimento.

8. Insira as informações de contato do **Aprovador da Empresa**. O aprovador da empresa é a pessoa que pode verificar que você está autorizado a criar uma conta no Centro de Desenvolvimento em nome da sua organização. Depois de fornecer essas informações, selecione **Avançar** para seguir para a **Seção de pagamento**.

    ![Identificar o aprovador da empresa](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Insira as informações de pagamento da sua conta. Se você tiver um código promocional que cubra o custo do registro, insira-o aqui. Caso contrário, forneça as informações de seu cartão de crédito (ou do PayPal nos mercados com suporte). Selecione **Avançar** para ir para a **Revisão** final.

   ![Registro de pagamento](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Revise as informações de sua conta e confirme que tudo está correto. Leia e aceite os termos e condições do [Contrato do Editor do Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Marque a caixa para indicar que você leu e aceitou os termos.

11. Selecione **Concluir** para confirmar seu registro. Será enviada uma mensagem de confirmação ao seu endereço de email.

12. Se você estiver planejando publicar somente ofertas gratuitas, selecione [Ir para Portal do Cloud Partner](https://cloudpartner.azure.com/) e pule para "Registrar sua conta no portal do cloud partner" neste artigo.

### <a name="commercial-offers"></a>Ofertas comerciais

Se estiver planejando publicar ofertas comerciais, como uma oferta de Máquina Virtual usando o modelo de cobrança por hora, você precisará fornecer as informações fiscais e bancárias. Para isso, entre em sua conta do Centro de Desenvolvimento e selecione **Atualizar suas informações de conta**. Siga as instruções na próxima seção, "Adicionar informações fiscais e bancárias".

>[!IMPORTANT]
>Você não poderá enviar por push uma oferta comercial para a produção sem fornecer informações fiscais e de conta bancária.

Se preferir atualizar suas informações fiscais e bancárias posteriormente, pule para "Registrar sua conta no portal do cloud partner" neste artigo.

>[!NOTE]
>É recomendável fornecer as informações fiscais e de conta bancária o mais rápido possível porque leva tempo para validar informações fiscais.

### <a name="add-banking-and-tax-information"></a>Adicionar informações fiscais e bancárias

Para publicar ofertas comerciais para compra, será preciso adicionar informações de pagamento e informações sobre impostos, e enviá-las para validação no Centro de Desenvolvimento da Microsoft.

**Para fornecer informações bancárias**

1.  Entre no [Centro de Desenvolvimento da Microsoft](https://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft.
2.  Selecione **Conta de pagamento** no menu à esquerda, em **Escolher método de pagamento**, selecione **Conta bancária** ou **PayPal**.

    >[!NOTE]
    >Se você tiver ofertas comerciais que os clientes compram no Marketplace, essa será a conta em que você receberá o pagamento para essas compras.
3.  Insira as informações de pagamento e selecione **Salvar**.

    >[!IMPORTANT]
    >Se você precisar atualizar ou alterar sua conta de pagamento, execute as etapas anteriores para substituir as informações atuais por novas.
    >
    >A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos confirmar a alteração da conta, como fizemos na primeira configuração da conta de pagamento. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.

4.  Selecione **Avançar**.

**Para fornecer informações fiscais**

1.  Entre no [Centro de Desenvolvimento da Microsoft](https://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft (se for necessário).
2.  No menu à esquerda, selecione **Perfil fiscal**.
3.  Na página **Configurar seu formulário de impostos**:
    - Selecione o país ou a região em que você tem residência permanente.
    - Selecione o país ou região de sua nacionalidade primária.
    - Selecione **Avançar**.
4.  Insira seus detalhes fiscais e, em seguida, clique em **Próximo**.

>[!WARNING]
>Você não poderá enviar suas ofertas comerciais por push para produção sem fornecer informações fiscais e de conta bancária em sua conta do Centro de Desenvolvedores da Microsoft.

### <a name="developer-center-registration-issues"></a>Problemas de registro do Centro de Desenvolvimento

Se você tiver problemas com o registro no Centro de Desenvolvimento, use as etapas a seguir para abir um tíquete de suporte.

1.  Vá para o [link de suporte](https://developer.microsoft.com/windows/support).
2.  Em **Entre em contato conosco**, selecione **Enviar um incidente**.
    ![Abrir um tíquete](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Para **Tipo de problema**, selecione "Ajuda com o Centro de Desenvolvimento", e para **Categoria**, selecione "Publicar e gerenciar aplicativos". Selecione **Iniciar email**.

    ![identificar tipo de problema](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Você receberá uma página de entrada. Use qualquer conta Microsoft para entrar. Se você não tiver uma conta Microsoft, então [crie uma](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).\

5.  Forneça informações detalhadas sobre o problema e selecione **Enviar** para enviar o tíquete.

    ![enviar um tíquete](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrar sua conta no Portal do Cloud Partner

Use o [Portal do Cloud Partner](https://cloudpartner.azure.com/) para publicar e gerenciar suas ofertas.

1.  Abra uma nova sessão de Janela anônima do Chrome ou navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.
2.  Vá para [portal do Cloud Partner](https://cloudpartner.azure.com/).
3.  Se você for um novo usuário e estiver entrando no [Portal do Cloud Partner](https://cloudpartner.azure.com/) pela primeira vez, entre com a mesma ID do email registrado na conta do Centro de Desenvolvimento. Isso garante que sua conta do Centro de Desenvolvimento e do Portal do Cloud Partner sejam vinculadas uma à outra.

Posteriormente, você pode adicionar os outros membros da empresa que estão trabalhando no aplicativo. Você poderá inseri-los como colaboradores ou proprietários no Portal do Cloud Partner, seguindo as etapas na próxima seção.

Se você for adicionado como colaborador/proprietário no Portal do Cloud Partner, poderá entrar com sua própria conta.

>[!TIP]
>As políticas de participação são descritas no site do Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Gerencie usuários como proprietários ou colaboradores no Portal do Cloud Partner

[Etapas para gerenciar usuários no Portal do Cloud Partner](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Próximas etapas

Agora que sua conta está criada e registrada, inicie o processo de publicação no Azure Marketplace.
