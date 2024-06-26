---
title: Set up test numbers for Zoom Phone Cloud Peering with Azure Communications Gateway
description: Learn how to configure Azure Communications Gateway with Zoom Phone Cloud Peering numbers for testing.
author: rcdun
ms.author: rdunstan
ms.service: communications-gateway
ms.topic: how-to
ms.date: 03/31/2024

#CustomerIntent: As someone deploying Azure Communications Gateway, I want to test my deployment so that I can be sure that calls work.
---

# Configure test numbers for Zoom Phone Cloud Peering with Azure Communications Gateway

To test Zoom Phone Cloud Peering with Azure Communications Gateway, you need test numbers. By following this article, you can set up the required user and number configuration in Zoom, on Azure Communications Gateway and in your network. You can then start testing.

## Prerequisites

You must have [chosen test numbers](deploy.md#prerequisites). You need two types of test number:
- Integration testing by your staff.
- Service verification (continuous call testing) by your chosen communication services.

You must complete the following procedures.

- [Prepare to deploy Azure Communications Gateway](prepare-to-deploy.md)
- [Deploy Azure Communications Gateway](deploy.md)
- [Connect Azure Communications Gateway to Zoom Phone Cloud Peering](connect-zoom.md)

You must provision Azure Communications Gateway with the numbers for integration testing during this procedure.

[!INCLUDE [communications-gateway-provisioning-permissions](includes/communications-gateway-provisioning-permissions.md)]

You must be an owner or admin of a Zoom account that you want to use for testing.

You must be able to contact your Zoom representative.

## Configure the test numbers for integration testing on Azure Communications Gateway

You must provision Azure Communications Gateway with the details of the test numbers for integration testing. This provisioning allows Azure Communications Gateway to identify that the calls should have Zoom service.

> [!IMPORTANT]
> Do not provision the service verification numbers for Zoom. Azure Communications Gateway routes calls involving those numbers automatically. Any provisioning you do for those numbers has no effect.

We recommend using the Number Management Portal (preview) to provision the test numbers. Alternatively, you can use Azure Communications Gateway's Provisioning API (preview).

# [Number Management Portal (preview)](#tab/number-management-portal)

You can configure numbers directly in the Number Management Portal, or by uploading a CSV file containing number configuration.

1. From the overview page for your Communications Gateway resource, find the **Number Management** section in the sidebar. Select **Accounts**.
1. Select **Create account**. Enter an **Account name** and select the **Enable Zoom Phone Cloud Peering** checkbox. Select **Create**.
1. Select the checkbox next to the new **Account name** and select **View numbers**.
1. Select **Create numbers**.
1. To configure the numbers directly in the Number Management Portal:
    1. Select **Manual input**.
    1. Select **Enable Zoom Phone Cloud Peering**.
    1. Optionally, enter a value for **Custom SIP header**.
    1. Add the numbers in **Telephone Numbers**.
    1. Select **Create**.
1. To upload a CSV containing multiple numbers:
    1. Prepare a `.csv` file. It must use the headings shown in the following table, and contain one number per line (up to 10,000 numbers).

        | Heading | Description  | Valid values |
        |---------|--------------|--------------|
        | `telephoneNumber`|The number to upload | E.164 numbers, including `+` and the country code |
        | `accountName` | The account to upload the number to | The name of an existing account |
        | `serviceDetails_zoomPhoneCloudPeering_enabled`| Whether Zoom Phone Cloud Peering is enabled | `true` or `false`|
        | `configuration_customSipHeader`| Optional: the value for a SIP custom header. | Can only contain letters, numbers, underscores, and dashes. Can be up to 100 characters in length. |

    1. Select **File Upload**.
    1. Select the `.csv` file that you prepared.
    1. Select **Upload**.

# [Provisioning API (preview)](#tab/provisioning-api)

The API allows you to indicate to Azure Communications Gateway which service you're supporting for each number, using _account_ and _number_ resources.

- Account resources are descriptions of your customers (typically, an enterprise), and per-customer settings for service provisioning.
- Number resources belong to an account. They describe numbers, the services (for example, Zoom) that the numbers make use of, and any extra per-number configuration.

Use the Provisioning API for Azure Communications Gateway to:

1. Provision an account to group the test numbers. Enable Zoom service for the account.
1. Provision the details of the numbers you chose under the account. Enable each number for Zoom service.

For example API requests, see [Create an account to represent a customer](/rest/api/voiceservices/#create-an-account-to-represent-a-customer) and [Add one number to the account](/rest/api/voiceservices/#add-one-number-to-the-account) or [Add or update multiple numbers at once](/rest/api/voiceservices/#add-or-update-multiple-numbers-at-once) in the _API Reference_ for the Provisioning API.

---

## Configure users in Zoom with the test numbers for integration testing

Upload the numbers for integration testing to Zoom. When you upload numbers, you can optionally configure Zoom to add a header containing custom contents to SIP INVITEs. You can use this header to identify the Zoom account for the number or indicate that these numbers are test numbers. For more information on this header, see Zoom's _Zoom Phone Provider Exchange Solution Reference Guide_.

Use [https://support.zoom.us/hc/en-us/articles/360020808292-Managing-phone-numbers](https://support.zoom.us/hc/en-us/articles/360020808292-Managing-phone-numbers) to assign the numbers for integration testing to the user accounts that you need to use for integration testing. Integration testing is part of preparing for live traffic.

> [!IMPORTANT]
> Do not assign the service verification numbers to Zoom user accounts. In the next step, you will ask your Zoom representative to configure the service verification numbers for you.

## Provide Zoom with the details of the service verification numbers

Ask your Zoom representative to set up the resiliency and failover verification tests using the service verification numbers. Zoom must map the service verification numbers to datacenters in ascending numerical order. For example, if you allocated +19075550101 and +19075550102, Zoom must map +19075550101 to the datacenters for DID 1 and +19075550102 to the datacenters for DID 2.

This ordering matches how Azure Communications Gateway routes calls for these tests, so allows Azure Communications Gateway to pass the tests.

## Update your network's routing configuration

Update your network configuration to route calls involving all the test numbers to Azure Communications Gateway. For more information about how to route calls to Azure Communications Gateway, see [Call routing requirements](reliability-communications-gateway.md#call-routing-requirements).

## Next step

> [!div class="nextstepaction"]
> [Prepare for live traffic](prepare-for-live-traffic-zoom.md)

