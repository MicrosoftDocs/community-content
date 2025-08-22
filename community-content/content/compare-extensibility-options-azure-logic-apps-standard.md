---
title: Compare Extensibility Options in Azure Logic Apps (Standard)
description: Learn how to choose the best extensibility option for integration solutions that you build with Azure Logic Apps (Standard).
contributor-type: community
author: sebastianmeyer-1
ms.author: mlcc-owners
ms.reviewer: wsilveira, estfan
ms.service: azure
ms.topic: concept-article
ms.date: 05/28/2025
---

# Compare the extensibility options for Azure Logic Apps (Standard)

---

**Principal author**: [Sebastian Meyer](https://github.com/sebastianmeyer-1)

---

Azure Logic Apps (Standard) is an integration platform where you can design and automate business processes, often with little or no code. This platform supports developers at all experience levels and offers flexibility for implementing workflows.

As your business processes get more complex, you might experience difficulties representing solutions in low-code format, while keeping performance and efficiency at the same levels that your organization requires. For these scenarios, Azure Logic Apps (Standard) gives you the capability to combine low-code and pro-code approaches so that you can reduce complexity in your workflows. For example, you can blend inline code, custom code, and Azure Functions in your solutions. 

This article covers each option in detail and includes, practical advice, comparison criteria, and operational considerations. You can then choose an option based on your needs, whether you're migrating legacy BizTalk solutions, managing simple code snippets, or integrating with serverless architectures.

## Inline code

Azure Logic Apps (Standard) provides actions that support inline code execution in multiple programming languages. This feature is particularly useful when you need to quickly add logic without relying on external dependencies or set up extra deployment steps.

### Key attributes

The following table describes the main benefits that inline code provides:

| Benefit | Description |
|---------|-------------|
| **Simplicity and integration** | Inline code is directly embedded into your workflow, so you don't have to manage separate deployments or services. This close coupling makes inline code useful for lightweight tasks, such as data validation or small business rules. |
| **Multiple programming languages** | Inline code is the only pro-code solution in Azure Logic Apps (Standard) that supports multiple languages: <br><br>- [C\#](/azure/logic-apps/add-run-csharp-scripts) <br>- [JavaScript](/azure/logic-apps/add-run-javascript) <br>- [PowerShell](/azure/logic-apps/add-run-powershell-scripts) <br><br>**Note**: The JavaScript inline code action is also available in Azure Logic Apps (Consumption). |
| **Cloud and local development support** | Inline code actions are fully embedded in the workflow designer for Azure Logic Apps (Standard), so you can use these actions for authoring in both the Azure portal and Visual Studio Code. |

### Considerations

The following table describes considerations for using inline code:

| Consideration | Description |
|---------------|-------------|
| **Dependency support** | Inline code actions don't support injecting dependencies such as custom C\# assemblies. This limitation might pose a problem if your code logic depends on components that aren't available in supported .NET versions. |
| **Debugging** | Inline code helps simplify development by keeping everything in one place, but debugging complex logic might prove challenging. The workflow designer in Azure Logic Apps (Standard) offers some basic support for troubleshooting by providing access to workflow run history, inputs, outputs, and execution logs during testing. For complex logic, consider other tools that let you test and debug logic in isolation. |
| **Versioning and deployment** | Inline code is embedded in the workflow definition, so you can't control code versions separately from the workflow where the code exists. If you need to use your code across multiple workflows in the same application, you must duplicate that code in each workflow. This option isn't recommended for components that require independent versioning or use in multiple workflows. |

## Custom code

The custom code action in Azure Logic Apps (Standard) offers a way to seamlessly include more sophisticated programming logic, including legacy code written with the .NET Framework, as a custom function. This capability makes custom code suitable for modernizing legacy BizTalk solutions or extending workflows. This option is particularly valuable if you need to implement complex logic, reuse business logic in multiple workflows, or perform operations that exceed the limits that inline code can comfortably handle.

### Key attributes

The following table describes the main benefits that custom code functions provide:

| Benefit | Description |
|---------|-------------|
| **Azure Functions programming model-based** | The custom code action is based on the same programming model that Azure Functions uses. This action deploys a newly created custom function to the same host as the logic app resource in Azure Logic Apps (Standard). The action uses a new trigger binding named **`WorkflowActionTrigger`**. Workflows can natively invoke this action during execution without having to make an HTTP request. |
| **Integrate with existing code** | You can incorporate custom reusable components by refactoring them into custom code functions that directly integrate into your workflows. For example, reusable components migh include legacy components written with the .NET Framework or modern .NET components in your workflows. |
| **Encapsulate complex processes** | The capability to move complex logic out from your workflow simplifies your overall solution and gives you better control over complex components. |
| **Simpler deployments** | Custom code functions become part of your Azure Logic Apps workspace, so they're available across multiple workflows in the same application. This tight coupling reinforces code reusability and central management, which help simplify your deployments and source control. |
| **Multiple .NET version support** | To [create custom code projects](/azure/logic-apps/create-run-custom-code-functions), use one of the following versions of .NET <br><br>- **.NET Framework**: Reuse legacy components in application modernization scenarios such as BizTalk migration. <br><br>- **.NET 8**: Extend your application without creating additional Azure resources by using modern .NET components. |

### Considerations

The following table describes considerations for using custom code functions:

| Consideration | Description |
|---------------|-------------|
| **Testing and quality assurance** | Custom code approaches are more complex, so make sure that you thoroughly unit test these modules. Consider integrating automated test pipelines by using Azure DevOps or GitHub Actions. |
| **Maintainability** | Greater complexity requires more disciplined code reviews, robust logging, and clear documentation to manage ongoing updates and deployments. |
| **Performance and scaling** | Custom code offers more control, but continue to closely monitor for performance bottlenecks. Integrate distributed tracing and Application Insights to track issues across the integrated Azure Logic Apps (Standard) ecosystem. |

## Azure Functions

For organizations that are heavily invested in serverless computing, Azure Functions presents a robust and scalable method to integrate existing business logic directly into your workflows.

### Key attributes

The following table describes the main benefits that Azure Functions provides:

| Benefit | Description |
|---------|-------------|
| **Code reusability and scalability** | Azure Functions lets you reuse business logic written in multiple supported languages along with the benefit from independent scaling. If you already deployed robust business functions, you can [call them from Azure Logic Apps](/azure/logic-apps/call-azure-functions-from-workflows) to avoid redundant development and code duplication. |
| **Operational considerations** | Azure Functions introduces external dependencies. You must manage separate deployments, version controls, and potential integration complexities between Azure Logic Apps and Azure Functions. |
| **Integration and supported languages** | Azure Functions [supports multiple languages and frameworks](/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp). This support might be useful when your business logic requires a specific domain or dependency components. |

### Considerations

The following table describes considerations for using Azure Functions:

| Consideration | Description |
|---------------|-------------|
| **Monitoring and logging** | To understand performance, track errors, and investigate exceptions across both Azure Functions and Azure Logic Apps, use Application Insights and Azure Monitor. |
| **Versioning and DevOps** | Maintain independent versioning for your code in Azure Functions, while you make sure to synchronize Azure Logic Apps workflows with any changes that disrupt the API contracts. |
| **Security** | Secure communication between Azure Logic Apps and Azure Functions by using managed identities or API keys. |

## Comparative analysis

The following side-by-side evaluation can help you determine the best approach for your needs:

| Approach | Ideal use case | Pros | Cons | Deployment considerations |
|----------|----------------|------|------|---------------------------|
| **Inline code** | Simple data transformation, validations, or light tasks | - Minimal overhead <br><br>- Direct integration <br><br>- No external dependencies <br><br>- Cloud and local authoring tools | - Limited debugging for complex logic <br><br>- Versioning tied to workflow | Deployed within a logic app workflow, so the version is always attached to the workflow. |
| **Custom code** | Migrating legacy systems, advanced processing | - Reuses existing code <br><br>- Centralized functions in the same logic app project | - Increased solution complexity <br><br>- Testing required for independent functions | Deployed as part of a Standard logic app, minimizing the surface area of deployment |
| **Azure Functions** | Existing serverless functions, independent scalable business logic | - Independent scaling <br><br>- Reuses serverless patterns <br><br>- Language versatility | - Introduces external dependencies <br><br>- Requires separate deployment and monitoring | - Independent deployment, allowing for independent scale <br><br>- Requires careful version management |

## Conclusion

Azure Logic Apps (Standard) is a versatile platform that bridges the gap between low-code simplicity and the power of pro-code development. You can choose from the following approaches:

- Inline code for simplicity
- Custom code for migration and reusability
- Azure Functions for scalability

By choosing the right approach, you can craft solutions that are robust, maintainable, and future-proof. The key for success is understanding your requirements:

- Balance development speed with long-term maintainability
- Consider performance implications
- Always design with security and testing in mind

As you expand the capabilities of your logic app workflows, remember that the best solutions come from carefully evaluating both the technical and operational landscapes.
