---
title: 'Tutorial: configurar o provisionamento de entrada do workday no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do WORKDAY para o Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 7d47c21da1279271b12933a2e4642abcce622600
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015476"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Tutorial: configurar o WORKDAY para o provisionamento de usuário do Azure AD
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar dados de trabalho do workday no Azure Active Directory. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do workday forem usuários somente na nuvem que não precisam de uma conta do AD local. Se os usuários exigirem apenas conta do AD local ou conta do AD e do Azure AD, consulte o tutorial em configurar o [WORKDAY para Active Directory](workday-inbound-tutorial.md) provisionamento de usuário. 

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário do Azure Active Directory](../app-provisioning/user-provisioning.md) integra-se com a [API de Recursos Humanos do Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para provisionar contas de usuários. Os fluxos de trabalho de provisionamento de usuário do Workday com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidade e recurso humanos:

* **Contratando novos funcionários** – quando um novo funcionário é adicionado ao workday, uma conta de usuário é criada automaticamente em Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md), com write-back do endereço de email para o workday.

* **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no workday (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS compatíveis com o Azure ad](../app-provisioning/user-provisioning.md).

* **Encerramentos de funcionários** -quando um funcionário é encerrado no workday, sua conta de usuário é automaticamente desabilitada em Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

* **Recontratação de funcionário** -quando um funcionário é recontratado no workday, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo da sua preferência) para Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esse WORKDAY para Azure Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida, baseados em nuvem para o provisionamento de usuário do Workday

* Organizações que exigem o provisionamento direto de usuário do WORKDAY para Azure Active Directory

* Organizações que exigem que os usuários sejam provisionados usando os dados obtidos do workday

* Organizações que usam Microsoft 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para usuários somente em nuvem. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – do WORKDAY para Azure Active Directory:** Nesse fluxo, eventos de trabalho (como novas contratações, transferências, terminações) ocorrem pela primeira vez no workday e, em seguida, os dados de evento fluem para Azure Active Directory. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no Azure AD.
* **Fluxo de write-back – do Active Directory local para o workday:** Depois que a criação da conta for concluída no Active Directory, ela será sincronizada com o Azure AD por meio de Azure AD Connect e informações como email, nome de usuário e número de telefone podem ser gravados novamente no workday.

  ![Visão geral](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertração ou novas contratações/transferências/encerramentos) no funcionário central do workday
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do workday EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD determina a alteração e invoca a operação criar/atualizar/habilitar/desabilitar para o usuário no Azure AD.
4. Se o aplicativo de [write-back do workday](workday-writeback-tutorial.md) estiver configurado, ele recuperará atributos como email, nome de usuário e número de telefone do Azure AD. 
5. O serviço de provisionamento do Azure AD define email, nome de usuário e número de telefone no workday.

## <a name="planning-your-deployment"></a>Planejamento da implantação

Configurar o provisionamento de usuário controlado por RH da nuvem do WORKDAY para o Azure AD requer um planejamento considerável que abrange diferentes aspectos, como:

* Determinando a ID de correspondência 
* Mapeamento de atributos
* Transformação de atributo 
* Filtros de escopo

