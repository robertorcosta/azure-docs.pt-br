---
title: 'Tutorial: configurar o Write-back do workday no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Write-back de atributo do Azure AD para o workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: a1428a92857f48920c86ed7a3f0719fa42b38b24
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072026"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Tutorial: configurar o Write-back de atributo do Azure AD para o workday
O objetivo deste tutorial é mostrar as etapas que você precisa executar para os atributos de write-back do Azure AD para o workday. O aplicativo de provisionamento de write-back do workday dá suporte à atribuição de valores aos seguintes atributos do workday:
* Email comercial 
* Nome de usuário do workday
* Número de telefone fixo de trabalho (incluindo código de país, código de área, número e extensão)
* Sinalizador primário de número de telefone de trabalho fixo
* Número de celular do trabalho (incluindo código do país, código de área, número)
* Sinalizador de trabalho primário móvel

## <a name="overview"></a>Visão geral

Depois de configurar a integração de provisionamento de entrada usando o [WORKDAY para o aplicativo de provisionamento do AD local](workday-inbound-tutorial.md) ou WORKDAY para o aplicativo de provisionamento [do Azure ad](workday-inbound-cloud-only-tutorial.md) , você pode opcionalmente configurar o aplicativo de write-back do WORKDAY para gravar informações de contato, como email de trabalho e número de telefone, para workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário do workday write-back é ideal para:

* Organizações usando Microsoft 365 que desejam fazer write-back de atributos autoritativos gerenciados por ele (como endereço de email, nome de usuário e número de telefone) de volta ao workday

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no Workday

Consulte a seção [Configurar o usuário do sistema de integração](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) para criar uma conta de usuário do sistema de integração do workday com permissões para recuperar dados do trabalhador. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurando o write-back de atributo do Azure AD para o Workday

Siga essas instruções para configurar o write-back de endereços de email do usuário e o nome de usuário do Azure Active Directory para o Workday.

