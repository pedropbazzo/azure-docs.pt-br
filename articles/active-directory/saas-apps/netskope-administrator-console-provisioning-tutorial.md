---
title: 'Tutorial: configurar o Netskope Console do Administrador para o provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Netskope Console do Administrador.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: b4ac2308eae3466dbb9d68895bca4a4de30fcebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304898"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Tutorial: configurar o Netskope Console do Administrador para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Netskope Console do Administrador e no Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Netskope Console do Administrador.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Netskope Console do Administrador](https://www.netskope.com/)
* Uma conta de usuário no Netskope Console do Administrador com permissões de administrador.

## <a name="assigning-users-to-netskope-administrator-console"></a>Atribuindo usuários ao Netskope Console do Administrador

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Netskope Console do Administrador. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Netskope Console do Administrador seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Dicas importantes para atribuir usuários ao Netskope Console do Administrador

* É recomendável que um único usuário do Azure AD seja atribuído ao Netskope Console do Administrador para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Netskope Console do Administrador, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurar Console do Administrador Netskope para provisionamento

1. Entre no console do [administrador do Netskope console do administrador](https://netskope.goskope.com/). Navegue até **página inicial > configurações**.

    ![Console de administração do Netskope Console do Administrador](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navegue até **ferramentas**. No menu **ferramentas** , navegue até **ferramentas de diretório > integração scim**.

    ![Ferramentas de Console do Administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Console do Administrador adicionar SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Role para baixo e clique no botão **Adicionar token** . Na caixa de diálogo **adicionar nome do cliente OAuth** , forneça um **nome de cliente** e clique no botão **salvar** .

    ![Netskope Console do Administrador adicionar token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Console do Administrador nome do cliente](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copie a **URL do servidor scim** e o **token**. Esses valores serão inseridos nos campos URL do locatário e token secreto, respectivamente, na guia provisionamento do aplicativo Netskope Console do Administrador no portal do Azure.

    ![Netskope Console do Administrador criar token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Adicionar o Netskope Console do Administrador da Galeria

Antes de configurar o Netskope Console do Administrador para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Netskope Console do Administrador da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Netskope Console do Administrador da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Netskope console do administrador**, selecione **Netskope console do administrador** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Netskope Console do Administrador na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configurando o provisionamento automático de usuário para o Netskope Console do Administrador 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Netskope Console do Administrador com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Netskope Console do Administrador seguindo as instruções fornecidas no tutorial de [logon único do Netskope console do administrador](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do Netskope Console do Administrador, confira [isso](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Netskope Console do Administrador no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Netskope Administrator Console**.

    ![O link de Console do Administrador Netskope na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira o valor da **URL do servidor scim** recuperado anteriormente na **URL do locatário**. Insira o valor do **token** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Netskope console do administrador. Se a conexão falhar, verifique se sua conta de Console do Administrador Netskope tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Netskope console do administrador**.

    ![Mapeamentos de usuário do Netskope Console do Administrador](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Netskope Console do Administrador na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Netskope console do administrador para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Netskope Console do Administrador atributos de usuário](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Netskope console do administrador**.

    ![Mapeamentos de grupo de Console do Administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Netskope Console do Administrador na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em Netskope console do administrador para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Netskope atributos de grupo de Console do Administrador](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para Netskope Console do Administrador, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para Netskope Console do Administrador escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Netskope console do administrador.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