Consulte o plano de [implantação de RH de nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter diretrizes abrangentes sobre esses tópicos. 

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no Workday

Consulte a seção [Configurar o usuário do sistema de integração](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) para criar uma conta de usuário do sistema de integração do workday com permissões para recuperar dados do trabalhador. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Configurar o provisionamento de usuário do WORKDAY para o Azure AD

As seções a seguir descrevem as etapas para configurar o provisionamento de usuário do Workday para o Azure AD para implantações somente em nuvem.

* [Adicionar o aplicativo conector de provisionamento do Azure AD e criar a conexão com o Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributo do Workday e do Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento do Azure AD e criar a conexão com o Workday

**Para configurar o provisionamento do Workday to Azure Active Directory para usuários somente na nuvem:**

1. Ir para <https://portal.azure.com>.

2. Na portal do Azure, procure e selecione **Azure Active Directory**.

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e, em seguida, selecione a categoria **Todos**.

5. Pesquise o **workday no provisionamento de usuário do Azure ad**e adicione esse aplicativo da galeria.

6. Depois de adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**.

7. Altere o **Modo** de **Provisionamento** para **Automático**.

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário do Workday** – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Deve ser semelhante a: username@contoso4

   * **Senha do Workday –** Digite a senha da conta do sistema de integração do Workday

   * **URL da API de serviços Web do workday –** Insira a URL para o ponto de extremidade de serviços Web do WORKDAY para seu locatário. A URL determina a versão da API de serviços Web do workday usada pelo conector. 
   
     | Formato de URL | Versão da API WWS usada | Alterações de XPATH necessárias |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Sim |

      > [!NOTE]
     > Se nenhuma informação de versão for especificada na URL, o aplicativo usará o WWS (workday Web Services) v 21.1 e nenhuma alteração será necessária para as expressões de API XPATH padrão enviadas com o aplicativo. Para usar uma versão específica da API do WWS, especifique o número de versão na URL <br>
     > Exemplo: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se você estiver usando uma API do WWS v 30.0 +, antes de ativar o trabalho de provisionamento, atualize as **expressões da API XPath** no **mapeamento de atributos – > opções avançadas-> editar lista de atributos para o workday** referindo-se à seção [Gerenciando sua configuração](workday-inbound-tutorial.md#managing-your-configuration) e [referência de atributo workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.

   * Clique no botão **Conexão de Teste**.

   * Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se o URL do Workday e as credenciais são válidas no Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributo do Workday e do Azure AD

Nesta seção, você irá configurar o fluxo dos dados do usuário do Workday para o Azure Active Directory de usuários somente na nuvem.

1. Na guia Provisionamento em **Mapeamentos**, clique em **Sincronizar Trabalhos para Azure AD**.

2. No campo **Escopo do objeto de origem** é possível selecionar quais conjuntos de usuários no Workday devem estar no escopo de provisionamento para Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é “todos os usuários no Workday”. Filtros de exemplo:

   * Exemplo: Escopo para usuários com IDs do Trabalhador entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: Coincidir EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas trabalhadores contingentes e funcionários não regulares

      * Atributo: ContingentID

      * Operador: IS NOT NULL

3. No campo **Ações do objeto de destino** é possível filtrar globalmente quais ações podem ser realizadas no Azure AD. **Criar** e **Atualizar** são as mais comuns.

4. Na seção **Mapeamentos de atributos** é possível definir como os atributos individuais do Workday mapeiam atributos do Active Directory.

5. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

   * **Tipo de Mapeamento**

      * **Direto** – grava o valor do atributo do Workday no atributo AD, sem alterações

      * **Constante** - grava um valor de cadeia de caracteres constante estático para o atributo do AD

      * **Expressão** – Permite gravar um valor personalizado para o atributo do AD baseado em um ou mais atributos do Workday. [Para obter mais informações, consulte este artigo sobre expressões](../app-provisioning/functions-for-customizing-application-data.md).

   * **Atributo de origem** - O atributo de usuário do Workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizar a lista de atributos de usuário do Workday](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

   * **Atributo de destino** – O atributo do usuário no Azure AD.

   * **Corresponder objetos utilizando esse atributo** – se esse mapeamento deverá ou não ser utilizado para identificar usuários entre o Workday e o Azure AD com exclusividade. Normalmente, esse valor é definido no campo ID do Trabalhador para o Workday que normalmente é mapeado para o atributo ID do Funcionário (novo) ou um atributo de extensão no Azure AD.

   * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.

   * **Aplicar esse mapeamento**

     * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

     * **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário

6. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.


## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do Workday tiverem sido concluídas, você pode ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Save** (Salvar).

3. Essa operação dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no locatário do Workday. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Azure Active Directory. 

5. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os atributos de workday com suporte para o provisionamento de entrada](../app-provisioning/workday-attribute-reference.md)
* [Saiba como configurar o Write-back do workday](workday-writeback-tutorial.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o Workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


