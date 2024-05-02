---
title: Working with Azure AD B2C Custom Policies #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Working with Azure AD B2C Custom Policies #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: cahublou #Required; a Microsoft employee's alias; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: Azure #Required; leave this attribute/value as-is.
ms.date: 04/24/2024 #Required; mm/dd/yyyy format.
contributor-type: community
---

# Working with Azure AD B2C Custom Policies

This post describes one of two sign-in options provided by Azure AD B2C tenant, which is custom policies (the other is user flows). With Custom Policies, we can build customized authentication flows based on our needs.

## Getting started

We should ensure the following before getting started:

- An active Azure AD B2C tenant
- A registered web application
- The necessary policy keys and register the Identity Experience Framework Apps
- Download the Azure AD B2C policy starter pack from GitHub, make the configurations and upload it to the tenant

## Add signing/encryption keys

Sign in to the Azure Portal, search for the Azure AD B2C tenant, and click Open B2C Tenant.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/01.PNG" alt-text="AzureADB2CSettings":::

From the overview page, under the Policies section, select **Identity Experience Framework**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/02.png" alt-text="IdentityExperienceFramework":::

## Create the signing key

Select **Manage** - **Policy Keys**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/03.png" alt-text="PolicyKeys":::

Select **Add**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/04.png" alt-text="AddImage":::

- From the Options section, select from the drop-down menu Generate.
- In the filed Name, type TokenSigningKeyContainer.
- In the Key type, choose RSA.
- In the Key usage, select Signature.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/05.png" alt-text="CreateKey":::

Select **Create**.

## Create the encryption key

- Select **Policy Keys** and then select **Add**.
- From the **Options** section, select from the drop-down menu **Generate**.
- In the field **Name**, type **TokenEncryptionKeyContainer**.
- In the **Key type**, choose **RSA**.
- In the **Key usage**, select **Encryption**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/06.png" alt-text="CreateaKey":::

Select **Create**.

## Register the IdentityExperienceFramework application

In the next step, we have to register the **IdentityExperienceFramework** application.
From the left-hand side blade select **Manage** - **App registrations** and then select **+ New registration**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/08.png" alt-text="RegisterIdentityExperienceFramework":::

In the field name, type **IdentityExperienceFramework**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/09.png" alt-text="RegisterAnApp":::

Under **Supported account types**, select **Accounts in this organizational directory only** (tenant name B2C only - Single tenant).

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/10.png" alt-text="SupportedAccountTypes":::

Under the **Redirect URI** section, select **Web**, and then type `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com`.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/11.png" alt-text="RedirectURI":::

Under the **Permissions** section, select the **Grant admin consent to openid and offline_access permissions** check box.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/12.png" alt-text="GrantAdminConsent":::

And click **Register**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/13.png" alt-text="RegisterBtn":::

## Expose the API by adding a scope

On the left-hand side blade, under the **Manage** section, select **Expose an API**,

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/14.png" alt-text="ExposeAnAPI":::

and then select  **+ Add a scope**,

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/15.png" alt-text="ExposeAnAPIScope":::

finally, select **Save and continue**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/16.png" alt-text="SaveContinue":::

Now, we have to type the values as shown in the image below to create a scope that allows custom policy execution in the Azure AD B2C tenant:

- Scope name: user_impersonation
- Admin consent display name: Access IdentityExperienceFramework
- Admin consent description: Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/17.png" alt-text="EditScope":::

Click **Add scope**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/18.png" alt-text="AddScope":::

## Register the ProxyIdentityExperienceFramework application

Go to **Manage** - **App registrations**, and then click **+ New registration**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/19.png" alt-text="RegisterProxyIdentityExperienceFramework":::

In the **Name** field, type **ProxyIdentityExperienceFramework**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/20.png" alt-text="RegisterApp2":::

Under **Supported account types**, select the **Accounts in this organizational directory only** radio button.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/21.png" alt-text="SupportedAccounts":::

Under the **Redirect URI** section, use the drop-down to select **Public client/native (mobile & desktop)**, and for **Redirect URI** type **myapp://auth**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/22.png" alt-text="RedirectURI2":::

