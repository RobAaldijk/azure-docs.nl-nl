---
title: 'Zelf studie: ServiceNow configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 3b592591f3d2190fdcc9ed7b3b12b2eca20a25a5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675838"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Zelf studie: ServiceNow configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel ServiceNow als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [GeServiceNowd](https://www.servicenow.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in ServiceNow
> * Gebruikers in ServiceNow verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en ServiceNow
> * Inrichtings groepen en groepslid maatschappen in ServiceNow
> * [Eenmalige aanmelding](servicenow-tutorial.md) bij ServiceNow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een [ServiceNow-exemplaar](https://www.servicenow.com/) van Calgary of hoger
* Een [ServiceNow Express-exemplaar](https://www.servicenow.com/) van Helsinki of hoger
* Een gebruikers account in ServiceNow met de rol Admin

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Stap 2. ServiceNow configureren voor ondersteuning bij het inrichten met Azure AD

1. Identificeer de naam van uw ServiceNow-exemplaar. U kunt de naam van het exemplaar vinden in de URL die u gebruikt voor toegang tot ServiceNow. In het onderstaande voor beeld is de naam van het exemplaar dev35214.

   ![ServiceNow-instantie](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Referenties voor een beheerder verkrijgen in ServiceNow. Navigeer naar het gebruikers profiel in ServiceNow en controleer of de gebruiker de rol Admin heeft. 

   ![Beheerdersrol ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Controleer of de volgende instellingen zijn **uitgeschakeld** in ServiceNow:

   1. **System Security**  >  **Instellingen voor strenge beveiliging** van systeem beveiliging selecteren  >  **basis verificatie voor inkomende schema aanvragen vereist** .
   2. **Systeem eigenschappen** selecteren  >  **webservices**  >  **vereisen basis autorisatie voor inkomende SOAP-aanvragen** .
     
   > [!IMPORTANT]
   > Als deze instelling is *ingeschakeld* , kan de inrichtings engine niet communiceren met ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Stap 3. ServiceNow toevoegen vanuit de Azure AD-toepassings galerie

Voeg ServiceNow toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van ServiceNow te starten. Als u eerder ServiceNow voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan ServiceNow, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Stap 5. Automatische gebruikers inrichting configureren voor ServiceNow 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor ServiceNow in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen** .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ServiceNow** in de lijst met toepassingen.

    ![De koppeling ServiceNow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten** .

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch** .

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Geef in de sectie **beheerders referenties** de referenties van uw ServiceNow en de gebruikers naam op. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met ServiceNow. Als de verbinding mislukt, zorg er dan voor dat uw ServiceNow-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm afbeelding toont de inrichtings pagina van de service, waar u beheerders referenties kunt invoeren.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan** .

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met ServiceNow** .

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar ServiceNow in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in ServiceNow voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de SERVICENOW-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met ServiceNow** .

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar ServiceNow in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in ServiceNow te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor **ServiceNow wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor ServiceNow door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan** .

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen** . De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* **InvalidLookupReference:** Bij het inrichten van bepaalde kenmerken, zoals afdeling en locatie in ServiceNow, moeten de waarden al bestaan in een verwijzings tabel in ServiceNow. U kunt bijvoorbeeld twee locaties (Seattle, Los Angeles) en drie afdelingen (verkoop, financiën, marketing) hebben in de tabel tabel **naam invoegen** in ServiceNow. Als u een gebruiker wilt inrichten waarbij zijn afdeling ' verkoop ' is en de locatie ' Seattle ' is, wordt hij met succes ingericht. Als u probeert een gebruiker in te richten met afdeling "verkoop" en locatie "LA", wordt de gebruiker niet ingericht. De locatie LA moet worden toegevoegd aan de verwijzings tabel in ServiceNow of het gebruikers kenmerk in azure AD moet worden bijgewerkt zodat dit overeenkomt met de indeling in ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Controleer de [kenmerk toewijzingen](../app-provisioning/customize-application-attributes.md) om het overeenkomende kenmerk te identificeren. Deze waarde moet aanwezig zijn op de gebruiker of groep die u wilt inrichten. 
* Bekijk de [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) om inzicht te krijgen in vereisten of beperkingen (bijvoorbeeld indeling om land code op te geven voor een gebruiker)
* Inrichtings aanvragen worden standaard verzonden naar https://{uw-instance-name}. Service-now. com/{table-name}. Als u een aangepaste Tenant-URL nodig hebt, kunt u de volledige URL opgeven in het veld exemplaar naam.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Deze fout wijst op een probleem met de communicatie tussen het ServiceNow-exemplaar. Controleer of de volgende instellingen zijn *uitgeschakeld* in ServiceNow:
   
   1. **System Security**  >  **Instellingen voor strenge beveiliging** van systeem beveiliging selecteren  >  **basis verificatie voor inkomende schema aanvragen vereist** .
   2. **Systeem eigenschappen** selecteren  >  **webservices**  >  **vereisen basis autorisatie voor inkomende SOAP-aanvragen** .

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)