---
title: Compare Extensibility Options for Azure Logic Apps (Standard)
description: Learn how to choose the best extensibility option for the integration solutions that you build with Azure Logic Apps (Standard).

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

Custom code in Azure Logic Apps (Standard) offers a seamless way to incorporate more sophisticated programming logic, including legacy code written in the .NET Framework. This ability makes custom code suitable for modernizing legacy BizTalk solutions or extending workflows. This approach is particularly valuable if you need to implement complex logic, reuse business logic within multiple workflows, or perform operations that exceed what inline code can comfortably handle.

### Key characteristics

- **Based on Azure Functions programming model**: A custom code action is based on the same programming model that Azure Functions uses. This action is a new function that's deployed in the same host as the Azure Logic Apps (Standard) application. It uses a new trigger binding: `WorkflowActionTrigger`. Workflows can invoke that action natively during execution without the need to execute an HTTP request.
- **Integration with existing code**: You can incorporate custom reusable components by refactoring them as custom code functions that directly integrate into your logic apps. These reusable components could be legacy components written in the .NET Framework or modern .NET components in your workflows.
- **Encapsulation of complex processes**: The ability to move complex logic from your workflow simplifies your overall solution and gives you better control of your complex components.
- **Simplified deployments**: Custom code functions become part of your Logic Apps workspace, so they're available across multiple workflows in the target application. This tight coupling reinforces code reusability and central management, to help simplify your deployments and source control.
- **Support for multiple .NET versions**: You can [create custom code projects](/azure/logic-apps/create-run-custom-code-functions) by using one of the following versions of .NET:
  - **.NET Framework**: Reuse legacy components in application modernization scenarios, like BizTalk migration.
  - **.NET 8**: Extend your application without creating additional Azure resources, by using modern .NET components.

### Considerations

- **Testing and quality assurance**: Because custom code approaches are more complex, ensure that these modules are thoroughly unit tested. Consider integrating automated testing pipelines by using Azure DevOps or GitHub Actions.
- **Maintainability**: With greater complexity comes the need for disciplined code reviews, robust logging, and clear documentation to manage ongoing updates and deployments.
- **Performance and scaling**: Although custom code offers more control, closely monitor for performance bottlenecks. Integrate distributed tracing and Application Insights to track issues across the integrated Azure Logic Apps (Standard) ecosystem.

## Azure Functions

For organizations that are heavily invested in serverless computing, Azure Functions presents a robust and scalable method to integrate existing business logic directly into your workflows.

### Key characteristics

- **Code reusability and scalability**: Azure Functions allows you to reuse business logic written in several supported languages, with the added benefit of independent scaling. If you already deployed robust business functions, [calling them from Azure Logic Apps](/azure/logic-apps/call-azure-functions-from-workflows) avoids redundant development and code duplication.
- **Operational considerations**: This approach introduces external dependencies. You must manage separate deployments, version controls, and potential integration complexities between Logic Apps and Azure Functions.
- **Integration and supported languages**: Azure Functions [supports multiple languages and frameworks](/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp). This support might be useful when your business logic requires a specific domain or dependency components.

### Considerations

- **Monitoring and logging**: Use Application Insights and Azure Monitor to understand performance, track errors, and investigate exceptions across both Azure Functions and Logic Apps.
- **Versioning and DevOps**: Maintain independent versioning of your code in Azure Functions while ensuring that Logic Apps workflows are synchronized with any changes that disrupt the API contracts.
- **Security**: Help secure communication between Logic Apps and Azure Functions by using managed identities or API keys.

## Comparative analysis

A clear side-by-side evaluation can help you determine which approach is best for your needs:

| Approach        | Ideal use case                                                 | Pros                                                                            | Cons                                                                         | Deployment considerations                                                                           |
|---------------------|--------------------------------------------------------------------|-------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Inline code**     | Simple data transformation, validations, light tasks               | - Minimal overhead <br> - Direct integration <br> - No external dependencies <br> - Cloud and local authoring tools                 | - Limited debugging for complex logic <br> - Versioning tied to workflow              | - Deployed within a Logic Apps workflow, so the version is always attached to the workflow                         |
| **Custom code**     | Migrating legacy systems, advanced processing                      | - Reuse of existing code <br> - Centralized functions within the same Logic Apps project | - Increased solution complexity <br> - Testing required for independent functions                                | - Deployed as part of the Logic Apps (Standard) application, minimizing the surface area of deployment |
| **Azure Functions** | Existing serverless functions, independent scalable business logic | - Independent scaling <br> - Reuse of serverless patterns <br> - Language versatility         | - Introduces external dependencies <br> - Requires separate deployment and monitoring | - Independent deployment, allowing for independent scale <br> - Requires careful management of versioning        |

## Conclusion

Logic Apps (Standard) is a versatile platform that bridges the gap between low-code simplicity and the power of pro-code development. You can choose among three approaches: inline code for simplicity, custom code for migration and reusability, or Azure Functions for scalability. By choosing the right approach, you can craft solutions that are robust, maintainable, and future-proof.

The key is understanding your requirements: balance development speed with long-term maintainability, consider performance implications, and always design with security and testing in mind. As you expand the capabilities of your logic apps, remember that the best solutions come from a careful evaluation of both the technical and operational landscapes.
