---
title: 'Tutorial: Integração do Azure Active Directory com o Qlik Sense Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Qlik Sense Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: 5344354c05547d6d2a5e2762c70a97cc4222c464
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552332"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Tutorial: Integração do Qlik Sense Enterprise ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Qlik Sense Enterprise ao Azure Active Directory (Azure AD). Ao integrar o Qlik Sense Enterprise ao Azure AD, é possível:

* Controlar, no Azure AD, quem tem acesso ao Qlik Sense Enterprise.
* Habilitar seus usuários a fazerem logon automaticamente no Qlik Sense Enterprise com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Qlik Sense Enterprise habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 
* O Qlik Sense Enterprise dá suporte SSO iniciado por **SP**.
* O Qlik Sense Enterprise dá suporte ao **provisionamento just-in-time**

* Depois de configurar o Qlik Sense Enterprise, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionar Qlik Sense Enterprise da galeria

Para configurar a integração do Qlik Sense Enterprise com o Azure AD, você precisará adicionar o Qlik Sense Enterprise à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Qlik Sense Enterprise** na caixa de pesquisa.
1. Selecione **Qlik Sense Enterprise** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Qlik Sense Enterprise usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Qlik Sense Enterprise.

Para configurar e testar o SSO do Azure AD com o Qlik Sense Enterprise, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Qlik Sense Enterprise](#configure-qlik-sense-enterprise-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** – para ter um equivalente de Brenda Fernandes no Qlik Sense Enterprise que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Qlik Sense Enterprise**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon real, o identificador e a URL de resposta, que serão explicados posteriormente neste tutorial, ou entre em contato com a [equipe de suporte do Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) para obter esses valores. A porta padrão para as URLs é 443, mas você pode personalizá-la de acordo com a necessidade da sua organização.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Qlik Sense Enterprise.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Qlik Sense Enterprise**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-qlik-sense-enterprise-sso"></a>Configurar o SSO do Qlik Sense Enterprise

1. Prepare o arquivo XML de Metadados de Federação para que você possa carregar no servidor Qlik Sense.

    > [!NOTE]
    > Antes de carregar os metadados IdP para o servidor Qlik Sense, o arquivo precisa ser editado para remover as informações e garantir a operação correta entre o Azure AD e o servidor Qlik Sense.

    ![QlikSense][qs24]

    a. Abra o arquivo FederationMetaData.xml baixado do portal do Azure em um editor de texto.

    b. Procure o valor **RoleDescriptor**.  Há quatro entradas (dois pares de marcas de elemento de abertura e fechamento).

    c. Exclua todas as informações e as marcas RoleDescriptor do arquivo.

    d. Salve o arquivo e mantenha-o à mão para uso posterior neste documento.

2. Navegue para o QMC (Qlik Sense Qlik Management Console) como um usuário que pode criar configurações de proxy virtual.

3. No QMC, clique no item de menu **Virtual Proxies**.

    ![QlikSense][qs6]

4. Na parte inferior da tela, clique no botão **Criar novo**.

    ![QlikSense][qs7]

5. A tela de Edição de proxy virtual é exibida.  No lado direito da tela, há um menu para tornar as opções de configuração visíveis.

    ![QlikSense][qs9]

6. Com a opção de menu identificação marcada, insira as informações de identificação para a configuração de proxy virtual do Azure.

    ![QlikSense][qs8]  

    a. O campo **Descrição** é um nome amigável para a configuração do proxy virtual.  Insira um valor para uma descrição.

    b. O campo **Prefixo** identifica o ponto de extremidade do proxy virtual para se conectar ao Qlik Sense com o Logon Único do Azure AD.  Insira um nome de prefixo exclusivo para esse proxy virtual.

    c. **Tempo limite de inatividade de sessão (minutos)** é o tempo limite para conexões por meio desse proxy virtual.

    d. O **Nome de cabeçalho de cookie de sessão** é o nome do cookie que armazena o identificador de sessão para a sessão do Qlik Sense que um usuário recebe após uma autenticação bem-sucedida.  Esse nome deve ser exclusivo.

7. Clique na opção de menu de autenticação para torná-la visível.  Aparece a tela de autenticação.

    ![QlikSense][qs10]

    a. O menu suspenso **Modo de acesso anônimo** determina se os usuários anônimos podem acessar o Qlik Sense por meio do proxy virtual.  A opção padrão é Nenhum usuário anônimo.

    b. O menu suspenso **Método de autenticação** determina o esquema de autenticação que o proxy virtual usará.  Selecione SAML na lista suspensa.  Mais opções são exibidas como resultado.

    c. No **campo URI de host SAML**, insira o nome de host que os usuários precisam usar para acessar o Qlik Sense por meio desse proxy virtual SAML.  O nome do host é o uri do servidor Qlik Sense.

    d. Na **ID da entidade SAML**, insira o mesmo valor inserido para o campo URI de host SAML.

    e. **Metadados IdP SAML** é o arquivo editado anteriormente na seção **Editar metadados de federação de configuração do Azure AD**.  **Antes de carregar os metadados IdP, o arquivo precisa ser editado** para remover as informações e garantir a operação correta entre o Azure AD e servidor Qlik Sense.  **Veja as instruções acima se o arquivo ainda precisar ser editado.**  Se o arquivo foi editado, clique no botão Procurar e selecione o arquivo de metadados editado para carregá-lo para a configuração do proxy virtual.

    f. Insira a referência de nome ou o esquema de atributo para o atributo SAML que representa o **UserID** que o Azure AD envia ao servidor Qlik Sense.  Informações de referência de esquema estão disponíveis na configuração de postagem de telas do aplicativo do Azure.  Para usar o atributo de nome, insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Insira o valor para o **diretório de usuário** que será anexado aos usuários quando eles se autenticarem no servidor Qlik Sense por meio do Azure AD.  Valores embutidos em código devem estar entre **colchetes []** .  Para usar um atributo enviado na asserção SAML do Azure AD, digite o nome do atributo na caixa de texto **sem** colchetes.

    h. O **algoritmo de assinatura SAML** define o certificado de provedor (nesse caso, o servidor Qlik Sense) do serviço de assinatura para a configuração de proxy virtual.  Se o servidor Qlik Sense usar um certificado confiável gerado usando o Microsoft Enhanced RSA e o Provedor de Criptografia AES, altere o algoritmo de assinatura de SAML para **SHA-256**.

    i. A seção de mapeamento de atributo SAML permite que atributos adicionais como grupos sejam enviados ao Qlik Sense para uso em regras de segurança.

8. Clique na opção de menu **BALANCEAMENTO DE CARGA** para torná-la visível.  Aparece a tela Balanceamento de Carga.

    ![QlikSense][qs11]

9. Clique no botão **Adicionar novo nó de servidor**, selecione o(s) nó(s) de mecanismo aos quais o Qlik Sense enviará sessões para fins de balanceamento de carga e clique no botão **Adicionar**.

    ![QlikSense][qs12]

10. Clique na opção de menu Avançado para torná-la visível. Aparece a tela Avançado.

    ![QlikSense][qs13]

    A lista de permissões de Host identifica os nomes de host que são aceitos durante a conexão com o servidor Qlik Sense.  **Insira o nome do host que os usuários especificarão ao se conectar ao servidor Qlik Sense.** O nome do host é o mesmo valor que o uri de host SAML, sem https://.

11. Clique no botão **Aplicar**.

    ![QlikSense][qs14]

12. Clique em OK para aceitar a mensagem de aviso informando que proxies vinculados ao proxy virtual serão reiniciados.

    ![QlikSense][qs15]

13. No lado direito da tela, o menu Itens Associados é exibido.  Clique na opção de menu **Proxies**.

    ![QlikSense][qs16]

14. Aparece a tela de proxy.  Clique no botão **Vincular** na parte inferior para vincular um proxy ao proxy virtual.

    ![QlikSense][qs17]

15. Selecione o nó de proxy que dará suporte a essa conexão de proxy virtual e clique no botão **Vincular**.  Depois da vinculação, o proxy será listado nos proxies associados.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Após cerca de cinco a dez segundos, será exibida a mensagem Atualizar QMC.  Clique no botão **Atualizar QMC**.

    ![QlikSense][qs20]

17. Quando o QMC for atualizado, clique no item de menu **Virtual proxies**. A nova entrada de proxy virtual SAML é listada na tabela na tela.  Clique na entrada de proxy virtual.

    ![QlikSense][qs51]

18. Na parte inferior da tela, o botão Baixar metadados SP será ativado.  Clique no botão **Baixar metadados SP** para salvar os metadados em um arquivo.

    ![QlikSense][qs52]

19. Abra o arquivo de metadados sp.  Observe a entrada **entityID** e a entrada **AssertionConsumerService**.  Esses valores são equivalentes para o **identificador**, **URL de logon** e o **URL de resposta** na configuração de aplicativo do AD do Azure. Cole esses valores na seção **Domínio e URLs do Qlik Sense Enterprise** na configuração do aplicativo do Azure AD se não forem correspondentes e, em seguida, você deverá substituí-los no assistente de configuração de aplicativo do Azure AD.

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Criar um usuário de teste do Qlik Sense Enterprise

O Qlik Sense Enterprise dá suporte ao **provisionamento just-in-time**. Os usuários são automaticamente adicionados ao repositório 'USERS' do Qlik Sense Enterprise quando usam o recurso de SSO. Além disso, os clientes podem usar o QMC e criar um UDC (Conector de Diretório de Usuários) para preencher previamente os usuários no Qlik Sense Enterprise por meio do LDAP escolhido, como o Active Directory e outros.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Qlik Sense Enterprise no Painel de Acesso, você deverá se conectar automaticamente ao Qlik Sense Enterprise para o qual você configurar o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