Under the **Permissions** section, select the **Grant admin consent to openid and offline_access permissions** check box.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/23.png" alt-text="GrantAdminConsentToOpenID":::

And click the **Register** button.

## Specify that the application should be treated as a public client

On the left-hand side menu, select **Manage** - **Authentication**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/25.png" alt-text="Authentication":::

Under the **Advanced settings** section, in the **Allow public client flows**, set the **Enable the following mobile and desktop flows** to **Yes**.

> [!NOTE]
> Make user that "**allowPublicClient**": true is set in the application manifest.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/26.png" alt-text="AdvancedSettingsYes":::

And select **Save**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/27.png" alt-text="SaveSettings":::

Now, grant permissions to the API scope we exposed earlier in the **IdentityExperienceFramework** registration: In the left menu, select **Manage** - **API permissions**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/28.png" alt-text="APIPermissions":::

Under **Configured permissions**, select **Add a permission**.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/29.png" alt-text="ConfiguredPermissions":::

Under the Request API permissions section, click on the **My APIs** tab, and select the **IdentityExperienceFramework** application.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/30.png" alt-text="RequestAPIPermissions":::

Under the **Select Permissions** section, select the **user_impersonation** scope.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/31.png" alt-text="UserImpersonationScope":::

press the **Add permissions** button

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/31.png" alt-text="AddPermissionsbtn":::

and select **Grant admin consent for (tenant name)**.

## Working with the custom policy starter pack

After unzipping the custom policy starter pack, zip file, we will see the folders as the image below.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/00_1.PNG" alt-text="CustomPolicyStarterPackFolders":::

| **Folder Name**                   | **Description**                                                                                                  |
| -----------                   | --------------------------------------------------------------------------------------------------------     |
| LocalAccounts                 | Includes custom policies XML files for local accounts                                                        |
| SocialAccounts                | Includes custom policies XML files for social accounts                                                       |
| SocialAndLocalAccounts        | Includes custom policies XML files for both local and social accounts                                        |
| SocialAndLocalAccountsWithMfa | Includes custom policies XML files for both local and social accounts with multi-factor authentication (Mfa) |

## Configure the Custom Policies

Before we upload the custom policy XML files we must make some changes to the **TrustFrameworkExtensions.xml**.

| ProxyIdentityExperienceFramework | Item Key="client_id"       | Application (client) ID  | IdentityExperienceFrameworkAppId |
| ---------------------------------| ---------------------------- | ------------------------     | -------------------------------- |
| IdentityExperienceFramework | Item Key="IdTokenAudience"| Application (client) ID  | IdentityExperienceFramework   |

Find, open the **LocalAccounts/TrustFrameworkExtensions.xml**, search for the

```html
<TechnicalProfile Id="login-NonInteractive"> 
```

element and replace the values as the examples below, and save the files.

```html
<DisplayName>Local Account SignIn</DisplayName>

<TechnicalProfiles> <TechnicalProfile Id="login-NonInteractive">

<Metadata>

<Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>

<Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>

</Metadata>

<InputClaims>

<InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />

<InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />

</InputClaims>

```

```html
    <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
            <Item Key="client_id">########-####-####-####-############</Item>
            <Item Key="IdTokenAudience">########-####-####-####-############</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="########-####-####-####-############" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="########-####-####-####-############" />
          </InputClaims>

```

## Upload the Policies

The last step is to upload the custom policies to the Azure B2C tenant. Under the Policies section, select the Identity Experience Framework menu item in the B2C tenant in the Azure portal.

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/34.png" alt-text="IdentityExperienceFrameworkImage":::

Select **Upload custom policy**

:::image type="content" source="media/working-with-azure-ad-b2c-custom-policies/35.png" alt-text="UploadCustomPolicy":::

Upload the policy files, in the following order:

- TrustFrameworkBase.xml
- TrustFrameworkExtensions.xml
- SignUpOrSignin.xml
- ProfileEdit.xml
- PasswordReset.xml
- Every policy file we upload will add the B2C_1A_ prefix.

## Useful Links

- [What is Azure Active Directory B2C?](/azure/active-directory-b2c/overview)
- [Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)
- [Register a web application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)
- [Create custom policies in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-user-flows?pivots=b2c-custom-policy)