* [Adicionar o aplicativo conector de write-back e criar a conexão com o Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos de write-back](#part-2-configure-writeback-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar o aplicativo conector de write-back e criar a conexão com o Workday

**Para configurar o conector de Write-back do Workday:**

1. Ir para <https://portal.azure.com>.

2. Na portal do Azure, procure e selecione **Azure Active Directory**.

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e, em seguida, selecione a categoria **Todos**.

5. Pesquise por **Fazer write-back de Workday** e adicione esse aplicativo da galeria.

6. Depois de adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**.

7. Altere o **Modo** de **Provisionamento** para **Automático**.

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**  – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Ele deve ser semelhante a: *username\@contoso4*

   * **Senha do administrador –** digite a senha da conta do sistema de integração do Workday

   * **URL do locatário –** Digite a URL para o ponto de extremidade de serviços Web do Workday para seu locatário. Esse valor deve ser semelhante a: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`, onde *contoso4* é substituído pelo seu nome de locatário correto e *wd3-impl* é substituído pela cadeia de caracteres de ambiente correta (se necessário).

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se o URL do Workday e as credenciais são válidas no Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de write-back

Nesta seção, você irá configurar como os atributos de write-back fluem do Azure AD para o Workday. 

1. Na guia provisionamento, em **mapeamentos**, clique no nome do mapeamento.

2. No campo **escopo do objeto de origem** , você pode, opcionalmente, filtrar, quais conjuntos de usuários em Azure Active Directory devem fazer parte do write-back. O escopo padrão é “todos os usuários no Azure AD”.

3. Na seção **Mapeamentos de atributo**, atualize a ID correspondente para indicar o atributo no Azure Active Directory em que a ID de trabalho do Workday ou a ID de funcionário é armazenada. Um método de correspondência popular é sincronizar o ID de Trabalho do Workday ou o ID de Funcionário para extensionAttribute1-15 no Azure AD e, em seguida, utilizar esse atributo no Azure AD para corresponder os usuários no Workday.

4. Normalmente, você mapeia o atributo do Azure AD *userPrincipalName* para o atributo do Workday *UserID* e mapeia o atributo *mail* do Azure AD para o atributo *EmailAddress* do Workday. 

     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Use as diretrizes compartilhadas abaixo para mapear valores de atributo de número de telefone do Azure AD para o workday. 

     | Atributo de telefone do workday | Valor esperado | Diretrizes de mapeamento |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | Mapeamento de constante ou de expressão cuja saída é "true" ou um valor de cadeia de caracteres "false". |
     | WorkphoneLandlineCountryCodeName | [Código de país 3166-1 ISO de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento de constante ou de expressão cuja saída é um código de país de três letras. |
     | WorkphoneLandlineCountryCodeNumber | [Código de chamada do país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento de constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneLandlineNumber | Número de telefone completo, incluindo o código de área | Mapear para o atributo *telephoneNumber* . Use Regex para remover espaço em branco, colchetes e código de país. Consulte o exemplo a seguir. |
     | WorkphoneLandlineExtension | Número da extensão | Se *telephoneNumber* contiver a extensão, use Regex para extrair o valor. |
     | WorkphoneMobileIsPrimary | true/false | Mapeamento de constante ou mapeamento de expressão cuja saída é "verdadeiro" ou valor de cadeia de caracteres "falso" |
     | WorkphoneMobileCountryCodeName | [Código de país 3166-1 ISO de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento de constante ou de expressão cuja saída é um código de país de três letras. |
     | WorkphoneMobileCountryCodeNumber | [Código de chamada do país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento de constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneMobileNumber | Número de telefone completo, incluindo o código de área | Mapear para o atributo *móvel* . Use Regex para remover espaço em branco, colchetes e código de país. Consulte o exemplo a seguir. |

     > [!NOTE]
     > Ao invocar o serviço Web Change_Work_Contact workday, o Azure AD envia os seguintes valores constantes: <br>
     > * **Communication_Usage_Type_ID** é definido como a cadeia de caracteres constante "trabalho" <br>
     > * **Phone_Device_Type_ID** é definido como a cadeia de caracteres constante "Mobile" para números de telefone celular e "fixo" para números de telefone fixos. <br>
     > 
     > Você encontrará falhas de write-back se o seu locatário do workday usar Type_IDs diferentes. Para evitar essas falhas, você pode usar a tarefa do workday **manter IDs de referência** e atualizar a Type_IDs para corresponder aos valores usados pelo Azure AD. <br>
     >  

     **Expressões Regex de referência-exemplo 1**

     Use a expressão regular abaixo se o número de telefone no Azure AD for definido usando o formato necessário para redefinição de senha de autoatendimento (SSPR). <br>
     Exemplo: se o valor do número de telefone for + 1 1112223333->, a expressão Regex produzirá a saída 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Expressões Regex de referência-exemplo 2**

     Use a expressão regular abaixo se o número de telefone no Azure AD for definido usando o formato (XXX) XXX-XXXX. <br>
     Exemplo: se o valor do número de telefone for (111) 222-3333->, a expressão Regex produzirá 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do Workday tiverem sido concluídas, você pode ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

1. Na lista suspensa **escopo** , selecione **sincronizar todos os usuários e grupos**. Com essa opção, o aplicativo write-back gravará atributos mapeados de todos os usuários do Azure ad para o workday, sujeito às regras de escopo definidas em **mapeamentos**  ->  **escopo do objeto de origem**. 

   > [!div class="mx-imgBorder"]
   > ![Selecionar escopo de write-back](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > O aplicativo de provisionamento de write-back do workday não oferece suporte à opção **sincronizar somente usuários e grupos atribuídos**.
 

2. Clique em **Save** (Salvar).

3. Esta operação iniciará a sincronização inicial, o que pode levar um número variável de horas, dependendo de quantos usuários estiverem no diretório de origem. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique a guia **logs de provisionamento** no portal do Azure para ver quais ações o serviço de provisionamento executou. Os logs de auditoria lista todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários são importados da origem e exportados para o aplicativo de destino.  

5. Depois que a sincronização inicial for concluída, ela gravará um relatório de resumo na guia **provisionamento** , conforme mostrado abaixo.

     > [!div class="mx-imgBorder"]
     > ![Barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

* O aplicativo de write-back usa um valor predefinido para parâmetros **Communication_Usage_Type_ID** e **Phone_Device_Type_ID**. Se o seu locatário do workday estiver usando um valor diferente para esses atributos, a operação de write-back não terá sucesso. Uma solução alternativa sugerida é atualizar o Type_IDs no workday. 
* Quando o aplicativo de write-back é configurado para atualizar números de telefone secundários, ele não substitui o número de telefone secundário existente no workday. Ele adiciona mais um número de telefone secundário ao registro de trabalho. Não há nenhuma solução alternativa para esse comportamento. 


## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o Workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

