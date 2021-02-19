---
tag: []
title: Setting up AD FS SAML SSO for Bitrise
redirect_from: []
summary: ''
menu:
  organizations:
    weight: 19
published: false

---
This guide provides step-by-step instructions on setting up SAML SSO using [Microsoft Active Directory Federation Services](https://docs.microsoft.com/en-us/windows-server/identity/active-directory-federation-services) (AD FS).

{% include message_box.html type="important" title="SAML SSO with Org Elite and Velocity plans" content="Please note that SAML SSO is only available for an Org with the [Org Elite and Velocity plans](https://www.bitrise.io/pricing). If you try to set up SAML SSO to an Org that has an [Org Standard subscription](https://www.bitrise.io/pricing/teams), the **Single Sign-On** tab will appear on the left menu bar in your **Account Settings** but you won’t be able to use it. Click **Upgrade to Org Elite** in the pop-up window to use SAML SSO in your Org. Since the SAML SSO feature is tied to the Org Elite and Velocity plans, if you decide to downgrade, you will lose this feature. All Org members will receive an email about the downgrade and you’ll have two weeks to re-upgrade to the Org Elite plan if you wish to use SAML SSO in your Org again."%}

## Before you start

Before connecting SAML SSO to your Organization (Org) on Bitrise, make sure:

* The AD FS administrator is at hand during the SAML SSO configuration process.
* As with other [Organization management actions](https://devcenter.bitrise.io/team-management/organizations/members-organizations/), only the Bitrise Org owner can set up SAML SSO to a Bitrise Organization.
* Your account on Bitrise has an Org with [Org Elite plan or Velocity plan](https://www.bitrise.io/pricing). If it doesn’t have an Org, go ahead and [create one](https://devcenter.bitrise.io/team-management/organizations/creating-org/). Setting up SAML SSO is the same for existing and brand new Orgs on Bitrise.

## Navigating to Single Sign On page of Bitrise

If you are an Org owner on Bitrise, you will have to use the **Single Sign-On** tab to set up a SAML SSO connection between Auth0 provider and your Bitrise Org.

1. On your Bitrise [Dashboard](https://app.bitrise.io/dashboard/builds) click your avatar, then click [**Account settings**](https://app.bitrise.io/me/profile#/overview) in the dropdown.![](/img/ssopage1.png)
2. The **Overview** page displays all the Orgs you’re a member of. Select the Org where you wish to set up the SAML SSO connection.![](/img/overview.png)
3. On the left menu bar, click the **Single Sign-On** tab which will take you to the **Enable Single Sign-On** page.![](/img/sso3.png)

## Configuring SAML SSO on Bitrise and AD FS

In this tutorial we will be jumping back and forth between Bitrise and AD FS so it is recommended that both tools are available during this process.

1. Add the **Identity provider sign-on URL** from AD FS in the **SAML SSO provider Single Sign-On URL (SSO URL)** field of Bitrise. For example, a valid value is `https://<AD FS URL>.com/adfs/ls`.

### Exporting a certificate

 1. You have to add a certificate generated by AD FS to the **SAML SSO provider certificate** field of the **Single Sign-On** page on Bitrise. If you’ve already created a certificate on AD FS, you can export it in PEM format from the AD FS server. If you haven’t created one yet, follow the instructions on [Microsoft's official guide: Obtain and Configure TS and TD Certificates for AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs#:\~:text=Open%20the%20AD%20FS%20Management,certificates%2C%20and%20then%20click%20OK.).
 2. In **Server Manager**, click **Tools**, and select **AD FS Management**.
 3. Select the **Certificates** folder on the left menu pane.
 4. Click a certificate under **Token-signing**. This brings up the **Certificates** window.![](/img/certtoken-1.jpg)
 5. Click **Details** tab on the **Certificate** page.![](/img/certificate-1.jpg)
 6. Hit **Next** on the **Certificate Export Wizard** window.![](/img/certwizard.jpg)
 7. Select the **Base-64 encoded X.509 (.CER)** the export file format. Click **Next**.![](/img/baseencoded.jpg)
 8. Give it a name in the **File name** field and hit **Save**.![](/img/filenamesave.jpg)
 9. Have a final look at your certificate settings. If you need to modify any of those, click the backward arrow next to **Certificate Export Wizard**. Otherwise, click **Finish**. Make sure you leave the AD FS window open as you will need it in a minute.![](/img/completewizard.jpg)
10. Open the exported certificate by a text editor and copy/paste its content to the **SAML SSO provider certificate** field on the **Enable Single Sign-On** page of Bitrise.
11. Save the settings by clicking **Configure SSO** on Bitrise.![](/img/configuresso-1.jpg) Let’s continue the SAML SSO configuration on AD FS by adding Bitrise.

### Adding Bitrise as a replying party trust to AD FS

Once you are finished with exporting the certificate, you can continue with adding Bitrise as a[ replying party trust to AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/create-a-relying-party-trust). The Add Relying Party Trust Wizard guides you through the steps.

 1. On AD FS, click **Replying Party Trust** on the left menu bar, then click **Relying Party Trust**.
 2. Select **Add Relying Party Trust** under **Actions**.![](/img/addreplyingpartytrust.jpg)
 3. On the **Welcome** page, select the **Claims aware** option and hit **Start**.![](/img/claimsaware.jpg)
 4. On the **Select Data Source** page, click the **Enter data about the relying party manually** option on the bottom of the page. Click **Next**.![](/img/selectdatasource.jpg)
 5. On the **Specify Display Name** page, add a **Display name,** for example `MyCorp`. Click **Next**.![](/img/specifydisplayname.jpg)
 6. Optionally, specifying a token encryption certificate on the **Configure Certificate** page is optional. Click **Next**.![](/img/optionalconfigure.jpg)
 7. On the **Configure UR**L page, select **Enable support for the SAML 2.0 WebSSO protocol** and copy paste the **Assertion Consumer Service URL (ACS URL)** from Bitrise to the **Replying party SAML 2 0 SSO service URL** field on AD FS. Click **Next**.![](/img/configureurl-1.jpg)
 8. On the **Configure Identifiers** page, add `Bitrise` in the **Relying party trust identifier** field. Click **Add**, then hit **Next**.![](/img/replyingidentifiers2.jpg)
 9. Do not modify the default access control policy on the **Choose Access Control Policy** page so that everyone can access this SAML SSO connection. Click **Next**.![](/img/permiteveryone.jpg)
10. On the **Ready to Add Trust** page, review the settings and click **Next**.![](/img/readytoaddtrust.jpg)
11. On the **Finish** page, tick the checkbox to edit claims issuance policy for Bitrise. Click **Close**.![](/img/finish.jpg)

### Configuring claim ruless

 1. On the **Edit Claim** **Issuance Policy** page, click the **Add Rule** button and hit **OK**.![](/img/editclaims.jpg)
 2. Create a **Send LDAP Attributes as Claims** claim rule and click **Next**.
 3. On the **Configure Claim Rule** page:
    * Add a rule name, for example Send E-mail, in the **Claim rule name** field.
    * Select an **Attribute Store** which is most likely the Active Directory.
    * In the **Mapping of LDAP attributes to outgoing claim types** field select E-mail Addresses.
 4. Click **Finish**.![](/img/configureclaimrule.jpg)
 5. Add another new rule that turns an E-mail to a formatter NameID. To do so, click **Add rule** in the **Edit Claim** **Issuance Policy** page again.
 6. On the **Select Rule Template**, select **Transform an Incoming Claim** option in the **Claim rule template** dropdown. Click **Next**.![](/img/chooseruletype.jpg)
 7. Give a name to the new rule, for example, `Transform E-mail`.
 8. Select **E-Mail Address** as the **Incoming Claim Type**.
 9. Select **NameId** as the **Outgoing claim type.**
10. Choose **Email** as the **Outgoing name ID format**.
11. Hit **OK** to finish the process.![](/img/newrule.jpeg)

## What’s next?

Learn how you can [log into your Org now that SAML SSO is set up](https://bitrise.atlassian.net/team-management/organizations/saml-sso-in-organizations/#logging-in-via-saml-sso-with-a-bitrise-account).

You might wan to [check out Org member’s SAML SSO statuses](https://bitrise.atlassian.net/team-management/organizations/saml-sso-in-organizations/#checking-saml-sso-statuses-on-bitrise) once the connection is up.

You might want to [enforce SAML SSO login to the Org](https://bitrise.atlassian.net/team-management/organizations/saml-sso-in-organizations/#enforcing-saml-sso-on-an-organization) once all Org members have authorized their SAML SSO connection to the Org.

Disabling SAML SSO is very simple - [learn how.](https://bitrise.atlassian.net/team-management/organizations/saml-sso-in-organizations/#disabling-an-organizations-saml-sso)