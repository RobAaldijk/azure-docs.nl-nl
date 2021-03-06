---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ADP | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: cf6973242e81cef53625a3b1eda4f2d92e1d5ffd
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308790"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ADP

In deze zelfstudie leert u hoe u ADP integreert met Azure Active Directory (Azure AD). Wanneer u ADP integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot ADP.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ADP.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op ADP waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ADP biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-adp-from-the-gallery"></a>ADP toevoegen vanuit de galerie

Om de integratie van ADP te configureren in Azure AD moet u ADP vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **ADP** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **ADP** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Eenmalige aanmelding van Azure AD configureren en testen voor ADP

Configureer en test eenmalige aanmelding van Azure AD met ADP met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ADP.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met ADP te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding bij ADP configureren](#configure-adp-sso)** : als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
    1. **[ADP-testgebruiker maken](#create-adp-test-user)** : als u een tegenhanger van B.Simon wilt maken in ADP die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b. Kopieer de waarde van **URL van gebruikerstoegang** om deze verderop in de zelfstudie te plakken in de sectie **Aanmeldings-URL configureren**.

    c. Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    d. Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Nee**.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ADP** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    Typ een URL in het vak **Id (Entiteits-id)** : `https://fed.adp.com`

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **ADP instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ADP.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ADP** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adp-sso"></a>Eenmalige aanmelding bij ADP configureren

Om eenmalige aanmelding te configureren in **ADP**, moet u het gedownloade **XML-bestand met metagegevens** uploaden naar de [ADP-website](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Dit proces kan enkele dagen duren.

### <a name="configure-your-adp-services-for-federated-access"></a>De ADP service(s) configureren voor federatieve toegang

>[!Important]
> Werknemers die federatieve toegang tot ADP-services nodig hebben, moeten worden toegewezen aan de ADP-service-app en daarna aan de specifieke ADP-service.
Nadat uw ADP-vertegenwoordiger de toewijzing heeft bevestigd, configureert u de ADP-service(s) en wijst u gebruikers toe of beheert u deze om de toegang van gebruikers tot de desbetreffende ADP-service te bepalen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **ADP** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **ADP** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.
1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit:  

    ![Gekoppelde eigenschappen van eenmalige aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b.  Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    c.  Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Ja**.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ADP** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.

1. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de **modus****Gekoppeld** om uw toepassing aan **ADP** te koppelen.

    ![Eenmalige aanmelding in de modus Gekoppeld](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Ga naar de sectie **Aanmeldings-URL configureren** en voer de volgende stappen uit:

    ![Aanmeldings-URL voor eenmalige aanmelding configureren](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Plak de **User access URL**, die u eerder hebt gekopieerd van het **tabblad Properties** van de ADP-app.
                                                             
    b. Hieronder ziet u de vijf apps die ondersteuning bieden voor verschillende **relaystate-URL's**. U moet de juiste **relaystate-URL** voor een bepaalde toepassing handmatig toevoegen aan de **User access URL**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. U moet vervolgens de wijzigingen **Opslaan**.

10. Nadat uw bevestiging heeft ontvangen van de ADP-vertegenwoordiger, begint u te testen met een of twee gebruikers.

    a. Wijs enkele gebruikers toe aan de ADP-service-app om federatieve toegang te testen.

    b. De test is geslaagd wanneer gebruikers via de ADP service-app in de galerie toegang krijgen tot hun ADP-service.
 
11. Als u bericht krijgt dat de test is geslaagd, wijst u de federatieve ADP-service toe aan individuele gebruikers of gebruikersgroepen en implementeert u de service voor uw werknemers. Deze toewijzing wordt later in de zelfstudie uitgelegd.

### <a name="create-adp-test-user"></a>ADP-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in ADP. Neem contact op met het [ADP-ondersteuningsteam](https://www.adp.com/contact-us/overview.aspx) om gebruikers toe te voegen aan het ADP-account. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ADP klikt, wordt u automatisch aangemeld bij de instantie van ADP waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [ADP proberen met Azure AD](https://aad.portal.azure.com)