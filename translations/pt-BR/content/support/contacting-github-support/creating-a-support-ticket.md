---
title: Creating a support ticket
intro: 'You can use the {% ifversion ghae %}{% data variables.contact.ae_azure_portal %}{% else %}{% data variables.contact.support_portal %}{% endif %} to create a support ticket and speak to {% data variables.contact.github_support %}.'
shortTitle: Creating a ticket
versions:
  fpt: '*'
  ghec: '*'
  ghes: '*'
  ghae: '*'
redirect_from:
  - /enterprise/admin/enterprise-support/preparing-to-submit-a-ticket
  - /admin/enterprise-support/preparing-to-submit-a-ticket
  - /admin/enterprise-support/receiving-help-from-github-support/preparing-to-submit-a-ticket
  - /enterprise/admin/guides/enterprise-support/reaching-github-enterprise-support
  - /enterprise/admin/enterprise-support/reaching-github-support
  - /admin/enterprise-support/reaching-github-support
  - /admin/enterprise-support/receiving-help-from-github-support/reaching-github-support
  - /enterprise/admin/enterprise-support/submitting-a-ticket
  - /admin/enterprise-support/submitting-a-ticket
  - /admin/enterprise-support/receiving-help-from-github-support/submitting-a-ticket
  - /articles/submitting-a-ticket
  - /github/working-with-github-support/submitting-a-ticket
topics:
  - Support
---

{% ifversion fpt or ghec or ghes %}

## About support tickets

{% data reusables.support.zendesk-old-tickets %}

{% ifversion fpt %}
{% data reusables.support.free-and-paid-support %}
{% endif %}

{% ifversion ghes or ghec %}
{% data reusables.enterprise-accounts.support-entitlements %}
{% endif %}

{% ifversion ghes %}
You can create your ticket using the {% data variables.contact.support_portal %} or, if you would like to include diagnostics with your support ticket, you can use the GitHub Enterprise Server Management Console.
{% endif %}

After you create your ticket, you can view your ticket and the responses from {% data variables.contact.github_support %} on the {% data variables.contact.contact_landing_page_portal %}. For more information, see "[Viewing and updating support tickets](/support/contacting-github-support/viewing-and-updating-support-tickets)."

## What to include in your support ticket

Providing {% data variables.contact.github_support %} with everything they need to understand, locate, and reproduce an issue will allow for a faster resolution and less back-and-forth between yourself and the support team. To ensure {% data variables.contact.github_support %} can assist you, consider the following points when you write your ticket:

- Obter informa????es que possam ajudar o {% data variables.contact.github_support %} a acompanhar, priorizar, reproduzir ou investigar o problema.
- Include full URLs, repository names, and usernames wherever possible.
- Reproduzir o problema, se necess??rio, e se preparar para compartilhar as etapas.
- Preparar uma descri????o completa do problema e dos resultados esperados.
- Copiar todas as mensagens de erro relacionadas ao problema.
- Determinar se h?? um n??mero de t??quete existente em qualquer outra comunica????o em andamento com o {% data variables.contact.github_support %}.
- Include relevant logs and attach any screenshots that demonstrate the issue.

{% ifversion ghes %}
## Determinar a pessoa mais indicada

Especialmente para t??quetes com prioridade {% data variables.product.support_ticket_priority_urgent %}, a pessoa que entrou em contato com {% data variables.contact.github_support %} deve:

 - Conhecer os sistemas, as ferramentas, as pol??ticas e as pr??ticas da empresa.
 - Ser usu??rio proficiente do {% data variables.product.product_name %}.
 - Ter todas as permiss??es de acesso a qualquer servi??o necess??rio para resolver o problema.
 - Ter autoriza????o para fazer as altera????es recomendadas na rede e em qualquer produto necess??rio.

{% endif %}

## Creating a support ticket{% ifversion ghes %} using the support portal{% endif %}

1. Navegue at?? o {% data variables.contact.contact_support_portal %}.
{% data reusables.support.submit-a-ticket %}

{% ifversion ghes %}

## Creating a ticket using the GitHub Enterprise Server Management Console

{% data reusables.enterprise_site_admin_settings.access-settings %}
{% data reusables.enterprise_site_admin_settings.management-console %}
{% data reusables.enterprise_management_console.type-management-console-password %}
{% data reusables.enterprise_management_console.support-link %}
1. Se voc?? quiser incluir diagn??sticos no seu ticket de suporte, em "Diagnostics" (Diagn??sticos), clique em **Download diagnostic info** (Baixar informa????es de diagn??stico) e salve o arquivo no local. Esse arquivo ser?? anexado ao seu t??quete de suporte. ![Screenshot of button labelled "Download diagnostics info" on Management Console Support page.](/assets/images/enterprise/support/download-diagnostics-info-button.png)
1. Para completar o seu ticket e exibir o {% data variables.contact.enterprise_portal %}, em "Abrir pedido de suporte", clique em **Nova solicita????o de suporte**. ![Screenshot of button labelled "New support request" on Management Console Support page.](/assets/images/enterprise/management-console/open-support-request.png)
{% data reusables.support.submit-a-ticket %}

{% endif %}

{% elsif ghae %}

Voc?? pode enviar um t??quete para suporte com {% data variables.product.prodname_ghe_managed %} de {% data variables.contact.ae_azure_portal %}.

## Pr??-requisitos

Para enviar um t??quete para {% data variables.product.prodname_ghe_managed %} em {% data variables.contact.ae_azure_portal %}, voc?? deve fornecer o ID para sua assinatura de {% data variables.product.prodname_ghe_managed %} no Azure ao seu Gerente de Conta do Cliente (CSAM) na Microsoft.

## Enviar um t??quete usando o {% data variables.contact.ae_azure_portal %}

Os clientes comerciais podem enviar um pedido de suporte no {% data variables.contact.contact_ae_portal %}. Clientes do governo devem usar os [Portal do Azure para clientes do governo](https://portal.azure.us/#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Para obter mais informa????es, consulte [Criar uma solicita????o de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) na documenta????o da Microsoft.

## Resolver problemas em {% data variables.contact.ae_azure_portal %}

{% data variables.product.company_short %} n??o pode solucionar problemas de acesso e de assinatura no portal do Azure. Para obter ajuda com o portal do Azure, entre em contato com o CSAM na Microsoft ou revise as seguintes informa????es.

- Se voc?? n??o puder efetuar o login no portal Azure, consulte [Problemas de login do Azure](https://docs.microsoft.com/en-US/azure/cost-management-billing/manage/troubleshoot-sign-in-issue) na documenta????o da Microsoft ou [envie uma solicita????o diretamente](https://support.microsoft.com/en-us/supportrequestform/84faec50-2cbc-9b8a-6dc1-9dc40bf69178).

- Se voc?? puder efetuar o login no portal do Azure, mas n??o puder enviar um t??quete para {% data variables.product.prodname_ghe_managed %}, revise os pr??-requisitos para enviar um t??quete. Para obter mais informa????es, consulte "[Pr??-requisitos](#prerequisites)".

{% endif %}

## Leia mais

- "[About GitHub Support](/support/learning-about-github-support/about-github-support)"
