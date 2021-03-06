---
title: Aplicativos de nuvem ou ações na política de Acesso Condicional – Azure Active Directory
description: O que são aplicativos de nuvem ou ações em uma política de Acesso Condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 658d18ed3c9eefeedffd82bbccedbde0621fe748
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145527"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: Aplicativos na nuvem ou ações

Os aplicativos de nuvem ou as ações são um sinal importante em uma política de Acesso Condicional. Políticas de Acesso Condicional permitem que administradores atribuam controles a aplicativos ou ações específicas.

- Os administradores podem escolher na lista de aplicativos que incluem aplicativos da Microsoft internos e [aplicativos integrados do Azure AD](../manage-apps/what-is-application-management.md) incluindo aplicativos da galeria, que não são da galeria e os publicados por meio do [Proxy de Aplicativo](../manage-apps/what-is-application-proxy.md).
- Os administradores podem optar por definir a política não baseada em um aplicativo de nuvem, mas em uma ação do usuário. A única ação com suporte é Registrar informações de segurança (versão prévia), permitindo o Acesso Condicional para impor controles em relação à [experiência combinada de registro de informações de segurança](../authentication/howto-registration-mfa-sspr-combined.md).

![Definir uma política de Acesso Condicional e especificar aplicativos de nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Muitos aplicativos de nuvem da Microsoft existentes estão incluídos na lista de aplicativos entre os quais você pode selecionar. 

Os administradores podem atribuir uma política de Acesso Condicional aos aplicativos de nuvem da Microsoft a seguir. Alguns aplicativos, como o Office 365 e o gerenciamento de Microsoft Azure, incluem vários aplicativos ou serviços filho relacionados. A lista a seguir não é exaustiva e está sujeita a alterações.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Banco de Dados SQL do Azure e Data Warehouse](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Análise do Microsoft Application Insights
- [Proteção de Informações do Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gerenciamento do Microsoft Azure](#microsoft-azure-management)
- Gerenciamento de Assinaturas do Microsoft Azure
- Microsoft Cloud App Security
- Portal de Controle de Acesso de Ferramentas do Microsoft Commerce
- Serviço de Autenticação de Ferramentas do Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registro do Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa da Microsoft no Bing
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Serviço do Power BI
- Project Online
- Skype for Business Online
- VPN (Rede Privada Virtual)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

O Microsoft 365 fornece serviços de produtividade e colaboração baseados em nuvem como o Exchange, SharePoint e Microsoft Teams. Microsoft 365 serviços de nuvem estão profundamente integrados para garantir experiências suaves e colaborativas. Essa integração pode causar confusão ao criar políticas, pois alguns aplicativos como o Microsoft Teams têm dependências de outros como SharePoint ou Exchange.

O aplicativo Office 365 torna possível direcionar esses serviços de uma só vez. É recomendável usar o novo aplicativo do Office 365, em vez de direcionar aplicativos de nuvem individuais para evitar problemas com [dependências de serviço](service-dependencies.md). Direcionar esse grupo de aplicativos ajuda a evitar problemas que possam surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicativos específicos da política se quiserem incluindo o aplicativo do Office 365 e excluindo os aplicativos específicos de sua escolha na política.

Aplicativos-chave incluídos no aplicativo cliente do Office 365:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Equipes da Microsoft
   - Exchange Online
   - SharePoint online
   - Microsoft 365 Serviço de Pesquisa
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Gerenciamento do Microsoft Azure

O aplicativo de Gerenciamento do Microsoft Azure inclui vários serviços subjacentes. 

   - Portal do Azure
   - Provedor do Azure Resource Manager
   - APIs de modelo de implantação clássico
   - Azure PowerShell
   - CLI do Azure
   - Portal do administrador de assinaturas do Visual Studio
   - Azure DevOps
   - Portal do Azure Data Factory

> [!NOTE]
> O aplicativo de Gerenciamento do Microsoft Azure se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não se aplica ao PowerShell do Azure AD, que chama o Microsoft Graph.

## <a name="other-applications"></a>Outros aplicativos

Além dos aplicativos Microsoft, os administradores podem adicionar qualquer aplicativo registrado no Azure AD a políticas de Acesso Condicional. Estes aplicativos podem incluir: 

- Aplicativos publicados por meio do [Proxy de Aplicativo do Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplicativos adicionados da galeria](../manage-apps/add-application-portal.md)
- [Os aplicativos personalizados não estão na galeria](../manage-apps/view-applications-portal.md)
- [Aplicativos herdados publicados por meio de redes e controladores de entrega de aplicativos](../manage-apps/secure-hybrid-access.md)
- Aplicativos que usam [logon único baseado em senha](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Como a política de acesso condicional define os requisitos para acessar um serviço, você não pode aplicá-lo a um aplicativo cliente (público/nativo). Em outras palavras, a política não é definida diretamente em um aplicativo cliente (público/nativo), mas é aplicada quando um cliente chama um serviço. Por exemplo, uma política definida no serviço SharePoint se aplica aos clientes que chamam o SharePoint. Uma política definida no Exchange se aplica à tentativa de acessar o email usando o cliente do Outlook. É por isso que aplicativos cliente (públicos/nativos) não estão disponíveis para seleção no seletor de Aplicativos de Nuvem e a opção de Acesso Condicional não está disponível nas configurações do aplicativo para o aplicativo cliente (público/nativo) registrado em seu locatário. 

## <a name="user-actions"></a>Ações do usuário

As ações do usuário são tarefas que podem ser executadas por um usuário. A única ação com suporte no momento é **Registrar informações de segurança**, que permite que a política de Acesso Condicional imponha quando os usuários habilitados para a tentativa de registro combinada registraram as informações de segurança deles. Mais informações podem ser encontradas no artigo [Registro de informações de segurança combinadas](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: condições](concept-conditional-access-conditions.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicativo cliente](service-dependencies.md)
